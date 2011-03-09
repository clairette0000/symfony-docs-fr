.. index::
   pair: Autoloader; Configuration

Comment autocharger ses Classes
===============================

Chaque fois que vous utilisez une classe non définie, PHP utilise le mécanisme
de chargement automatique pour déléguer le chargement d'un fichier définissant
cette classe. Symfony2 fournit un chargeur automatique «universel», qui est
capable de charger les classes à partir des fichiers qui mettent en œuvre l'une
des conventions suivantes:

* La technique d'interopérabilité namespace des `standards`_ de PHP 5.3 et des noms de classes;

* La convention de nommage `PEAR`_ concernant les classes.

Si vos classes et les librairies tierces que vous utilisez pour votre projet
suivent ces standards, l'autoloader de Symfony2 est le seul autoloader qui vous
suffira.

Utilisation
-----------

L'usage de l'autoloader :class:`Symfony\\Component\\ClassLoader\\UniversalClassLoader`
est simple::

    require_once '/path/to/src/Symfony/Component/ClassLoader/UniversalClassLoader.php';

    use Symfony\Component\ClassLoader\UniversalClassLoader;

    $loader = new UniversalClassLoader();
    $loader->register();

L'autoloader est utile seulement si vous ajoutez quelques librairies à
autocharger.

.. note::

    L'autoloader est automatiquement inclus dans une application Symfony2
    ( d'où le ``app/autoload.php``).    

Si les classes à autocharger utilisent la directive namespace, utilisez la méthode
:method:`Symfony\\Component\\ClassLoader\\UniversalClassLoader::registerNamespace`
ou
:method:`Symfony\\Component\\ClassLoader\\UniversalClassLoader::registerNamespaces`::

    $loader->registerNamespace('Symfony', __DIR__.'/vendor/symfony/src');

    $loader->registerNamespaces(array(
        'Symfony' => __DIR__.'/vendor/symfony/src',
        'Zend'    => __DIR__.'/vendor/zend/library',
    ));

Concernant les classes qui suivent la convention de nommage PEAR, utilisez la
méthode
:method:`Symfony\\Component\\ClassLoader\\UniversalClassLoader::registerPrefix`
ou
:method:`Symfony\\Component\\ClassLoader\\UniversalClassLoader::registerPrefixes`::

    $loader->registerPrefix('Twig_', __DIR__.'/vendor/twig/lib');

    $loader->registerPrefixes(array(
        'Swift_' => __DIR__.'/vendor/swiftmailer/lib/classes',
        'Twig_'  => __DIR__.'/vendor/twig/lib',
    ));

.. note::

    Quelques librairies nécessitent aussi que leur propre chemin soit
    enregistré dans leur include path de PHP (``set_include_path()``).

Les classes provenant d'un sous-namespace ou d'une sous-hiérarchie des classes
PEAR peuvent être recherchées dans une liste d'emplacements pour faciliter
l'usage d'un lot de classes plus spécifiques de larges projets::

    $loader->registerNamespaces(array(
        'Doctrine\\Common'           => __DIR__.'/vendor/doctrine-common/lib',
        'Doctrine\\DBAL\\Migrations' => __DIR__.'/vendor/doctrine-migrations/lib',
        'Doctrine\\DBAL'             => __DIR__.'/vendor/doctrine-dbal/lib',
        'Doctrine'                   => __DIR__.'/vendor/doctrine/lib',
    ));

Dans cet exemple, si vous essayez d'utiliser une classe dans le namespace
``Doctrine\Common`` ou un de ses enfants, l'autoloader cherchera d'abord la
classe à l'intérieur du répertoire ``doctrine-common``, et il se repliera alors
vers le répertoire ``Doctrine`` (le dernier configuré) s'il ne trouve pas,
avant d'abandonner. L'ordre des enregistrements est important dans ce cas.

.. _standards: http://groups.google.com/group/php-standards/web/psr-0-final-proposal
.. _PEAR:      http://pear.php.net/manual/en/standards.php
