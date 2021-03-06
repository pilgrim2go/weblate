Upgrading Weblate
=================

Upgrading
---------

.. _generic-upgrade-instructions:

Generic upgrade instructions
++++++++++++++++++++++++++++

Before upgrading, please check current :ref:`requirements` as they might have
changed. Once all requirements are installed or updated, please adjust your
:file:`settings.py` to match changes in the configuration (consult
:file:`settings_example.py` for correct values).

To upgrade database structure, you should run:

.. code-block:: sh

    ./manage.py migrate

To collect new static files, run:

.. code-block:: sh

    ./manage.py collectstatic --noinput

To upgrade default set of privileges definitions (optional), run:

.. code-block:: sh

    ./manage.py setupgroups

To upgrade default set of language definitions (optional), run:

.. code-block:: sh

    ./manage.py setuplang

If you are running version from Git, you should also regenerate locale files
every time you are upgrading. You can do this by invoking:

.. code-block:: sh

    ./manage.py compilemessages

.. versionchanged:: 1.2

    Since version 1.2 the migration is done using South module, to upgrade to 1.2,
    please see :ref:`version-specific-instructions`.

.. versionchanged:: 1.9

    Since version 1.9, Weblate also supports Django 1.7 migrations, please check
    :ref:`django-17` for more information.

.. versionchanged:: 2.3

    Since version 2.3, Weblate supports only Django native migrations, South is
    no longer supported, please check :ref:`django-17` for more information.

.. versionchanged:: 2.11

    Since version 2.11, there is reduced support for migrating from
    older not released versions. In case you hit problem in this, please 
    upgrade first to closest released version and then continue in 
    upgrading to latest one.

.. versionchanged:: 2.12

    Since version 2.12 upgrade is not supported for versions prior to 2.2. In
    case you are upgrading from such old version, please upgrade to 2.2 first
    and then continue in upgrading to current release.

.. _version-specific-instructions:

Version specific instructions
+++++++++++++++++++++++++++++

Upgrade from 0.5 to 0.6
~~~~~~~~~~~~~~~~~~~~~~~

On upgrade to version 0.6 you should run :samp:`./manage.py syncdb` and
:samp:`./manage.py setupgroups --move` to setup access control as described
in installation section.

Upgrade from 0.6 to 0.7
~~~~~~~~~~~~~~~~~~~~~~~

On upgrade to version 0.7 you should run :samp:`./manage.py syncdb` to
setup new tables and :samp:`./manage.py rebuild_index` to build index for
fulltext search.

Upgrade from 0.7 to 0.8
~~~~~~~~~~~~~~~~~~~~~~~

On upgrade to version 0.8 you should run :samp:`./manage.py syncdb` to setup
new tables, :samp:`./manage.py setupgroups` to update privileges setup and
:samp:`./manage.py rebuild_index` to rebuild index for fulltext search.

Upgrade from 0.8 to 0.9
~~~~~~~~~~~~~~~~~~~~~~~

On upgrade to version 0.9 file structure has changed. You need to move
:file:`repos` and :file:`whoosh-index` to :file:`weblate` folder. Also running
:samp:`./manage.py syncdb`, :samp:`./manage.py setupgroups` and
:samp:`./manage.py setuplang` is recommended to get latest updates of
privileges and language definitions.

Upgrade from 0.9 to 1.0
~~~~~~~~~~~~~~~~~~~~~~~

On upgrade to version 1.0 one field has been added to database, you need to
invoke following SQL command to adjust it:

.. code-block:: sql

    ALTER TABLE `trans_subproject` ADD `template` VARCHAR(200);

Upgrade from 1.0 (1.1) to 1.2
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On upgrade to version 1.2, the migration procedure has changed. It now uses
South for migrating database. To switch to this new migration schema, you need
to run following commands:

.. code-block:: sh

    ./manage.py syncdb
    ./manage.py migrate trans 0001 --fake
    ./manage.py migrate accounts 0001 --fake
    ./manage.py migrate lang 0001 --fake

Also please note that there are several new requirements and version 0.8 of
django-registration is now being required, see :ref:`requirements` for more
details.

Once you have done this, you can use :ref:`generic-upgrade-instructions`.

Upgrade from 1.2 to 1.3
~~~~~~~~~~~~~~~~~~~~~~~

Since 1.3, :file:`settings.py` is not shipped with Weblate, but only example
settings as :file:`settings_example.py` it is recommended to use it as new base
for your setup.

Upgrade from 1.4 to 1.5
~~~~~~~~~~~~~~~~~~~~~~~

Several internal modules and paths have been renamed and changed, please adjust
your :file:`settings.py` to match that (consult :file:`settings_example.py` for
correct values).

* Many modules lost their ``weblate.`` prefix.
* Checks were moved to submodules.
* Locales were moved to top level directory.

The migration of database structure to 1.5 might take quite long, it is
recommended to put your site offline, while the migration is going on.

.. note::

    If you have update in same directory, stale :file:`*.pyc` files might be
    left around and cause various import errors. To recover from this, delete
    all of them in Weblate's directory, for example by
    ``find . -name '*.pyc' -delete``.

Upgrade from 1.6 to 1.7
~~~~~~~~~~~~~~~~~~~~~~~

The migration of database structure to 1.7 might take quite long, it is
recommended to put your site offline, while the migration is going on.

If you are translating monolingual files, it is recommended to rerun quality
checks as they might have been wrongly linked to units in previous versions.

Upgrade from 1.7 to 1.8
~~~~~~~~~~~~~~~~~~~~~~~

The migration of database structure to 1.8 might take quite long, it is
recommended to put your site offline, while the migration is going on.

Authentication setup has been changed and some internal modules have changed
name, please adjust your :file:`settings.py` to match that (consult
:file:`settings_example.py` for correct values).

Also please note that there are several new requirements, see
:ref:`requirements` for more details.

Upgrade from 1.8 to 1.9
~~~~~~~~~~~~~~~~~~~~~~~

Several internal modules and paths have been renamed and changed, please adjust
your :file:`settings.py` to match that (consult :file:`settings_example.py` for
correct values).

.. seealso::

    If you are upgrading to Django 1.7 in same step, please consult
    :ref:`django-17`.

Upgrade from 1.9 to 2.0
~~~~~~~~~~~~~~~~~~~~~~~

Several internal modules and paths have been renamed and changed, please adjust
your :file:`settings.py` to match that (consult :file:`settings_example.py` for
correct values).

This upgrade also requires you to upgrade python-social-auth from 0.1.x to
0.2.x series, what will most likely to need to fake one of their migrations
(see :doc:`Upgrading PSA with South <psa:configuration/django>` for more information):

.. code-block:: sh

    ./manage.py migrate --fake default

.. seealso::

    If you are upgrading to Django 1.7 in same step, please consult
    :ref:`django-17`.

Upgrade from 2.0 to 2.1
~~~~~~~~~~~~~~~~~~~~~~~

The filesystem paths configuration has changed, the :setting:`GIT_ROOT` and
:setting:`WHOOSH_INDEX` are gone and now all data resides in
:setting:`DATA_DIR`. The existing data should be automatically migrated by
supplied migration, but in case of non standard setup, you might need to move
these manually.

.. seealso::

    If you are upgrading to Django 1.7 in same step, please consult
    :ref:`django-17`.

Upgrade from 2.1 to 2.2
~~~~~~~~~~~~~~~~~~~~~~~

Weblate now supports fulltext search on additional fields. In order to make it
work on existing data you need to update fulltext index by:

.. code-block:: sh

    ./manage.py rebuild_index --clean --all

If you have some monolingual translations, Weblate now allows to edit template
(source) strings as well. To see them, you need to reload translations, what
will either happen automatically on next repository update or you can force it
manually:

.. code-block:: sh

    ./manage.py loadpo --all

.. seealso::

    If you are upgrading to Django 1.7 in same step, please consult
    :ref:`django-17`.

Upgrade from 2.2 to 2.3
~~~~~~~~~~~~~~~~~~~~~~~

If you have not yet performed upgrade to Django 1.7 and newer, first upgrade to
2.2 following instructions above. Weblate 2.3 no longer supports migration from
Django 1.6.

If you were using Weblate 2.2 with Django 1.6, you will now need to fake some
migrations:

.. code-block:: sh

    ./manage.py migrate --fake accounts 0004_auto_20150108_1424
    ./manage.py migrate --fake lang 0001_initial
    ./manage.py migrate --fake trans 0018_auto_20150213_1447

Previous Weblate releases contained bug which made some monolingual
translations behave inconsistently for fuzzy and not translated strings, if you
have such, it is recommended to run:

.. code-block:: sh

    ./manage.py fixup_flags --all

.. seealso:: :ref:`generic-upgrade-instructions`

Upgrade from 2.3 to 2.4
~~~~~~~~~~~~~~~~~~~~~~~

Handling of static content has been rewritten, please adjust configuration of
your webserver accordingly (see :ref:`static-files` for more details). Most
importantly:

* ``/media/`` path is no longer used
* ``/static/`` path now holds both admin and Weblate static files

There is now also additional dependency - ``django_compressor``, please install
it prior to upgrading.

.. seealso:: :ref:`generic-upgrade-instructions`

Upgrade from 2.4 to 2.5
~~~~~~~~~~~~~~~~~~~~~~~

The fulltext index has been changed, so unless you rebuild it, the fulltext
search will not work. To rebuild it, execute:

.. code-block:: sh

    ./manage.py rebuild_index --clean --all

.. seealso:: :ref:`generic-upgrade-instructions`

Upgrade from 2.5 to 2.6
~~~~~~~~~~~~~~~~~~~~~~~

Follow generic upgrade instructions, there is no special change.

Notable configuration or dependencies changes:

* new dependecy on Django REST Framework, see :ref:`requirements`
* example configuration now configures Django REST Framework, please adjust
  your settings accordingly
* the USE_TZ settings is now enabled by default

.. note::

    Weblate now much more relies on correct site name in the database, please 
    see :ref:`production-site` for instructions how to set it up.

.. seealso:: :ref:`generic-upgrade-instructions`

Upgrade from 2.6 to 2.7
~~~~~~~~~~~~~~~~~~~~~~~

Follow generic upgrade instructions, there is no special change.

Notable configuration or dependencies changes:

* new optional dependency on python-bidi, see :ref:`requirements`
* Google Web Translation was removed, remove it from your configuration

.. seealso:: :ref:`generic-upgrade-instructions`

Upgrade from 2.7 to 2.8
~~~~~~~~~~~~~~~~~~~~~~~

Follow generic upgrade instructions, there is no special change.

Notable configuration or dependencies changes:

* new dependency on defusedxml, see :ref:`requirements`
* there is new quality check: :ref:`check-xml-invalid`

.. seealso:: :ref:`generic-upgrade-instructions`

Upgrade from 2.8 to 2.9
~~~~~~~~~~~~~~~~~~~~~~~

Please follow generic upgrade instructions, the only notable 
change is addition of media storage to :setting:`DATA_DIR`.

.. seealso:: :ref:`generic-upgrade-instructions`

Upgrade from 2.9 to 2.10
~~~~~~~~~~~~~~~~~~~~~~~~

Follow generic upgrade instructions, there is no special change.

Notable configuration or dependencies changes:

* The ``INSTALLED_APPS`` now should include ``weblate.utils``.
* There is new check in default set (``SamePluralsCheck``).
* There is change in ``SOCIAL_AUTH_PIPELINE`` default settings.
* You might want to enable optional :ref:`git-exporter`.
* There is new ``RemoveControlChars`` in default :setting:`AUTOFIX_LIST`.
* If you are using Microsoft Translator, please replace
  :ref:`ms-translate` with :ref:`ms-cognitive-translate`,
  Microsoft has changed authentication scheme.

.. seealso:: :ref:`generic-upgrade-instructions`

Upgrade from 2.10 to 2.11
~~~~~~~~~~~~~~~~~~~~~~~~~

Follow generic upgrade instructions, there is no special change.

Notable configuration or dependencies changes:

* There is new recommended value for ``SOCIAL_AUTH_SLUGIFY_FUNCTION``.
* There is change in ``MIDDLEWARE_CLASSES`` setting.
* The ``python-social-auth`` module has been deprecated upstream, Weblate
  now uses ``social-auth-core`` and ``social-auth-app-django`` instead. You also
  have to adjust :file:`settings.py` as several modules have been moved from
  ``social`` to either ``social_core`` or ``social_django``. Please consult
  :file:`settings_example.py` for correct values.

.. warning::

    If you were using python-social-auth 0.2.19 or older with Weblate 2.10, you
    should first upgrade Weblate 2.10 to python-social-auth 0.2.21 and then
    perform upgrade to Weblate 2.11. Otherwise you end up with non applicable
    database migrations.

    See `Migrating from python-social-auth to split social <https://github.com/omab/python-social-auth/blob/master/MIGRATING_TO_SOCIAL.md#migrations>`_
    for more information.


.. seealso:: :ref:`generic-upgrade-instructions`

Upgrade from 2.11 to 2.12
~~~~~~~~~~~~~~~~~~~~~~~~~

Follow generic upgrade instructions, there is no special change.

Notable configuration or dependencies changes:

* The database migration will take quite long on this update as all
  translation units stored in database have to be updated. Expect about 1 hour
  of migration for 500000 translation units (depends on hardware and database).
* There is new dependency on ``django-appconf`` and ``siphashc3``.
* The setting for ``UNAUTHENTICATED_USER`` for ``REST_FRAMEWORK`` has been
  changed to properly handle anonymous user permissions in REST API.
* The ``INSTALLED_APPS`` now should include ``weblate.screenshots``.
* There is new optional dependency on tesserocr, see :ref:`requirements`.

.. seealso:: :ref:`generic-upgrade-instructions`

Upgrade from 2.12 to 2.13
~~~~~~~~~~~~~~~~~~~~~~~~~

Follow generic upgrade instructions, there is no special change.

Notable configuration or dependencies changes:

* There is new quality check: :ref:`check-translated`.
* The ``INSTALLED_APPS`` now should include ``weblate.permissions``.
* The per project ALCs are now implemented using Group ACL, you might need to
  adjust your setup if you were using Group ACLs before, see :ref:`groupacl`
  for more information about the setup.
* There are several new permissions which should be assigned to default groups,
  you should run ``./manage.py setupgroups`` to update them. Alternatively you
  might want to add following permissions where applicable (see :ref:`extra-privs`
  for their default setup):

  * Can access VCS repository
  * Can access project

.. note::

    If you have update in same directory, stale :file:`*.pyc` files might be
    left around and cause various import errors. To recover from this, delete
    all of them in Weblate's directory, for example by
    ``find . -name '*.pyc' -delete``.

.. seealso:: :ref:`generic-upgrade-instructions`

Upgrade from 2.13 to 2.14
~~~~~~~~~~~~~~~~~~~~~~~~~

Follow generic upgrade instructions, there is no special change.

Notable configuration or dependencies changes:

* There is new middleware ``weblate.middleware.SecurityMiddleware`` in the
  default configuration, see :ref:`csp` for more details.
* Weblate now uses Django password validation, it's controlled by
  ``AUTH_PASSWORD_VALIDATORS`` setting.
* Weblate now customizes disconnect pipeline for Python Social Auth,
  the ``SOCIAL_AUTH_DISCONNECT_PIPELINE`` setting is now needed.
* There is change in ``SOCIAL_AUTH_PIPELINE`` default settings.
* All pending email verifications will be invalid due to validation change.
* The authentication attempts are now rate limited, see :ref:`rate-limit` for
  more details.

.. seealso:: :ref:`generic-upgrade-instructions`

.. _django-17:

Upgrading to Django 1.7
-----------------------

.. versionchanged:: 2.3

    This migration is supported only in Weblate 2.2, in case you are 
    upgrading from some older version, you will have to do intermediate update
    to 2.2.

Django 1.7 has a new feature to handle database schema upgrade called
"migrations" which is incompatible with South (used before by Weblate).

Before migrating to Django 1.7, you first need to apply all migrations from
South. If you already have upgraded Django to 1.7, you can do this using
virtualenv and :file:`examples/migrate-south` script:

.. code-block:: sh

    examples/migrate-south --settings weblate.settings

Once you have done that, you can run Django migrations and work as usual. For
the initial setup, you might need to fake some of the migrations though:

.. code-block:: sh

    ./manage.py migrate --fake-initial

.. _pootle-migration:

Migrating from Pootle
---------------------

As Weblate was originally written as replacement from Pootle, it is supported
to migrate user accounts from Pootle. All you need to do is to copy
``auth_user`` table from Pootle, user profiles will be automatically created
for users as they log in and they will be asked to update their settings.
Alternatively you can use :djadmin:`importusers` to import dumped user
credentials.
