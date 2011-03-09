.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

Comment personnaliser vos pages d'erreur
========================================

Quand une exception est capturée par Symfony2, celle-ci est traitée à l'intérieur
de la classe ``Kernel`` et éventuellement renvoyée vers un contrôleur
spécial, ``FrameworkBundle:Exception:show`` pour être manipulée. Le contrôleur,
qui se situe dans le core ``FrameworkBundle``, détermine quel template d'erreur
sera affiché et le code de statuts qui devrait être attribué à une exception donnée.

.. tip::

    La personnalisation d'une exception en cours de traitement est normalement
    bien plus puissante que ce qui est écrit ici même. Un évènement interne,
    ``core.exception``, est déclenché ce qui autorise une prise en main au-delà
    de la simple capture de l'exception. Pour plus d'informations, intéressez
    vous à ``events-core.exception``.

Tous les templates d'erreur se situent à l'intérieur du ``FrameworkBundle``.
Pour surcharger un template, nous devons nous fier à la méthode standard pour
surcharger des templates qui résident dans un Bundle. Pour plus d'informations,
consultez :ref:`overiding-bundle-templates`.

Par exemple, pour surcharger le template d'erreur par défaut qui est dévoilé au
visiteur, créez un nouveau template localisé dans
``app/views/FrameworkBundle/Exception/error.html.twig``:

.. code-block:: html+jinja

    <!DOCTYPE html>
    <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
        </head>
        <body>
            <h1>Oops! Un problème est survenu !</h1>
            <h2>Le serveur nous indique : "{{ exception.statuscode }} {{ exception.statustext }}".</h2>
        </body>
    </html>

.. tip::
    
    Si vous n'êtes pas encore tout à fait familier avec Twig, ne vous tracassez
    pas pour autant. Twig est un simple et puissant moteur de template
    optionnel bien qu'il soit déjà intégré à ``Symfony2`` par défaut.

En plus de l'affichage HTML standard de la page d'erreur, Symfony2 dispose de
multitudes de pages d'erreur par défaut pour les formats de réponses les plus
répandus, comme JSON (``error.json.twig``), XML (``error.xml.twig``) et même
javascript (``error.js.twig``) pour ne citer que ceux-là. Pour surcharger
n'importe quel de ces templates, il suffit juste de créer un nouveau fichier avec
le même nom dans votre répertoire ``app/views/FrameworkBundle/Exception``. C'est
la façon classique de surcharger un quelconque template situé dans un Bundle.

Les pages d'exception "debug-friendly" dévoilées au développeur peuvent même être
personnalisées de la même façon en créant des templates comme ``exception.html.twig``
pour une page classique d'exception HTML ou ``exception.json.twig`` pour une page
d'exception JSON.

.. tip::

    Pour découvrir la liste complète de templates d'erreurs par défaut, explorez
    le répertoire ``Resources/views/Exception`` du ``FrameworkBundle``. Dans une
    installation de Symfony2 standard, le ``FrameworkBundle`` peut être retrouvé
    dans ``vendor/symfony/src/Symfony/Bundle/FrameworkBundle``. Souvent, la
    façon la plus déconcertante de facilité pour personnaliser une page d'erreur
    est de copier le ``FrameworkBundle`` dans ``app/views/FrameworkBundle/Exception``
    puis d'en modifier le contenu.
