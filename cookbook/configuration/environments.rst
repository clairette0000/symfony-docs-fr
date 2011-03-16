.. index::
   single: Environments;

Comment maîtriser et créer de nouveaux environnements ?
=======================================================

Chaque application est une combinaison de code et de tout un lot de configuration
qui dicte le comportement que le code doit avoir. La configuration peut définir 
le rôle de la base de données et son utilisation, ce qui doit être mis ou pas en
cache ou encore ce qui doit être mis dans les fichiers de logs.
Dans Symfony2, le concept d'"environnement" est qu'une même application de base
peut être démarrée avec différentes configurations. Par exemple, l'environnement
"dev" pourrait utiliser une configuration qui rend le développement rapide et 
facile, tandis que l'environnement "prod" pourrait utiliser un jeu de configuration
qui privilégie les temps de réponse.

.. index::
   single: Environments; Configuration files

Différents environnements, différents fichiers de configuration
---------------------------------------------------------------

Une application Symfony2 typique possède trois environnements: ``dev``,
``prod``, and ``test``. Comme nous l'avons dit plus haut, chaque "environnement"
représente simplement une manière d'éxécuter un même code de base, mais avec une 
configuration différente. Ca n'est donc pas une surprise, chque environnement ne 
charge que ses propres fichiers de configuration. Si vous utilisez le format de 
configuration YAML, les fichiers suivants sont utilisés :

 * pour l'environnement de ``dev`` : ``app/config/config_dev.yml``
 * pour l'environnement de ``prod`` : ``app/config/config_prod.yml``
 * pour l'environnement de ``test`` : ``app/config/config_test.yml``

Cela fonctionne grâce à un simple standard utilisé par défaut dans la classe 
``AppKernel``:

.. code-block:: php

    // app/AppKernel.php
    // ...
    
    class AppKernel extends Kernel
    {
        // ...

        public function registerContainerConfiguration(LoaderInterface $loader)
        {
            $loader->load(__DIR__.'/config/config_'.$this->getEnvironment().'.yml');
        }
    }

Comme vous pouvez le voir, une fois chargé, Symfony2 utilise l'environnement afin 
de déterminer quel fichier de configuration charger. Cela permet de définir plusieurs
environnements de manière simple, puissante et transparente.

Bien sûr, en réalité, chaque environnement diffère des autres sur au moins un aspect.
Généralement, la majorité de la configuration sera commune à tous les environnements.
Ouvrez maintenant le fichier de configuration de "dev" et voyez comme l'import se 
fait facilement:

.. configuration-block::

    .. code-block:: yaml

        imports:
            - { resource: config.yml }

        # ...

    .. code-block:: xml

        <imports>
            <import resource="config.xml" />
        </imports>

        <!-- ... -->

    .. code-block:: php

        $loader->import('config.php');

        // ...

Pour partager une configuration commune, les fichiers de configuration de chaque 
environnement commencent importer très simplement un fichier de configuration 
global (``config.yml``).

Dans la suite du fichier, il est possible de s'écarter de la configuration par 
défaut en surchargeant spécifiquement chaque paramètre. Par exemple, par défaut, 
la barre d'outil ``web_profiler`` est désactivée. Pourtant, dans l'environnement 
de ``dev``, la barre d'outil est activée en changeant la valeur par défaut, dans 
le fichier de configuration:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config_dev.yml
        imports:
            - { resource: config.yml }

        web_profiler:
            toolbar: true
            # ...

    .. code-block:: xml

        <!-- app/config/config_dev.xml -->
        <imports>
            <import resource="config.xml" />
        </imports>

        <webprofiler:config
            toolbar="true"
            # ...
        />

    .. code-block:: php

        // app/config/config_dev.php
        $loader->import('config.php');

        $container->loadFromExtension('web_profiler', array(
            'toolbar' => true,
            // ..
        ));

.. index::
   single: Environments; Executing different environments

Executing an Application in Different Environments
--------------------------------------------------

To execute the application in each environment, load up the application using
either the ``app.php`` (for the ``prod`` environment) or the ``app_dev.php``
(for the ``dev`` environment) front controller:

.. code-block:: text

    http://localhost/app.php      -> *prod* environment
    http://localhost/app_dev.php  -> *dev* environment

.. note::

   The given URLs assume that your web server is configured to use the ``web/``
   directory of the application as its root. Read more in
   :doc:`Installing Symfony2</book/installation>`.

If you open up one of these files, you'll quickly see that the environment
used by each is explicitly set:

.. code-block:: php
   :linenos:

    <?php

    require_once __DIR__.'/../app/bootstrap_cache.php';
    require_once __DIR__.'/../app/AppCache.php';

    use Symfony\Component\HttpFoundation\Request;

    $kernel = new AppCache(new AppKernel('prod', false));
    $kernel->handle(Request::createFromGlobals())->send();

As you can see, the ``prod`` key specifies that this environment will run
in the ``prod`` environment. A Symfony2 application can be executed in any
environment by using this code and changing the environment string.

.. note::

   The ``test`` environment is used when writing functional tests and is
   not accessible in the browser directly via a front controller. In other
   words, unlike the other environments, there is no ``app_test.php`` front
   controller file.

.. index::
   single: Configuration; Debug mode

.. sidebar:: *Debug* Mode

    Important, but unrelated to the topic of *environments* is the ``false``
    key on line 8 of the front controller above. This specifies whether or
    not the application should run in "debug mode". Regardless of the environment,
    a Symfony2 application can be run with debug mode set to ``true`` or
    ``false``. This affects many things in the application, such as whether
    or not errors should be displayed or if cache files are dynamically rebuilt
    on each request. Though not a requirement, debug mode is generally set
    to ``true`` for the ``dev`` and ``test`` environments and ``false`` for
    the ``prod`` environment.

    Internally, the value of the debug mode becomes the ``kernel.debug``
    parameter used inside the :doc:`service container </book/service_container>`.
    If you look inside the application configuration file, you'll see the
    parameter used, for example, to turn logging on or off when using the
    Doctrine DBAL:

    .. configuration-block::

        .. code-block:: yaml

            doctrine:
               dbal:
                   logging:  %kernel.debug%
                   # ...

        .. code-block:: xml

            <doctrine:dbal logging="%kernel.debug%" ... />

        .. code-block:: php

            $container->loadFromExtension('doctrine', array(
                'dbal' => array(
                    'logging'  => '%kernel.debug%',
                    // ...
                ),
                // ...
            ));

.. index::
   single: Environments; Creating a new environment

Creating a New Environment
--------------------------

By default, a Symfony2 application has three environments that handle most
cases. Of course, since an environment is nothing more than a string that
corresponds to a set of configuration, creating a new environment is quite
easy.

Suppose, for example, that before deployment, you need to benchmark your
application. One way to benchmark the application is to use near-production
settings, but with Symfony2's ``web_profiler`` enabled. This allows Symfony2
to record information about your application while benchmarking.

The best way to accomplish this is via a new environment called, for example,
``benchmark``. Start by creating a new configuration file:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config_benchmark.yml

        imports:
            - { resource: config_prod.yml }

        framework:
            profiler: { only_exceptions: false }

    .. code-block:: xml

        <!-- app/config/config_benchmark.xml -->

        <imports>
            <import resource="config_prod.xml" />
        </imports>

        <framework:config>
            <framework:profiler only-exceptions="false" />
        </framework:config>

    .. code-block:: php

        // app/config/config_benchmark.php
        
        $loader->import('config_prod.php')

        $container->loadFromExtension('framework', array(
            'profiler' => array('only-exceptions' => false),
        ));

And with this simple addition, the application now supports a new environment
called ``benchmark``.

This new configuration file imports the configuration from the ``prod`` environment
and modifies it. This guarantees that the new environment is identical to
the ``prod`` environment, except for any changes explicitly made here.

Because you'll want this environment to be accessible via a browser, you
should also create a front controller for it. Copy the ``web/app.php`` file
to ``web/app_benchmark.php`` and edit the environment to be ``benchmark``:

.. code-block:: php

    <?php

    require_once __DIR__.'/../app/bootstrap.php';
    require_once __DIR__.'/../app/AppKernel.php';

    use Symfony\Component\HttpFoundation\Request;

    $kernel = new AppKernel('benchmark', false);
    $kernel->handle(Request::createFromGlobals())->send();

The new environment is now accessible via::

    http://localhost/app_benchmark.php

.. note::

   Some environments, like the ``dev`` environment, are never meant to be
   accessed on any deployed server by the general public. This is because
   certain environments, for debugging purposes, may give too much information
   about the application or underlying infrastructure. To be sure these environments
   aren't accessible, the front controller is usually protected from external
   IP addresses via the following code at the top of the controller:
   
    .. code-block:: php

        if (!in_array(@$_SERVER['REMOTE_ADDR'], array('127.0.0.1', '::1'))) {
            die('You are not allowed to access this file. Check '.basename(__FILE__).' for more information.');
        }

.. index::
   single: Environments; Cache directory

Environments and the Cache Directory
------------------------------------

Symfony2 takes advantage of caching in many ways: the application configuration,
routing configuration, Twig templates and more are cached to PHP objects
stored in files on the filesystem.

By default, these cached files are largely stored in the ``app/cache`` directory.
However, each environment caches its own set of files:

.. code-block:: text

    app/cache/dev   - cache directory for the *dev* environment
    app/cache/prod  - cache directory for the *prod* environment

Sometimes, when debugging, it may be helpful to inspect a cached file to
understand how something is working. When doing so, remember to look in
the directory of the environment you're using (most commonly ``dev`` while
developing and debugging). While it can vary, the ``app/cache/dev`` directory
includes the following:

* ``appDevDebugProjectContainer.php`` - the cached "service container" that
  represents the cached application configuration;

* ``appdevUrlGenerator.php`` - the PHP class generated from the routing
  configuration and used when generating URLs;

* ``appdevUrlMatcher.php`` - the PHP class used for route matching - look
  here to see the compiled regular expression logic used to match incoming
  URLs to different routes;

* ``twig/`` - this directory contains all the cached Twig templates.
