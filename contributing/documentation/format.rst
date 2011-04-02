Format de la documentation
==========================

La documentation de Symfony2 utilise `reStructuredText`_ comme langage de
structuration et `Sphinx`_ pour produire le rendu (HTML, PDF,...).

reStructuredText
----------------

reStructuredText est un fichier texte et un système de compilation dont la
syntaxe de balisage est facile à lire et "what-you-see-is-what-you-get".

Vous pouvez en apprendre davantage sur sa syntaxe en consultant les `documents`_
de Symfony2 existants ou `reStructuredText Primer`_ sur le site de Sphinx.

Si vous êtes un adepte de Markdown, faites attention, certaines choses sont
parfois similaires mais différentes:

* Les listes commencent en début de ligne (l'indentation n'est pas permise);

* Les blocs au sein d'une ligne utilisent les double-coches (````comme cela````).

Sphinx
------

Sphinx est un générateur de rendu qui ajoute quelques outils utiles pour créer
la documentation à partir de documents reStructuredText. Il ajoute par exemple
de nouvelle directives et des rôles d'interprétations de texte au standard de
`structuration`_ reST.

Coloration syntaxique
~~~~~~~~~~~~~~~~~~~~~

Tous les codes d'exemples utilisent PHP comme langage coloré par défaut. Vous
pouvez changer cela avec la directive ``code-block``:

.. code-block:: rst

    .. code-block:: yaml

        { foo: bar, bar: { foo: bar, bar: baz } }

Si votre code PHP commence par ``<?php``, vous devrez utilisez ``html+php``
en tant que pseudo-langage coloré:

.. code-block:: rst

    .. code-block:: html+php

        <?php echo $this->foobar(); ?>

.. note::

    Une liste des langages supportés est disponible sur le `site de Pygments`_.

Blocs de configuration
~~~~~~~~~~~~~~~~~~~~~~

À chaque fois que vous voulez afficher une configuration, vous devez utiliser
la directive ``configuration-block`` pour afficher la configuration dans tous
les formats de configuration supportés (``PHP``, ``YAML``, and ``XML``):

.. code-block:: rst

    .. configuration-block::

        .. code-block:: yaml

            # Configuration en YAML

        .. code-block:: xml

            <!-- Configuration en XML //-->

        .. code-block:: php

            // Configuration en PHP

Le bout de code reST précedent affiche ce qui suit:

.. configuration-block::

    .. code-block:: yaml

        # Configuration en YAML

    .. code-block:: xml

        <!-- Configuration en XML //-->

    .. code-block:: php

        // Configuration en PHP

La liste actuelle des formats supportés est la suivante:

=============== ===========
Format Markup   Affiché
=============== ===========
html            HTML
xml             XML
php             PHP
yaml            YAML
jinja           Twig
html+jinja      Twig
jinja+html      Twig
php+html        PHP
html+php        PHP
ini             INI
php-annotations Annotations
=============== ===========

.. _reStructuredText:        http://docutils.sf.net/rst.html
.. _Sphinx:                  http://sphinx.pocoo.org/
.. _documents:               http://github.com/symfony/symfony-docs
.. _reStructuredText Primer: http://sphinx.pocoo.org/rest.html
.. _structration:            http://sphinx.pocoo.org/markup/
.. _site de Pygments:        http://pygments.org/languages/
