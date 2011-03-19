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

Exécuter une application dans des environnements différents
-----------------------------------------------------------

Pour éxécuter une application dans un environnement, chargez l'application en 
utilisant soit le contrôleur ``app.php`` (pour l'environnement de ``prod``), 
soit le contrôleur ``app_dev.php`` (pour l'environnement de ``dev`` environment):

.. code-block:: text

    http://localhost/app.php      -> environnement de *prod*
    http://localhost/app_dev.php  -> environnement de *dev*

.. note::

   Ces URLs supposent que votre serveur web est configuré pour utiliser le 
   répertoire racine ``web/``. Pour en savoir plus, lisez
   :doc:`Installing Symfony2</book/installation>`.

Si vous ouvrez l'un de ces fichiers, vous verrez très rapidement que l'environnement
utilisé est explicitement spécifié:

.. code-block:: php
   :linenos:

    <?php

    require_once __DIR__.'/../app/bootstrap_cache.php';
    require_once __DIR__.'/../app/AppCache.php';

    use Symfony\Component\HttpFoundation\Request;

    $kernel = new AppCache(new AppKernel('prod', false));
    $kernel->handle(Request::createFromGlobals())->send();

Comme vous pouvez le voir, le code ``prod`` spécifie que cet environnement
s'éxécutera en tant qu'environnement de ``prod``. Une application Symfony2 peut
être éxécutée dans n'importe quel environnement en utilisant ce modèle et en changeant
le code de l'environnement.

.. note::

   L'environnement de ``test`` est utilisé pour les tests fonctionnels et n'est 
   pas accessible directement par le navigateur via un contrôleur de front. En 
   d'autres termes, contrairement aux autres environnements, le contrôleur 
   ``app_test.php`` n'existe pas.

.. index::
   single: Configuration; Debug mode

.. sidebar:: *Debug* Mode

    Chose importante, mais sans rapport avec les *environnements*, la clé ``false``
    à la ligne 8 du contrôleur ci-dessus, spécifie que l'application ne doit pas
    être exécutée en mode "debug". Indépendamment de l'environnement, une application
    Symfony2 peut être exécutée avec le mode debug activé ou non. Cela affecte 
    beaucoup choses dans l'application, notamment si les erreurs doivent être ou
    non affichées ou encore si les fichiers de cache sont regénérés à chaque requête.
    Sans indication contraire, le mode debug est généralement à ``true`` pour 
    les environnements de ``dev`` et de ``test``et à ``false`` pour l'environnement 
    de ``prod``.

    En interne, la valeur du mode debug devient le paramètre ``kernel.debug`` 
    qui est utilisé dans le :doc:`service container </book/service_container>`.
    Si vous jetez un oeil dans le fichier de configuration de l'application, 
    vous verrez que le paramètre est utilisé, par exemple, pour activer ou non 
    l'authentification Doctrine:

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

Créer un nouvel environnement
-----------------------------

Par défaut, une application Symfony2 a 3 environnements qui permettent de gérer 
la plupart des situations. Bien sûr, puisqu'un environnement n'est rien d'autre 
qu'un nom de code correspondant à une configuration, créer un nouvel environnement
est très facile.

Supposez, par exemple, qu'avant le déploiement, vous avez besoin de faire un audit
de votre application. Une manière de le faire est d'utiliser des paramètres de 
pré-production, mais avec le ``web_profiler`` de Symfony2 activé. Cela permet à
Symfony2 d'enregistrer des informations de votre application pendant l'audit.

La meilleure manière de réaliser cela est d'utiliser un nouvel environnement,
appelé, par exemple, ``benchmark``. Commencez par créer un nouveau fichier de 
configuration:

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

Et avec ces simples ajouts, l'application supporte maintenant un nouvel environnement
appelé ``benchmark``.

Cette nouvelle configuration importe la configuration de l'environnement de``prod``
et la modifie. Cela garantie que le nouvel environnement est identique à l'environnement
de ``prod``, exceptés tout changement effectué explicitement.

Parce que vous voudrez que cet environnement soit accessible depuis un navigateur,
vous devrez créer un nouveau contrôleur frontal. Copiez le fichier ``web/app.php`` 
vers ``web/app_benchmark.php`` et éditez l'environnement pour mettre ``benchmark``:

.. code-block:: php

    <?php

    require_once __DIR__.'/../app/bootstrap.php';
    require_once __DIR__.'/../app/AppKernel.php';

    use Symfony\Component\HttpFoundation\Request;

    $kernel = new AppKernel('benchmark', false);
    $kernel->handle(Request::createFromGlobals())->send();

Le nouvel environnement est maintenant accessible via::

    http://localhost/app_benchmark.php

.. note::

   Certains environnements, comme l'environnement de ``dev``, ne sont pas prévus
   pour être déployés sur un serveur ni être accessibles par le public. C'est 
   parce que certain environnements, notamment pour le debuggage, pourraient divulguer
   trop d'informations sur l'application ou son fonctionnement interne. Pour s'assurer
   que ces environnements ne soient pas accessible, le contrôleur est généralement
   protégé contre les accès depuis les Ip externes, comme le montre le code en tête
   du fichier:
   
    .. code-block:: php

        if (!in_array(@$_SERVER['REMOTE_ADDR'], array('127.0.0.1', '::1'))) {
            die('You are not allowed to access this file. Check '.basename(__FILE__).' for more information.');
        }

.. index::
   single: Environments; Cache directory

Les environnements et le cache
------------------------------

Symfony2 tire profit du cache de différentes manières: la configuration de 
l'application, la configuration du routage, les templates Twig et bien plus, sont
convertis en objets PHP et stockés dans des fichiers cachés.

Par défaut, ces fichiers sont majoritairement stockés dans le dossier ``app/cache``.
Cependant, chacque environnement cache ses propres fichiers:

.. code-block:: text

    app/cache/dev   - dossier de cache de l'environnement de *dev*
    app/cache/prod  - dossier de cache de l'environnement de *prod*

Parfois, en débuggant, il peut être utile d'inspecter un fichier caché afin de 
comprendre le fonctionnement de l'application. Dans ce cas, souvenez vous de bien
regarder dans le dossier de l'environnement que vous utilisez (très souvent ``dev`` 
lors du développement et du debug). Bien que cela puisse varier, le dossier 
``app/cache/dev`` contient les fichiers suivants:

* ``appDevDebugProjectContainer.php`` - le "service container" mis en cache qui représente
   la configuration mise en cache de l'application;

* ``appdevUrlGenerator.php`` - la classe PHP générée à partir du fichier de routage
  et utilusé pour générer les URLs;

* ``appdevUrlMatcher.php`` - la classe PHP utilisée pour la correspondance des routes
   C'est ici qu'il faut regarder pour voir les expressions régulières utilsées pour 
   la correspondances entre les URLs entrantes et les différentes routes;

* ``twig/`` - ce dossier contient tous les templates Twig mis en cache.
