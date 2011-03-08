The Big Picture
===============

Commencer à utiliser Symfony2 en 10 minutes! Ce tutoriel vous guide à travers
quelques-uns des concepts les plus importants derrière Symfony2. Il explique
comment démarrer rapidement en vous montrant la structure d'un exemple de
projet.

Si vous avez utilisé un framework web avant, vous devriez vous sentir à l'aise
avec Symfony2. Sinon, bienvenue à une toute nouvelle façon de développer des
applications web!

.. index::
   pair: Sandbox; Download

Téléchargement et Installation de Symfony2
------------------------------------------

Tout d'abord, vérifiez que vous avez installé et configuré un serveur web (comme
Apache) avec PHP 5.3.2 ou supérieur.

Vous êtes prêt? Commençons par télécharger Symfony2. Pour commencer encore plus
vite, nous allons utiliser la fonction "sandbox Symfony2". Il s'agit d'un projet
Symfony2 préconfiguré qui comprend certains contrôleurs simples et de leurs
bibliothèques requises. Le grand avantage du sandbox sur les autres méthodes
d'installation est que vous pouvez commencer à expérimenter avec Symfony2
immédiatement.

Télécharger le `sandbox`_ et décompressez-le dans votre répertoire racine web.
Vous devriez maintenant avoir une arborescence ``sandbox/`` comme ci-après::

    www/ <- votre répertoire web racine
        sandbox/ <- l'archive décompressée
            app/
                cache/
                config/
                logs/
            src/
                Sensio/
                    HelloBundle/
                        Controller/
                        Resources/
            vendor/
                symfony/
                doctrine/
                ...
            web/

.. index::
   single: Installation; Check

Vérification de la Configuration
--------------------------------

Symfony2 est livré avec un testeur intuitif de configuration du serveur pour
vous éviter des maux de tête dûs au serveur web ou d'une mauvaise configuration
de PHP. Utilisez l'URL suivante pour consulter le diagnostic de votre serveur:

    http://localhost/sandbox/web/check.php

Lisez attentivement le résultat du script et corrigez tous les éventuels
problèmes d'exception.

Maintenant, vous pouvez afficher votre première "vraie" page web Symfony2:

    http://localhost/sandbox/web/app_dev.php/

Symfony2 devrait vous féliciter pour le travail accompli jusqu'à présent!

Création de votre première Application
--------------------------------------

Le sandbox est livré avec un simple Bonjour tout le monde " application "que nous allons utiliser pour en savoir plus sur Symfony2. Go to the following URL to be greeted by Symfony2 (replace Fabien with your first name): Aller à l'URL ci-dessous pour être accueilli par Symfony2 (remplacer Fabien avec votre prénom):

Le sandbox est livré avec une petite ":term:`application`" Hello Word que nous
allons utiliser pour en savoir plus sur Symfony2. Saisissez l'URL ci-dessous
pour être accueilli par Symfony2 (remplacez Fabien par votre prénom):

    http://localhost/sandbox/web/app_dev.php/hello/Fabien

Que ce passe-t-il ici? Disséquons cette URL:

.. index:: Front Controller

* ``app_dev.php``: Il s'agit du "front controller". C'est le point d'entrée
unique de l'application et il répond à toutes les demandes des utilisateurs;

* ``/hello/Fabien``: C'est le chemin d'accès virtuel à la ressource auquel
l'utilisateut souhaite accéder.

Votre responsabilité en tant que développeur est d'écrire le code qui envoie la
demande de l'utilisateur (``/hello/Fabien``) à la ressource qui lui est associée
(``Hello Fabien!``).

.. index::
   single: Configuration

Configuration
~~~~~~~~~~~~~

Les fichiers de configuration de Symfony2 peuvent être aussi bien écrits en PHP,
XML ou `YAML`_. Ces différents types sont compatibles et peuvent être utilisés
de manière interchangeable dans une application.

.. tip::

    Le sandbox est par défaut en YAML mais vous pouvez aisément choisir XML ou
    PHP en ouvrant le fichier ``app/AppKernel.php`` et en modifiant la méthode
    ``registerContainerConfiguration``.

.. index::
   single: Routing
   pair: Configuration; Routing

Routage
~~~~~~~

Symfony2 achemine la requête de votre code en utilisant un fichier de
configuration. Voici quelques exemples du fichier de configuration du routage
pour notre application:

.. configuration-block::

    .. code-block:: yaml

        # app/config/routing.yml
        homepage:
            pattern:  /
            defaults: { _controller: FrameworkBundle:Default:index }

        hello:
            resource: "@HelloBundle/Resources/config/routing.yml"

    .. code-block:: xml

        <!-- app/config/routing.xml -->
        <?xml version="1.0" encoding="UTF-8" ?>

        <routes xmlns="http://www.symfony-project.org/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.symfony-project.org/schema/routing http://www.symfony-project.org/schema/routing/routing-1.0.xsd">

            <route id="homepage" pattern="/">
                <default key="_controller">FrameworkBundle:Default:index</default>
            </route>

            <import resource="@HelloBundle/Resources/config/routing.xml" />
        </routes>

    .. code-block:: php

        // app/config/routing.php
        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->add('homepage', new Route('/', array(
            '_controller' => 'FrameworkBundle:Default:index',
        )));
        $collection->addCollection($loader->import("@HelloBundle/Resources/config/routing.php"));

        return $collection;

Les toutes premières lignes du fichier de configuration du routage définit le
code qui sera exécuté quand l'utilisateur demandera la ressource "``/``" (par
exemple, la page d'accueil).

Si vous êtes à l'aise avec le routage, jetez un oeil à la dernière directive du
fichier de configuration. Symfony2 peut inclure des informations de routage
d'autres fichiers de configuration de routage en utilisant la directive
d'importation. Dans ce cas, nous voulons importer la configuration de routage de
HelloBundle. Un Bundle est comme un plugin qui aurait des pouvoirs décuplés mais
nous en reparlerons plus tard. Pour l'instant, regardons la configuration de
routage que nous avons importé:

.. configuration-block::

    .. code-block:: yaml

        # src/Sensio/HelloBundle/Resources/config/routing.yml
        hello:
            pattern:  /hello/{name}
            defaults: { _controller: HelloBundle:Hello:index }

    .. code-block:: xml

        <!-- src/Sensio/HelloBundle/Resources/config/routing.xml -->
        <?xml version="1.0" encoding="UTF-8" ?>

        <routes xmlns="http://www.symfony-project.org/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.symfony-project.org/schema/routing http://www.symfony-project.org/schema/routing/routing-1.0.xsd">

            <route id="hello" pattern="/hello/{name}">
                <default key="_controller">HelloBundle:Hello:index</default>
            </route>
        </routes>

    .. code-block:: php

        // src/Sensio/HelloBundle/Resources/config/routing.php
        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->add('hello', new Route('/hello/{name}', array(
            '_controller' => 'HelloBundle:Hello:index',
        )));

        return $collection;

Comme vous pouvez le voir, le gabarit de ressources "``/hello/{name}``" (une
chaîne de caractères entre accolades comme ``{name}`` est un espace réservé) est
associé à un contrôleur, référencé par la valeur ``_controller``.

.. index::
   single: Controller
   single: MVC; Controller

Contrôleurs
~~~~~~~~~~~

Le contrôleur définit les actions pour traiter les demandes des utilisateurs et
prépare des réponses (souvent en HTML).

.. code-block:: php
   :linenos:

    // src/Sensio/HelloBundle/Controller/HelloController.php

    namespace Sensio\HelloBundle\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class HelloController extends Controller
    {
        public function indexAction($name)
        {
            return $this->render('HelloBundle:Hello:index.html.twig', array('name' => $name));

            // render a PHP template instead
            // return $this->render('HelloBundle:Hello:index.html.php', array('name' => $name));
        }
    }

The code is pretty straightforward but let's explain it line by line:

* *line 3*: Symfony2 takes advantage of new PHP 5.3 namespacing features,
  and all controllers should be properly namespaced (though this is not
  required). In this example, the controller lives in the bundle named ``HelloBundle``,
  which forms the first part of the ``_controller`` routing value.

* *line 7*: The controller name is the combination of the second part of the
  ``_controller`` routing value  (``Hello``) and the word ``Controller``. It
  extends the built-in ``Controller`` class, which provides useful shortcuts
  (as we will see later in this tutorial).

* *line 9*: Each controller is made of several actions. As per the routing
  configuration, the hello page is handled by the ``index`` action (the third
  part of the ``_controller`` routing value). This method receives the
  placeholder values as arguments (``$name`` in our case).

* *line 11*: The ``render()`` method loads and renders a template file
  (``HelloBundle:Hello:index.html.twig``) with the variables passed as a
  second argument.

But what is a :term:`bundle`? All the code you write in a Symfony2 project is
organized in bundles. In Symfony2 speak, a bundle is a structured set of files
(PHP files, stylesheets, JavaScripts, images, ...) that implements a single
feature (a blog, a forum, ...) and which can be easily shared with other
developers. In our example, we only have one bundle, ``HelloBundle``.

Templates
~~~~~~~~~

The controller renders the ``HelloBundle:Hello:index.html.twig`` template. By 
default, the sandbox uses Twig as its template engine but you can also use
traditional PHP templates if you choose.

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% extends "HelloBundle::layout.html.twig" %}

    {% block content %}
        Hello {{ name }}!
    {% endblock %}

Congratulations! You've had your first taste of Symfony2 code and created
your first page. That wasn't so hard, was it? There's a lot more to explore,
but you should already see how Symfony2 makes it really easy to implement
web sites better and faster.

.. index::
   single: Environment
   single: Configuration; Environment

Working with Environments
-------------------------

Now that you have a better understanding of how Symfony2 works, have a closer
look at the bottom of the page; you will notice a small bar with the Symfony2
and PHP logos. This is called the "Web Debug Toolbar" and it is the developer's
best friend. Of course, such a tool must not be displayed when you deploy your
application to production. That's why you will find another front controller in
the ``web/`` directory (``app.php``), optimized for the production environment:

    http://localhost/sandbox/web/app.php/hello/Fabien

And if you use Apache with ``mod_rewrite`` enabled, you can even omit the
``app.php`` part of the URL:

    http://localhost/sandbox/web/hello/Fabien

Last but not least, on the production servers, you should point your web root
directory to the ``web/`` directory to secure your installation and have an even
better looking URL:

    http://localhost/hello/Fabien

To make the production environment as fast as possible, Symfony2 maintains a
cache under the ``app/cache/`` directory. When you make changes to the code or
configuration, you need to manually remove the cached files. When developing
your application, you should use the development front controller (``app_dev.php``),
which does not use the cache. When using the development front controller,
your changes will appear immediately.

Final Thoughts
--------------

Thanks for trying out Symfony2! By now, you should be able to create your own 
simple routes, controllers and templates. As an exercise, try to build 
something more useful than the Hello application! If you are eager to 
learn more about Symfony2, dive into the next section: "The View".

.. _sandbox: http://symfony-reloaded.org/code#sandbox
.. _YAML:    http://www.yaml.org/
