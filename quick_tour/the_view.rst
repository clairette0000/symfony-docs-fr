La Vue
======

Après avoir lu la première partie de ce tutoriel, vous avez considéré que
Symfony2 valait encore 10 minutes. Je partage votre avis! Dans cette seconde
partie, vous en apprendrez plus sur le moteur de template Symfony2, `Twig`_.
Twig est un moteur de template pour PHP flexible, rapide et sécurisé. Il rend
vos templates plus lisibles et concis, il les rend aussi plus conviviaux pour
les concepteurs de sites Web.

.. note::
    Au lieu de Twig, vous pouvez également utiliser :doc:`PHP </cookbook/templating/PHP>`
    pour vos modèles. Les deux moteurs de template sont pris en charge par
    Symfony2 et sont tout aussi bien supportés.

.. index::
   single: Twig
   single: View; Twig

Twig, un aperçu rapide
----------------------

.. tip::
    Si vous voulez apprendre Twig, nous vous recommandons fortement de lire sa
    `documentation`_ officielle . Cette section est juste un aperçu rapide des
    principaux concepts.

Un template Twig est un fichier texte qui peut générer n'importe quel format
texte (HTML, XML, CSV, LaTeX, ...). Twig définit deux types de séparateurs:

* ``{{ ... }}``: Affiche une variable ou le résultat d'une expression;

* ``{% ... %}``: Un tag qui contrôle la logique d'un template; il exécute les 
boucles ``for`` ou les déclarations ``if`` par exemple.

Ci-dessous, nous dévoilons un succinct template qui illustre quelques notions de
base:

.. code-block:: html+jinja

    <!DOCTYPE html>
    <html>
        <head>
            <title>Mon site Web</title>
        </head>
        <body>
            <h1>{{ page_title }}</h1>

            <ul id="navigation">
                {% for item in navigation %}
                    <li><a href="{{ item.href }}">{{ item.caption }}</a></li>
                {% endfor %}
            </ul>
        </body>
    </html>

Les variables transmises au template peuvent être des chaines de caractères, des
tableaux ou même des objets. Twig discerne la différence entre eux et vous permet
d'accéder aux "attributs" de la variable grâce à l'usage d'un point (``.``).

.. code-block:: jinja

    {# array('name' => 'Fabien') #}
    {{ name }}

    {# array('user' => array('name' => 'Fabien')) #}
    {{ user.name }}

    {# force array lookup #}
    {{ user['name'] }}

    {# array('user' => new User('Fabien')) #}
    {{ user.name }}
    {{ user.getName }}

    {# fforce method name lookup #}
    {{ user.name() }}
    {{ user.getName() }}

    {# pass arguments to a method #}
    {{ user.date('Y-m-d') }}

.. note::
    Il est important de savoir que les accolades ne font pas partie de la
    variable, mais remplacent l'instruction print en quelque sorte. Si vous
    accédez aux variables à l'intérieur des balises, ne mettez pas d'accolades
    autour.

Templates de décoration
-----------------------

Bien souvent, dans un projet, les templates partagent des éléments communs, 
comme le traditionnel binôme header/footer.
Dans Symfony2, nous préférons aborder cette problématique différemment : un 
template peut être décoré par un autre.
De la même manière que des classes PHP: l'héritage de template vous permet de
construire un "layout" de base qui contient tous les éléments communs de votre
site et définit les "blocs" que les templates enfants peuvent surcharger.

Le template ``index.html.twig`` est décoré par le template 
``layout.html.twig``, grâce au tag ``extends``:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% extends "HelloBundle::layout.html.twig" %}

    {% block content %}
        Hello {{ name }}!
    {% endblock %}

La notation ``HelloBundle::layout.html.twig`` vous semble familiaire n'est-ce pas? 
Il s'agit de la même notation que celle utilisée pour un template classique.
Le ``::`` signifie simplement que l'élément contrôleur est vide. En conséquence, 
le fichier correspondant est directement stocké dans ``views/``.

Maintenant, jetons un coup d'œil au fichier ``layout.html.twig``:

.. code-block:: jinja

    {% extends "::base.html.twig" %}

    {% block body %}
        <h1>Hello Application</h1>

        {% block content %}{% endblock %}
    {% endblock %}

Le tag ``{% block %}`` definit deux blocs (``body`` et ``content``) que le
template enfant peut remplir. Tous les tags block ne font qu'annoncer qu'un
template enfant peut l'emporter sur ces portions de template. Le template
``index.html.twig`` surcharge le bloc ``content``. L'autre est définit dans le
layout de base comme le layout étant lui-même enrobé par un autre. Quand la
partie Bundle du template est vide (``::base.html.twig``), les vues sont à
chercher dans le répertoire ``app/views/``. Ce dernier stocke les vues globales
pour votre projet entier:

.. code-block:: jinja

    {# app/views/base.html.twig #}
    <!DOCTYPE html>
    <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
            <title>{% block title %}Hello Application{% endblock %}</title>
        </head>
        <body>
            {% block body '' %}
        </body>
    </html>

Tags, Filtres, et Fonctions
---------------------------

Une des meilleures caractéristiques de Twig est son extensibilité via des tags,
des filtres et des fonctions; Symfony2 en est livré avec de nombreux préintégrés
pour faciliter le travail des concepteurs de sites Web.

Inclure d'autres templates
~~~~~~~~~~~~~~~~~~~~~~~~~~

La meilleure façon de partager un bout de code entre plusieurs templates
distincts est de définir un template qui pourra être inclu dans un autre.

Créez le template ``hello.html.twig``:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/hello.html.twig #}
    Hello {{ name }}

Et changez le template ``index.html.twig`` pour inclure celui que vous venez 
de créer:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% extends "HelloBundle::layout.html.twig" %}

    {# override the body block from index.html.twig #}
    {% block body %}
        {% include "HelloBundle:Hello:hello.html.twig" %}
    {% endblock %}

Inclure d'autres contrôleurs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Que faire si vous voulez inclure le résultat d'un autre contrôleur dans un 
template?
C'est très utile par exemple en Ajax, ou lorsque le template inclus a besoin de 
variables qui ne sont pas disponibles dans le template principal.

Si vous créez une action ``fancy``, et que vous voulez l'inclure dans le 
template ``index``, utilisez le tag ``render``:

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% render "HelloBundle:Hello:fancy" with { 'name': name, 'color': 'green' } %}

Ici, le ``HelloBundle:Hello:fancy`` réfère à l'action ``fancy`` du contrôleur
``Hello``, et l'argument est utilisé comme valeurs d'un chemin de requête
simulée::

    // src/Sensio/HelloBundle/Controller/HelloController.php

    class HelloController extends Controller
    {
        public function fancyAction($name, $color)
        {
            // créez un objet qui a besoin de la variable $color
            $object = ...;

            return $this->render('HelloBundle:Hello:fancy.html.twig', array('name' => $name, 'object' => $object));
        }

        // ...
    }

Créations de liens entre les pages
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Créer des liens entre les pages d'une application web est incontournable. Au
lieu de coder en dur les URL dans les templates, la fonction ``path`` peut 
générer des URLs en fonction de la configuration du routage. De cette manière, 
toutes vos URLs peuvent être facilement modifiées en changeant juste le fichier 
de configuration:

.. code-block:: jinja

    <a href="{{ path('hello', { 'name': 'Thomas' }) }}">Saluons Thomas!</a>

La fonction ``path`` prend le nom de la route et un tableau de paramètres comme
arguments. Le nom de la route est la clé principale en vertu de laquelle les
routes sont référencées et les paramètres sont les valeurs définies dans le
pattern de routage:

.. code-block:: yaml

    # src/Sensio/HelloBundle/Resources/config/routing.yml
    hello: # The route name
        pattern:  /hello/{name}
        defaults: { _controller: HelloBundle:Hello:index }

.. tip::

    La fonction ``url`` génère des URLs *absolus* : ``{{ url('hello', {
    'name': 'Thomas' }) }}``.

Inclusion d'Assets: images, javascripts et feuilles de styles
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Que serait internet sans images, javascripts, et feuilles de styles? Symfony2
fournit la fonction ``asset`` pour les manipuler très facilement:

.. code-block:: jinja

    <link href="{{ asset('css/blog.css') }}" rel="stylesheet" type="text/css" />

    <img src="{{ asset('images/logo.png') }}" />

Le but principal de la fonction ``asset`` est de rendre votre application plus
portable. Grâce à cette fonction, vous pouvez déplacer le répertoire racine de
l'application partout dans votre répertoire racine web, sans changer le moindre 
code dans les templates.

Output Escaping
---------------

Twig est configuré pour échapper automatiquement toutes les sorties par défaut.
Lisez la `documentation`_ de Twig pour en apprendre davantage sur l'Output
Escaping et son extension dédiée.

Réflexions finales
------------------

Twig est simple mais puissant. Grâce à la mise en page, aux blocs, aux modèles
et aux inclusions d'action, il est très facile d'organiser vos modèles de
manière logique et extensible.

Vous avez seulement travaillé avec Symfony2 pendant environ 20 minutes, et vous
pouvez déjà faire des choses assez incroyables avec lui. C'est la puissance de
Symfony2. Apprendre les bases est facile, et vous allez bientôt apprendre que
cette simplicité se cache sous une architecture très flexible.

Mais je m'avance. Tout d'abord, vous devez en savoir plus sur le contrôleur et
c'est exactement le sujet de la prochaine partie de ce tutoriel. Prêt pour 10
minutes supplémentaires avec Symfony2 pour explorer :doc:`the_controller`?

.. _Twig:          http://www.twig-project.org/
.. _documentation: http://www.twig-project.org/documentation
