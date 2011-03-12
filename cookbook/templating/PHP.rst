.. index::
   single: PHP Templates

Comment utiliser PHP plutôt que Twig dans vos templates?
========================================================

Même si Symfony2 utilise Twig comme moteur de template par défaut,vous pouvez 
toujours utiliser du pur code PHP si vous le voulez.
Les deux moteurs de templates sont pleinement supportés par Symfony2.
Symfony2 ajoute quelques fonctionnalités par dessus PHP pour rendre l'écriture 
de templates en PHP encore plus puissante.

Restituer des templates en PHP
------------------------------

Pour restituer un template PHP au lieu d'un template Twig, utilisez l'extension 
``.php`` dans le nom du template au lieu de ``.twig``.
Dans l'exemple ci-dessous, le contrôleur restituera le template ``index.html.php``::

    // src/Sensio/HelloBundle/Controller/HelloController.php

    public function indexAction($name)
    {
        return $this->render('HelloBundle:Hello:index.html.php', array('name' => $name));
    }

.. index::
  single: Templating; Layout
  single: Layout

Templates de décoration
-----------------------

Bien souvent, dans un projet, les templates partagent des éléments communs, 
comme le traditionnel binôme header/footer.
Dans Symfony2, nous préférons aborder cette problématique différemment: un 
template peut être décoré par un autre.

Le template ``index.html.php`` est décoré par le template ``layout.html.php`` 
grâce à l'appel de la méthode ``extend()``:

.. code-block:: html+php

    <!-- src/Sensio/HelloBundle/Resources/views/Hello/index.html.php -->
    <?php $view->extend('HelloBundle::layout.html.php') ?>

    Hello <?php echo $name ?>!

La notation ``HelloBundle::layout.html.php`` vous semble familiaire n'est-ce pas?
Il s'agit de la même notation que celle utilisée pour un template classique.
La partie ``::`` signifie simplement que le contrôleur est vide.
En conséquence, le fichier correspondant est directement stocké dans ``views/``.

Maintenant, jetons un coup d'œil au fichier ``layout.html.php``:

.. code-block:: html+php

    <!-- src/Sensio/HelloBundle/Resources/views/layout.html.php -->
    <?php $view->extend('::base.html.php') ?>

    <h1>Hello Application</h1>

    <?php $view['slots']->output('_content') ?>

Le layout est lui-même décoré par un autre template (``::base.html.php``). Symfony2
supporte des niveaux de décoration multiples: un layout peut très bien être 
décoré par un autre. Lorsque le nom du bundle est laissé vide, cela signifie 
que les templates concernés sont stocké directement dans le dossier 
``app/views/``. Ce dossier stocke les vues globales de votre projet:

.. code-block:: html+php

    <!-- app/views/base.html.php -->
    <!DOCTYPE html>
    <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
            <title><?php $view['slots']->output('title', 'Hello Application') ?></title>
        </head>
        <body>
            <?php $view['slots']->output('_content') ?>
        </body>
    </html>

Pour chacun des layouts, l'expression ``$view['slots']->output('_content')`` 
est remplacée par les templates fils, respectivement ``index.html.php`` et 
``layout.html.php`` (nous verrons les slots dans la section suivante).

Comme vous pouvez le voir, Symfony2 permet d'accéder aux méthodes d'un mystérieux 
objet ``$view``. Dans un template, la variable ``$view`` est toujours disponible 
et fait référence à un objet qui dispose d'un certain nombre de méthodes qui 
rendent le moteur de template plus rapide.

.. index::
   single: Templating; Slot
   single: Slot

Travailler avec les Slots
-------------------------

Un slot est un morceau de code, défini dans un template, et utilisable dans 
n'importe quel layout qui décore ce template. Dans le fichier 
``index.html.php``, définissez le slot ``title``:

.. code-block:: html+php

    <!-- src/Sensio/HelloBundle/Resources/views/Hello/index.html.php -->
    <?php $view->extend('HelloBundle::layout.html.php') ?>

    <?php $view['slots']->set('title', 'Hello World Application') ?>

    Hello <?php echo $name ?>!

Le layout possède déjà le code qui affiche le titre dans le header:

.. code-block:: html+php

    <!-- app/views/layout.html.php -->
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
        <title><?php $view['slots']->output('title', 'Hello Application') ?></title>
    </head>

La méthode ``output()`` affiche le contenu du slot et accepte une valeur par 
défaut optionnelle au cas où le slot ne serait pas défini. 
``_content`` est juste un slot spécial qui contient le code du template fils.

Pour les gros slots, il existe une syntaxe étendue:

.. code-block:: html+php

    <?php $view['slots']->start('title') ?>
        Beaucoup de code HTML
    <?php $view['slots']->stop() ?>

.. index::
   single: Templating; Include

Inclure d'autres templates
--------------------------

La meilleure façon de partager un bout de code entre plusieurs templates
distincts est de définir un template qui pourra être inclu dans un autre.

Créez le template ``hello.html.php``:

.. code-block:: html+php

    <!-- src/Sensio/HelloBundle/Resources/views/Hello/hello.html.php -->
    Hello <?php echo $name ?>!

Et changez le template ``index.html.php`` pour inclure celui que vous venez de créer:

.. code-block:: html+php

    <!-- src/Sensio/HelloBundle/Resources/views/Hello/index.html.php -->
    <?php $view->extend('HelloBundle::layout.html.php') ?>

    <?php echo $view->render('HelloBundle:Hello:hello.html.php', array('name' => $name)) ?>

La méthode ``render()`` génère et retourne le contenu du template passé en 
paramètre (c'est exactement la même méthode que celle utilisée dans les controllers).

.. index::
   single: Templating; Embedding Pages

Inclure d'autres contrôleurs
----------------------------

Que faire si vous voulez inclure le résultat d'un autre contrôleur dans un template?
C'est très utile par exemple en Ajax, ou lorsque le template inclus a besoin de 
variables qui ne sont pas disponibles dans le template principal.

Si vous créer l'action ``fancy``, et que vous voulez l'inclure dans le template ``index.html.php``, utilisez simplement le code suivant:

.. code-block:: html+php

    <!-- src/Sensio/HelloBundle/Resources/views/Hello/index.html.php -->
    <?php echo $view['actions']->render('HelloBundle:Hello:fancy', array('name' => $name, 'color' => 'green')) ?>

Ici, ``HelloBundle:Hello:fancy`` fait référence à l'action ``fancy`` du controller ``Hello``::

    // src/Sensio/HelloBundle/Controller/HelloController.php

    class HelloController extends Controller
    {
        public function fancyAction($name, $color)
        {
            // create some object, based on the $color variable
            $object = ...;

            return $this->render('HelloBundle:Hello:fancy.html.php', array('name' => $name, 'object' => $object));
        }

        // ...
    }

Mais où l'élément de tableau ``$view['actions']`` est-il défini? Comme dans le 
cas de ``$view['slots']``, il s'agit d'un helper que nous aborderons dans la 
section suivante.

.. index::
   single: Templating; Helpers

Utiliser des helpers dans un template
-------------------------------------

Le système de template de Symfony2 peut être facilement étendu grâce aux helpers.
Les helpers sont des objets PHP qui proposent des fonctionnalités utiles dans 
un template. ``actions`` et ``slots`` sont deux des helpers intégrés à Symfony2.

Créer des liens entre les pages
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Créer des liens entre les pages d'une application web est une nécessité.
Au lieu de coder en dur les URL dans les templates, le helper ``router`` 
peut générer des URLs en fonction de la configuration du routage. De cette 
manière, toutes vos URLs peuvent être facilement modifiées en changeant juste 
le fichier de configuration:

.. code-block:: html+php

    <a href="<?php echo $view['router']->generate('hello', array('name' => 'Thomas')) ?>">
        Salut Thomas!
    </a>

La méthode ``generate()`` prend en argument le nom de la route et un tableau de 
paramètres. Le nom de la route est la clé sous laquelle les routes sont 
référencées et les paramètres sont les valeurs définies dans le pattern de routage:

.. code-block:: yaml

    # src/Sensio/HelloBundle/Resources/config/routing.yml
    hello: # The route name
        pattern:  /hello/{name}
        defaults: { _controller: HelloBundle:Hello:index }

Inclure des Assets: images, JavaScripts, et feuilles de style
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Que serait internet sans les images, le JavaScript, et les feuilles de style?
Symfony2 fournit la fonction ``assets`` pour les manipuler très facilement:

.. code-block:: html+php

    <link href="<?php echo $view['assets']->getUrl('css/blog.css') ?>" rel="stylesheet" type="text/css" />

    <img src="<?php echo $view['assets']->getUrl('images/logo.png') ?>" />

Le but principal du helper ``assets`` est de rendre votre application encore 
plus portable. Grâce à ce helper, vous pouvez très facilement déplacer le 
dossier racine de votre application ou vous voulez dans votre répertoire web, 
sans changer le moindre code dans les templates.

Output Escaping
---------------

Dans un template PHP, échappez toujours les variables avant de les afficher::

    <?php echo $view->escape($var) ?>

Par défaut, la méthode ``escape()`` part du principe qu'une variable est 
affichée dans une page HTML. Le second argument vous permet de changer ce contexte.
Par exemple pour afficher une variable dans un fichier javascript, utilisez 
le contexte ``js``::

    <?php echo $view->escape($var, 'js') ?>
