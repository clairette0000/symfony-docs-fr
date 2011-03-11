Préliminaires
=============

Commencez à utiliser Symfony2 en 10 minutes! Ce tutoriel vous guide à travers
quelques-uns des concepts les plus importants de Symfony2. Il explique
comment démarrer rapidement en vous montrant la structure d'un exemple de
projet.

Si vous avez utilisé un framework web avant, vous devriez vous sentir à l'aise
avec Symfony2. Sinon, bienvenue dans une toute nouvelle façon de développer des
applications web!

.. index::
   pair: Sandbox; Download

Téléchargement et Installation de Symfony2
------------------------------------------

Tout d'abord, vérifiez que vous avez installé et configuré un serveur web (comme
Apache) avec PHP 5.3.2 ou supérieur.

Vous êtes prêt? Commençons par télécharger Symfony2. Pour commencer encore plus
vite, nous allons utiliser le "sandbox Symfony2". Il s'agit d'un projet
Symfony2 préconfiguré qui comprend certains contrôleurs simples et les
bibliothèques requises. Le grand avantage du sandbox sur les autres méthodes
d'installation est que vous pouvez commencer à expérimenter Symfony2
immédiatement.

Télécharger le `sandbox`_ et décompressez-le dans votre répertoire racine web.
Vous devriez maintenant avoir une arborescence ``sandbox/`` comme ci-après::

    www/ ← votre répertoire web racine
        sandbox/ ← l'archive décompressée
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
vous éviter des maux de tête dûs au serveur web ou à une mauvaise configuration
de PHP. Utilisez l'URL suivante pour consulter le diagnostic de votre serveur:

    http://localhost/sandbox/web/check.php

Lisez attentivement le résultat du script et corrigez tous les éventuels
problèmes d'exception.

Maintenant, vous pouvez afficher votre première "vraie" page web Symfony2:

    http://localhost/sandbox/web/app_dev.php/

Symfony2 devrait vous féliciter pour le travail accompli jusqu'à présent!

Création de votre première Application
--------------------------------------

Le sandbox est livré avec une petite ":term:`application`" Hello Word que nous
allons utiliser pour en savoir plus sur Symfony2. Saisissez l'URL ci-dessous
pour être accueilli par Symfony2 (remplacez Fabien par votre prénom):

    http://localhost/sandbox/web/app_dev.php/hello/Fabien

Que se passe-t-il ici? Disséquons cette URL:

.. index:: Front Controller

* ``app_dev.php``: Il s'agit du contrôleur frontal. C'est le point d'entrée
unique de l'application et il répond à toutes les demandes des utilisateurs;

* ``/hello/Fabien``: C'est le chemin d'accès virtuel à la ressource auquel
l'utilisateur souhaite accéder.

Votre responsabilité en tant que développeur est d'écrire le code qui envoie la
demande de l'utilisateur (``/hello/Fabien``) à la ressource qui lui est associée
(``Hello Fabien!``).

.. index::
   single: Configuration

Configuration
~~~~~~~~~~~~~

Les fichiers de configuration de Symfony2 peuvent être aussi bien écrits en PHP
qu'en XML ou en `YAML`_. Ces différents types sont compatibles et peuvent être
utilisés de manière interchangeable dans une application.

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

Les toutes premières lignes du fichier de configuration du routage définissent le
code qui sera exécuté quand l'utilisateur demandera la ressource "``/``" (par
exemple, la page d'accueil).

Si vous êtes à l'aise avec le routage, jetez un oeil à la dernière directive du
fichier de configuration. Symfony2 peut inclure des informations de routage
d'autres fichiers de configuration de routage en utilisant la directive
d'importation. Dans ce cas, nous voulons importer la configuration de routage de
HelloBundle. Un Bundle est comme un plugin qui aurait des pouvoirs décuplés mais
nous en reparlerons plus tard. Pour l'instant, regardons la configuration de
routage que nous avons importée:

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

Le code est assez simple à comprendre mais nous allons quand même l'expliquer
ligne par ligne:

* *ligne 3*: Symfony2 tire profit de la nouvelle fonctionnalité de PHP 5.3
  (*namespace*) et tous les contrôleurs devraient être proprement "namespacés".
  Dans cet exemple, le contrôleur se situe dans le Bundle intitulé HelloBundle,
  qui forme la première partie de la valeur du routage ``_controller``.
  
* *ligne 7*: Le nom du contrôleur est une combinaison de la seconde partie de la
  valeur ``_controller`` du routage (``Hello``) et du mot ``Controller``. Elle
  étend la classe intégrée ``Controller``, qui offre des raccourcis utiles (comme
  nous le verrons un peu plus tard dans ce tutoriel).
  
* *ligne 9*: Chaque contrôleur est constitué de plusieurs actions. Selon la
  configuration du routage, la page hello est gérée par l'action ``index`` (la
  troisième partie de la valeur du routage ``_controller``). Cette méthode
  reçoit les valeurs indiquées en tant qu'arguments (``$name`` dans notre cas).
  
* *ligne 11*: La méthode ``render()`` charge et transforme un fichier template
  (``HelloBundle:Hello:index.html.twig``) avec les variables passées comme
  second argument.
  
Mais qu'est-ce qu'un :term:`Bundle` ? Tout le code que vous écrivez dans un
projet Symfony2 est organisée en Bundles. Dans le jargon Symfony2, un Bundle est
un ensemble structuré de fichiers (scripts PHP, feuilles de style CSS,
javascripts, images,...) qui implémente une fonction unique (un blog,
un forum,...) et qui peuvent être facilement partagés avec d'autres
développeurs. Dans notre exemple, nous n'avons qu'un seul Bundle,
``HelloBundle``.

Templates
~~~~~~~~~

Le contrôleur diffuse le template ``HelloBundle:Hello:index.html.twig``. Par
défaut, le sandbox utilise Twig comme moteur de template, mais vous pouvez
également utiliser un template PHP traditionnel si vous voulez.

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% extends "HelloBundle::layout.html.twig" %}

    {% block content %}
        Hello {{ name }}!
    {% endblock %}

Félicitations! Vous avez eu votre première approche du code de Symfony2 et
créé votre première page. Ce n'était pas si éreintant, n'est-ce pas? Il y a
beaucoup plus à explorer, mais vous devriez déjà voir comment Symfony2 permet
vraiment facilement de mettre en œuvre plus rapidement de meilleurs sites.

.. index::
   single: Environment
   single: Configuration; Environment

Différenciation des environnements
----------------------------------

Maintenant que vous avez une meilleure compréhension de la façon dont Symfony2
fonctionne, intéressons nous de plus près au bas de la page, vous remarquerez
une petite barre avec les logos de Symfony2 et PHP. C'est ce qu'on appelle la
"barre de debug Symfony" et c'est le meilleur ami du développeur. Bien entendu,
un tel outil ne doit pas être affiché lorsque vous déployez votre application en
production. C'est pourquoi vous trouverez un autre contrôleur frontal dans le
répertoire ``web/`` (``app.php``), optimisé pour l'environnement de production:

    http://localhost/sandbox/web/app.php/hello/Fabien

Et si vous utilisez Apache avec ``mod_rewrite`` activé, vous pouvez même
occulter le ``app.php`` de votre URL:

    http://localhost/sandbox/web/hello/Fabien

Dernière chose et pas des moindres, sur les serveurs de production, vous devez
pointer votre répertoire racine web sur le répertoire ``web/`` pour garantir
votre installation et avoir une meilleure apparence d'URL:

    http://localhost/hello/Fabien

Pour rendre l'environnement de production aussi véloce que possible, Symfony2
maintient un cache dans le répertoire ``app/cache/``. Lorsque vous apportez des
modifications au code ou à la configuration, vous devrez supprimer manuellement
les fichiers mis en cache. Lorsque vous développez votre application, vous devez
utiliser le contrôleur frontal de développement (``app_dev.php``), qui n'utilise
pas le cache. Lorsque vous utilisez le contrôleur frontal de développement, vos
modifications apparaissent immédiatement.

Réflexions finales
------------------

Merci d'essayer Symfony2! A l'heure actuelle, vous devriez être en mesure de
vous exercer sur vos propres routages, contrôleurs et templates. Comme exercice,
essayez de construire quelque chose de plus utile que l'application Hello! Si
vous êtes désireux d'en apprendre plus sur Symfony2, plongez dans la section
suivante: ":doc:`the_view`".

.. _sandbox: http://symfony-reloaded.org/code#sandbox
.. _YAML:    http://www.yaml.org/
