Format de la documentation
==========================

La documentation de Symfony2 utilise `reStructuredText`_ comme langage de
structuration et `Sphinx`_ pour produire le rendu (HTML, PDF, ...).

reStructuredText
----------------

reStructuredText "is an easy-to-read, what-you-see-is-what-you-get plaintext
markup syntax and parser system".

Vous pouvez en apprendre davantage sur sa syntaxe en consultant les `documents`_
Symfony2 existants ou `reStructuredText Primer`_ sur le site de Sphinx.

.. todo: "si vous êtes familiez", à revoir

Si vous êtes familiez avec Markdown, faites attention, certaines choses sont
parfois similaires mais différentes :

* Les listes commencent en début de ligne (l'indentation n'est pas permise) ;

* Les blocs au sein d'une ligne utilisent les double-coches (````comme cela````).

Sphinx
------

Sphinx est un système de construction qui ajoute quelques outils utiles pour
créer la documentation à partir de documents reStructuredText. Il ajoute par
exemple de nouvelle directives et des rôles d'interprétations de texte au
standard de `structuration`_ reST.

Coloration syntaxique
~~~~~~~~~~~~~~~~~~~~~

Tous les codes d'exemple utilisent PHP comme langage coloré par défaut. Vous
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

Whenever you show a configuration, you must use the ``configuration-block``
directive to show the configuration in all supported configuration formats
(``PHP``, ``YAML``, and ``XML``):

.. code-block:: rst

    .. configuration-block::

        .. code-block:: yaml

            # Configuration in YAML

        .. code-block:: xml

            <!-- Configuration in XML //-->

        .. code-block:: php

            // Configuration in PHP

The previous reST snippet renders as follow:

.. configuration-block::

    .. code-block:: yaml

        # Configuration in YAML

    .. code-block:: xml

        <!-- Configuration in XML //-->

    .. code-block:: php

        // Configuration in PHP

The current list of supported formats are the following:

=============== ===========
Markup format   Displayed
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
.. _markup:                  http://sphinx.pocoo.org/markup/
.. _Pygments website:        http://pygments.org/languages/
