.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

L'Architecture
==============

Vous êtes mon héros! Qui aurait pensé que vous seriez encore là après les trois
premières parties? Vos efforts seront récompensés dans un instant.

Les trois premières parties n'explorent pas trop profondément l'architecture du
framework. Comme Symfony2 se distingue de la nuée des frameworks, nous allons
nous y atteler dès maintenant.

.. index::
   single: Arborescence

L'Arborescence
--------------

L'arborescence d'une :term:`application` Symfony2 est plutôt flexible mais
celui du sandbox reflète la structure typique et recommandée d'une
application Symfony2:

* ``app/``: La configuration de l'application;
* ``src/``: Le code PHP du projet;
* ``vendor/``: Les librairies tierces;
* ``web/``: Le répertoire web racine.

Le répertoire Web
~~~~~~~~~~~~~~~~~

Le répertoire Web racine est la source de tous les fichiers statics et publics
comme les images, les feuilles de styles et les fichiers javascript. C'est aussi
ici que se situeront les :term:`contrôleurs frontaux`::

    // web/app.php
    require_once __DIR__.'/../app/bootstrap.php';
    require_once __DIR__.'/../app/AppKernel.php';

    use Symfony\Component\HttpFoundation\Request;

    $kernel = new AppKernel('prod', false);
    $kernel->handle(Request::createFromGlobals())->send();

Le noyau (kernel) requiert d'abord le fichier ``bootstrap.php`` qui amorce le
framework et l'autoloader (voir ci-bas).

Comme tout contrôleur frontal, ``app.php`` utilise une classe Kernel ``AppKernel``
pour amorcer une application.

.. index::
   single: Kernel
   single: Noyau

Le répertoire Application
~~~~~~~~~~~~~~~~~~~~~~~~~

La classe ``AppKernel`` est le point d'entrée principal de la configuration de
l'application et en tant que tel, il est stocké dans le répertoire ``app/``.

Cette classe doit implémenter trois méthodes:

* ``registerRootDir()``: retourne la configuration du répertoire racine;

* ``registerBundles()``: retourne un tableau de tous les Bundles nécessaires au fonctionnement de l'application (rappelez vous de ``Sensio\HelloBundle\HelloBundle``);

* ``registerContainerConfiguration()``: charge la configuration (sera détaillée ultérieurement);

Jetons un coup d'œil à l'implémentation par défaut de ces méthodes pour une
meilleure compréhension de la flexibilité du framework.

L'autoloading PHP peut être configuré via ``autoload.php``::

    // app/autoload.php
    use Symfony\Component\ClassLoader\UniversalClassLoader;

    $loader = new UniversalClassLoader();
    $loader->registerNamespaces(array(
        'Symfony'                        => __DIR__.'/../vendor/symfony/src',
        'Sensio'                         => __DIR__.'/../src',
        'Doctrine\\Common\\DataFixtures' => __DIR__.'/../vendor/doctrine-data-fixtures/lib',
        'Doctrine\\Common'               => __DIR__.'/../vendor/doctrine-common/lib',
        'Doctrine\\DBAL\\Migrations'     => __DIR__.'/../vendor/doctrine-migrations/lib',
        'Doctrine\\MongoDB'              => __DIR__.'/../vendor/doctrine-mongodb/lib',
        'Doctrine\\ODM\\MongoDB'         => __DIR__.'/../vendor/doctrine-mongodb-odm/lib',
        'Doctrine\\DBAL'                 => __DIR__.'/../vendor/doctrine-dbal/lib',
        'Doctrine'                       => __DIR__.'/../vendor/doctrine/lib',
        'Zend'                           => __DIR__.'/../vendor/zend/library',
    ));
    $loader->registerPrefixes(array(
        'Twig_Extensions_' => __DIR__.'/../vendor/twig-extensions/lib',
        'Twig_'            => __DIR__.'/../vendor/twig/lib',
        'Swift_'           => __DIR__.'/../vendor/swiftmailer/lib/classes',
    ));
    $loader->register();

Le ``UniversalClassLoader`` de Symfony2 est utilisé pour autocharger les
fichiers qui respectent chaque techniques d'interopérabilité des `standards`_
de PHP 5.3 concernant la directive namespace ou la `convention`_ de nommage PEAR
concernant les classes. Comme vous pouvez le voir ici, toutes les dépendances
sont stockées dans le répertoire ``vendor/``, mais ce n'est juste qu'une
convention. Vous pouvez les stocker n'importe où vous souhaitez, généralement,
sur votre serveur ou au sein même de vos projets.

.. index::
   single: Bundles

Le système de Bundles
---------------------

Cette section présente une des plus géniales et puissantes fonctionnalités de
Symfony2, le système de :term:`Bundles`.

Un Bundle est une sorte de plugin chez les autres logiciels. Alors pourquoi
l'a-t-on nommé *Bundle* et non pas *Plugin*? Parce que *tout* est un Bundle dans
Symfony2, des fonctionnalités du noyau du framework au code que vous écrirez
pour votre application. Les Bundles sont les citoyens de première zone pour
Symfony2. Ils vous donnent la flexibilité d'utiliser des fonctionnalités
pré-construites dans des Bundles tiers ou de distribuer vos propres Bundles. Ils
facilitent le piochage et le choix des fonctionnalités à activer pour
votre application et les optimisent de la manière que vous désirez.

Une application est constituée de Bundles comme définis dans la méthode
``registerBundles()`` de la classe ``AppKernel``::

    // app/AppKernel.php
    public function registerBundles()
    {
        $bundles = array(
            new Symfony\Bundle\FrameworkBundle\FrameworkBundle(),
            new Symfony\Bundle\TwigBundle\TwigBundle(),

            // enable third-party bundles
            new Symfony\Bundle\ZendBundle\ZendBundle(),
            new Symfony\Bundle\SwiftmailerBundle\SwiftmailerBundle(),
            new Symfony\Bundle\DoctrineBundle\DoctrineBundle(),
            //new Symfony\Bundle\DoctrineMigrationsBundle\DoctrineMigrationsBundle(),
            //new Symfony\Bundle\DoctrineMongoDBBundle\DoctrineMongoDBBundle(),

            // register your bundles
            new Sensio\HelloBundle\HelloBundle(),
        );

        if ($this->isDebug()) {
            $bundles[] = new Symfony\Bundle\WebProfilerBundle\WebProfilerBundle();
        }

        return $bundles;
    }

Mis à part le ``HelloBundle`` que nous avons déjà traité dans ce tutoriel,
remarquez que le noyau active aussi ``FrameworkBundle``, ``DoctrineBundle``,
``SwiftmailerBundle`` et ``ZendBundle``. Ils sont tous fournis avec le noyau
du framework.

Chaque Bundle peut être personnalisé via des fichiers de configuration écrits en
YAML, XML ou PHP. Regardons la configuration par défaut:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        framework:
            charset:       UTF-8
            error_handler: null
            csrf_protection:
                enabled: true
                secret: xxxxxxxxxx
            router:        { resource: "%kernel.root_dir%/config/routing.yml" }
            validation:    { enabled: true, annotations: true }
            templating:    { engines: ['twig'] } #assets_version: SomeVersionScheme
            session:
                default_locale: en
                lifetime:       3600
                auto_start:     true

        # Twig Configuration
        twig:
            debug:            %kernel.debug%
            strict_variables: %kernel.debug%

        ## Doctrine Configuration
        #doctrine:
        #   dbal:
        #       dbname:   xxxxxxxx
        #       user:     xxxxxxxx
        #       password: ~
        #       logging:  %kernel.debug%
        #   orm:
        #       auto_generate_proxy_classes: %kernel.debug%
        #       mappings:
        #           HelloBundle: ~

        ## Swiftmailer Configuration
        #swiftmailer:
        #    transport:  smtp
        #    encryption: ssl
        #    auth_mode:  login
        #    host:       smtp.gmail.com
        #    username:   xxxxxxxx
        #    password:   xxxxxxxx

    .. code-block:: xml

        <!-- app/config/config.xml -->
        <framework:config charset="UTF-8" error-handler="null" cache-warmer="false">
            <framework:router resource="%kernel.root_dir%/config/routing.xml" cache-warmer="true" />
            <framework:validation enabled="true" annotations="true" />
            <framework:session default-locale="en" lifetime="3600" auto-start="true" />
            <framework:templating assets-version="SomeVersionScheme" cache-warmer="true">
                <framework:engine id="twig" />
            </framework:templating>
            <framework:csrf-protection enabled="true" secret="xxxxxxxxxx" />
        </framework:config>

        <!-- Twig Configuration -->
        <twig:config debug="%kernel.debug%" strict-variables="%kernel.debug%" cache-warmer="true" />

        <!-- Doctrine Configuration -->
        <!--
        <doctrine:config>
            <doctrine:dbal dbname="xxxxxxxx" user="xxxxxxxx" password="" logging="%kernel.debug%" />
            <doctrine:orm auto-generate-proxy-classes="%kernel.debug%">
                <doctrine:mappings>
                    <doctrine:mapping name="HelloBundle" />
                </doctrine:mappings>
            </doctrine:orm>
        </doctrine:config>
        -->

        <!-- Swiftmailer Configuration -->
        <!--
        <swiftmailer:config
            transport="smtp"
            encryption="ssl"
            auth-mode="login"
            host="smtp.gmail.com"
            username="xxxxxxxx"
            password="xxxxxxxx" />
        -->

    .. code-block:: php

        // app/config/config.php
        $container->loadFromExtension('framework', array(
            'charset'         => 'UTF-8',
            'error_handler'   => null,
            'csrf-protection' => array('enabled' => true, 'secret' => 'xxxxxxxxxx'),
            'router'          => array('resource' => '%kernel.root_dir%/config/routing.php'),
            'validation'      => array('enabled' => true, 'annotations' => true),
            'templating'      => array(
                'engines' => array('twig'),
                #'assets_version' => "SomeVersionScheme",
            ),
            'session' => array(
                'default_locale' => "en",
                'lifetime'       => "3600",
                'auto_start'     => true,
            ),
        ));

        // Twig Configuration
        $container->loadFromExtension('twig', array(
            'debug'            => '%kernel.debug%',
            'strict_variables' => '%kernel.debug%',
        ));

        // Doctrine Configuration
        /*
        $container->loadFromExtension('doctrine', array(
            'dbal' => array(
                'dbname'   => 'xxxxxxxx',
                'user'     => 'xxxxxxxx',
                'password' => '',
                'logging'  => '%kernel.debug%',
            ),
            'orm' => array(
                'auto_generate_proxy_classes' => '%kernel.debug%',
                'mappings' => array('HelloBundle' => array()),
            ),
        ));
        */

        // Swiftmailer Configuration
        /*
        $container->loadFromExtension('swiftmailer', array(
            'transport'  => "smtp",
            'encryption' => "ssl",
            'auth_mode'  => "login",
            'host'       => "smtp.gmail.com",
            'username'   => "xxxxxxxx",
            'password'   => "xxxxxxxx",
        ));
        */

Chaque entrée encadrée définit la configuration d'un Bundle.

Chaque :term:`environnement` peut surcharger la configuration par défaut en
apportant un fichier spécifique de configuration:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config_dev.yml
        imports:
            - { resource: config.yml }

        framework:
            router:   { resource: "%kernel.root_dir%/config/routing_dev.yml" }
            profiler: { only_exceptions: false }

        web_profiler:
            toolbar: true
            intercept_redirects: true

        zend:
            logger:
                priority: debug
                path:     %kernel.logs_dir%/%kernel.environment%.log

    .. code-block:: xml

        <!-- app/config/config_dev.xml -->
        <imports>
            <import resource="config.xml" />
        </imports>

        <framework:config>
            <framework:router resource="%kernel.root_dir%/config/routing_dev.xml" />
            <framework:profiler only-exceptions="false" />
        </framework:config>

        <webprofiler:config
            toolbar="true"
            intercept-redirects="true"
        />

        <zend:config>
            <zend:logger priority="info" path="%kernel.logs_dir%/%kernel.environment%.log" />
        </zend:config>

    .. code-block:: php

        // app/config/config_dev.php
        $loader->import('config.php');

        $container->loadFromExtension('framework', array(
            'router'   => array('resource' => '%kernel.root_dir%/config/routing_dev.php'),
            'profiler' => array('only-exceptions' => false),
        ));

        $container->loadFromExtension('web_profiler', array(
            'toolbar' => true,
            'intercept-redirects' => true,
        ));

        $container->loadFromExtension('zend', array(
            'logger' => array(
                'priority' => 'info',
                'path'     => '%kernel.logs_dir%/%kernel.environment%.log',
            ),
        ));

Vous comprenez maintenant pourquoi Symfony2 est si flexible? Partagez vos
Bundles entre applications, stockez-les localement ou globalement, c'est vous
qui décidez.

.. index::
   single: Vendors
   single: Librairies tierces

Utilisation de solutions externes (Vendors)
-------------------------------------------

Il y a de fortes probabilités que votre application dépende de bibliothèques
tierces. Celles-ci doivent être stockées dans le répertoire ``src/vendor/``. Ce
répertoire contient déjà les librairies de Symfony2, la librairie SwiftMailer,
l'ORM Doctrine, le système de template Twig et une sélection des classes du
Framework Zend.

.. index::
   single: Configuration Cache
   single: Logs

Cache et Logs
-------------

Symfony2 est probablement l'un des plus rapides framework full-stack existant.
Mais comment peut-il être si rapide s'il analyse et interprète des dizaines de
fichiers YAML et XML pour chaque demande? Ceci est partiellement dû à son
système de cache. La configuration de l'application est uniquement analysée
lors de la première demande, puis compilé en un pur code PHP dans le répertoire
``cache/`` de l'application. Dans l'environnement de développement, Symfony2 est
assez intelligent pour vider le cache lorsque vous modifiez un fichier. Mais
dans l'environnement de production, il est de votre ressort d'effacer le
cache lorsque vous mettez à jour votre code ou modifier sa configuration.

Quand vous développez une application Web, de nombreuses choses peuvent faillir
de nombreuses façons. Le fichier log dans le répertoire ``logs/`` de votre
application vous dira tout concernant les requêtes et vous aidera à résoudre
votre souci rapidement.

.. index::
   single: CLI
   single: Ligne de commande

L'Interface en Ligne de Commande (CLI)
--------------------------------------

Chaque application est fournie avec une interface utilitaire en ligne de
commandes (``console``) qui vous aidera à maintenir votre application. Il met à
votre disposition des commandes qui accélèrent votre productivité en
automatisant les tâches fastidieuses et répétitives.

Lancez-le sans aucun argument pour en apprendre plus sur ses possibilités:

.. code-block:: bash

    $ php app/console

L'option ``--help`` vous dévoilera le synopsis et les options d'une commande donnée:

.. code-block:: bash

    $ php app/console router:debug --help

Le mot de la fin
----------------

Vous pouvez trouver ça extravagant mais après avoir lu cette partie, vous
devriez être suffisament à l'aise pour faire vos premières griffes et laisser
Symfony2 travailler pour vous. Tout est fait dans Symfony2 pour que vous traciez
votre voie. Alors, n'hésitez pas à renommer et déplacer des répertoires comme
bon vous semble.

C'en est tout pour cette découverte éclair. De l'essai à l'envoi d'e-mails, vous
avez encore besoin d'en apprendre beaucoup pour devenir un maître Symfony2. Prêt à
plonger dans ces thèmes maintenant? Ne cherchez plus : consultez le `Manuel`_ et
approfondissez vos connaissances dans les domaines qui vous attirent.

.. _standards:    http://groups.google.com/group/php-standards/web/psr-0-final-proposal
.. _convention:   http://pear.php.net/
.. _Manuel:       http://www.symfony-reloaded.org/learn
