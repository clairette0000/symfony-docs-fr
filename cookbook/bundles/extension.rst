.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

.. index::
   single: Configuration; Sémantique
   single: Bundle; Configuration Extension

Comment entreprendre la Configuration Sémantique d'un Bundle?
=============================================================

Une configuration sémantique fournit un moyen encore plus souple de fournir une
configuration pour un Bundle avec les avantages suivants pour de simples
paramètres:

* Possibilité de définir plus que de simples paramètres (des services par exemple);

* Meilleure hiérarchie dans la configuration (vous pouvez définir des configurations imbriquées);

* Fusion intelligente quand plusieurs fichiers de configuration surchargent une configuration existante;

* Validation de configurations (si vous définissez un fichier XSD et utilisez XML);

* Auto-complétion quand vous utilisez XSD et XML.

.. index::
   single: Bundles; Extension
   single: Injection de dépendances, Extension

Création d'une Extention
------------------------

Pour définir une configuration sémantique, créez une extension d'Injection de
Dépendances pour étendre
:class:`Symfony\\Component\\DependencyInjection\\Extension\\Extension`::

    // HelloBundle/DependencyInjection/HelloExtension.php
    use Symfony\Component\DependencyInjection\Extension\Extension;
    use Symfony\Component\DependencyInjection\ContainerBuilder;

    class HelloExtension extends Extension
    {
        public function load(array $configs, ContainerBuilder $container)
        {
            // ...
        }

        public function getXsdValidationBasePath()
        {
            return __DIR__.'/../Resources/config/';
        }

        public function getNamespace()
        {
            return 'http://www.example.com/symfony/schema/';
        }

        public function getAlias()
        {
            return 'hello';
        }
    }

La classe précédente définit un namespace ``hello``, utilisable dans un
quelconque fichier de configuration:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        hello: ~

    .. code-block:: xml

        <!-- app/config/config.xml -->
        <?xml version="1.0" ?>

        <container xmlns="http://symfony.com/schema/dic/services"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:hello="http://www.example.com/symfony/schema/"
            xsi:schemaLocation="http://www.example.com/symfony/schema/ http://www.example.com/symfony/schema/hello-1.0.xsd">

           <hello:config />
           ...

        </container>

    .. code-block:: php

        // app/config/config.php
        $container->loadFromExtension('hello', array());

.. tip::

    Votre extension de code est toujours appelé, même si l'utilisateur ne
    fournit aucune configuration. Dans ce cas, le tableau de configuration sera
    vide et vous pouvez toujours fournir des valeurs par défaut raisonnables si
    vous le souhaitez.

Analyse d'une Configuration
---------------------------

Chaque fois qu'un utilisateur inclut le namespace ``hello`` dans le fichier de
configuration, il est ajouté à un tableau de configurations et est transmis à la
méthode ``load()`` de votre extension (Symfony2 convertit automatiquement XML et
YAML en tableau).

Donc, compte tenu de la configuration suivante:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        hello:
            foo: foo
            bar: bar

    .. code-block:: xml

        <!-- app/config/config.xml -->
        <?xml version="1.0" ?>

        <container xmlns="http://symfony.com/schema/dic/services"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:hello="http://www.example.com/symfony/schema/"
            xsi:schemaLocation="http://www.example.com/symfony/schema/ http://www.example.com/symfony/schema/hello-1.0.xsd">

            <hello:config foo="foo">
                <hello:bar>foo</hello:bar>
            </hello:config>

        </container>

    .. code-block:: php

        // app/config/config.php
        $container->loadFromExtension('hello', array(
            'foo' => 'foo',
            'bar' => 'bar',
        ));

Le tableau transmis à votre méthode ressemble à ce qui suit::

    array(
        array(
            'foo' => 'foo',
            'bar' => 'bar',
        )
    )

Au sein de ``load()``, la variable ``$container`` réfère à un conteneur qui
connaît seulement le namespace de cette configuration. Vous pouvez manipuler cela
de la manière que vous voulez et ajouter des services et des paramètres.

Les paramètres globaux sont les suivants:

* ``kernel.name``
* ``kernel.environment``
* ``kernel.debug``
* ``kernel.root_dir``
* ``kernel.cache_dir``
* ``kernel.logs_dir``
* ``kernel.bundle_dirs``
* ``kernel.bundles``
* ``kernel.charset``

.. caution::

    Tous les paramètres et noms de services préfixés par ``_`` (underscore) sont
    la chasse gardée du framework et aucun autre supplémentaire ne doit être
    défini par des Bundles.

.. index::
   pair: Convention; Configuration

Conventions d'Extension
-----------------------

Quand vous créez une extension, suivez simplement ces conventions:

* L'extension doit être stockée dans le sous-namespace ``DependencyInjection``;

* L'extension doit être nommée après le nom de Bundle et suffixée avec ``Extension`` (``SensionHelloExtension`` pour ``SensioHelloBundle``);

* L'alias doit être unique et nommé après le nom du Bundle (``sensio_blog`` pour ``SensioBlogBundle``);

* L'extension devrait être accompagnée d'un schéma XSD.

Si vous suivez ces simples conventions, vos extensions seront enregistrées
automatiquement par Symfony2. Si non, surchargez la méthode Bundle
:method:`Symfony\\Component\\HttpKernel\\Bundle\\Bundle::build`::

    class HelloBundle extends Bundle
    {
        public function build(ContainerBuilder $container)
        {
            // register the extension(s) found in DependencyInjection/ directory
            parent::build($container);

            // register extensions that do not follow the conventions manually
            $container->registerExtension(new ExtensionHello());
        }
    }
