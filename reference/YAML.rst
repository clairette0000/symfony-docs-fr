.. index::
   single: YAML
   single: Configuration; YAML

YAML
====

Le site web `YAML`_ indique qu'il "est un standard de serialisation de données
lisible par les humains pour tous les langages de programmation". YAML est un
langage simple qui décrit des données. Comme PHP, il possède une syntaxe pour
des types simples tels que les chaînes de caractères, les booléens,
les flottants, ou les entiers. Mais contrairement à PHP, il fait la différence
entre les tableaux (séquences) et les associations (clé/valeur).

Le composant :namespace:`Symfony\\Component\\Yaml` Symfony2 sait comment
analyser du YAML et traduire un tableau PHP en YAML.

.. note::

    Même si le format YAML permet de décrire des structures complexes de données
    imbriquées, ce chapitre présente seulement les fonctionnalités nécessaires
    pour utiliser YAML comme format de fichier de configuration.  

Lire des fichiers YAML
----------------------

La méthode :method:`Symfony\\Component\\Yaml\\Parser::parse` analyse une
chaîne YAML et la convertit en un tableau PHP::

    use Symfony\Component\Yaml\Parser;

    $yaml = new Parser();
    $valeur = $yaml->parse(file_get_contents('/chemin/vers/le/fichier.yml'));

Si une erreur apparaît durant l'analyse, l'analyseur lance une exception
indiquant le type d'erreur et la ligne de la chaîne YAML responsable de
l'erreur::

    try {
        $valeur = $yaml->parse(file_get_contents('/chemin/vers/le/fichier.yml'));
    } catch (\InvalidArgumentException $e) {
        // an error occurred during parsing
        echo "Impossible de parser la chaîne YAML: ".$e->getMessage();
    }

.. tip::

    Comme l'analyseur est réutilisable, vous pouvez utiliser le même objet pour
    charger différentes chaînes YAML.

Lors du chargement d'un fichier YAML, il est parfois préférable d'utiliser la
méthode statique :method:`Symfony\\Component\\Yaml\\Yaml::load`::

    use Symfony\Component\Yaml\Yaml;

    $loader = Yaml::load('/chemin/vers/le/fichier.yml');

La méthode statique ``Yaml::load()`` prend en argument une chaine ou un fichier
contenant du YAML. En interne, elle appelle la méthode ``Parser::parse()``, mais
avec quelques bonus en plus:

* Elle exécute les fichiers YAML comme s'il s'agissait de fichiers PHP afin que
  vous puissiez intégrer des commandes PHP dans les fichiers YAML ;

* Quand un fichier ne peut pas être analysé, elle ajoute automatiquement le nom
  du fichier au message d'erreur, simplifiant le débogage quand votre application
  charge plusieurs fichiers YAML.

Écrire des fichiers YAML
------------------------

La méthode :method:`Symfony\\Component\\Yaml\\Dumper::dump` traduit n'importe
quel tableau PHP dans sa représentation YAML::

    use Symfony\Component\Yaml\Dumper;

    $array = array('foo' => 'bar', 'bar' => array('foo' => 'bar', 'bar' => 'baz'));

    $dumper = new Dumper();
    $yaml = $dumper->dump($array);
    file_put_contents('/chemin/vers/le/fichier.yml', $yaml);

.. note::

    Il y a quelques limitations: le traducteur n'est pas capable de traduire
    des ressources et la traduction d'objets PHP est condidérée comme une
    fonctionnalité non stable.

Si vous n'avez besoin que de traduire un tableau, vous pouvez utiliser 
la méthode statique raccourcie :method:`Symfony\\Component\\Yaml\\Yaml::dump`::

    $yaml = Yaml::dump($array, $inline);

Le format YAML supporte les tableaux en deux dimensions. Par défaut, le
traducteur utilise la représentation en ligne:

.. code-block:: yaml

    { foo: bar, bar: { foo: bar, bar: baz } }

Mais le second argument de la méthode ``dump()`` permet de personnaliser le
niveau à partir duquel la sortie passe de la représentation étendue à la
représentation en ligne::

    echo $dumper->dump($array, 1);

.. code-block:: yaml

    foo: bar
    bar: { foo: bar, bar: baz }

.. code-block:: php

    echo $dumper->dump($array, 2);

.. code-block:: yaml

    foo: bar
    bar:
        foo: bar
        bar: baz

La syntaxe YAML
---------------

Chaînes
~~~~~~~

.. code-block:: yaml

    Une chaîne en YAML

.. code-block:: yaml

    'Une chaîne délimitée par des apostrophes'

.. tip::
   Dans une chaîne délimitée par des apostrophes, une apostrophe doit être
   doublée:

   .. code-block:: yaml

        'Une apostrophe '' dans une chaîne délimitée par des apostrophes'

.. code-block:: yaml

    "Une chaine entre guillemets en YAML\n"
    "Une chaîne délimitée par des guillemets\n"

Les syntaxes utilisant des apostrophes ou des guillemets sont utiles lorsque des
chaînes commencent ou se terminent avec un espace ou plus.

.. tip::

    La syntaxe utilisant des guillemets fournit une méthode permettant
    d'exprimer des chaînes étendues en utilisant le caractère d'échappement
    ``\``. C'est utile lorsque vous devez incorporer la séquence ``\n`` ou des
    caractères unicode dans une chaîne.

Quand une chaîne contient des sauts de lignes, vous pouvez utiliser la méthode
littérale, exprimée par le *pipe* (``|``), pour indiquer que la chaîne va
couvrir plusieurs lignes. Avec cette méthode, les sauts de lignes sur des lignes
vierges sont préservées:

.. code-block:: yaml

    |
      \/ /| |\/| |
      / / | |  | |__

Alternativement, les chaînes peuvent être écrites avec le style compact, dénoté
par le chevron ``>``, où chaque saut de ligne est remplacé par un espace:

.. code-block:: yaml

    >
      Ceci est une très longue phrase
      qui couvre plusieurs lignes en YAML,
      mais chacune d'elles sera représentée
      comme une chaîne sans saut de lignes.

.. note::

    Notez les deux espaces avant chaque ligne dans les précédents exemples. Ils
    n'apparaîtront pas dans les chaînes PHP résultantes.

Nombres
~~~~~~~

.. code-block:: yaml

    # un entier
    12

.. code-block:: yaml

    # un octal
    014

.. code-block:: yaml

    # un héxadécimal
    0xC

.. code-block:: yaml

    # un flottant
    13.4

.. code-block:: yaml

    # un nombre exponentiel
    1.2e+34

.. code-block:: yaml

    # l'infini
    .inf

Valeurs nulles
~~~~~~~~~~~~~~

La valeur nulle en YAML peut être exprimée avec ``null`` ou ``~``.

Booléens
~~~~~~~~

Les booléens en YAML sont exprimés avec ``true`` et ``false``.

Dates
~~~~~

YAML utilise le standard ISO-8601 pour désigner des dates:

.. code-block:: yaml

    2001-12-14t21:59:43.10-05:00

.. code-block:: yaml

    # date simple
    2002-12-14

Collections
~~~~~~~~~~~

Un fichier YAML est rarement utilisé pour décrire une donnée simple. La plupart
du temps, il décrit une collection. Une collection peut être une séquence ou une
association d'élements. Les deux sont converties en tableau PHP.

Les séquences utilisent un tiret suivi d'un espace (``-`` ):

.. code-block:: yaml

    - PHP
    - Perl
    - Python

Le code YAML précédent est équivalent au code PHP suivant::

    array('PHP', 'Perl', 'Python');

Les associations utilisent les deux-points suivi d'un espace (``:`` ) pour
marquer chaque paire de clé/valeur:

.. code-block:: yaml

    PHP: 5.2
    MySQL: 5.1
    Apache: 2.2.20

Ce qui est équivalent à ce code PHP::

    array('PHP' => 5.2, 'MySQL' => 5.1, 'Apache' => '2.2.20');

.. note::

    Dans une association, une clé peut être n'importe donnée.

Le nombre d'espace entre les deux-points et la valeur ne pose aucun problème:

.. code-block:: yaml

    PHP:    5.2
    MySQL:  5.1
    Apache: 2.2.20

YAML utilise l'indentation avec un espace ou plus pour décrire les collections
imbriquées:

.. code-block:: yaml

    "symfony 1.4":
        PHP:      5.2
        Doctrine: 1.2
    "Symfony2":
        PHP:      5.3
        Doctrine: 2.0

Ce code YAML est équivalent au code PHP suivant::

    array(
        'symfony 1.4' => array(
            'PHP'      => 5.2,
            'Doctrine' => 1.2,
        ),
        'Symfony2' => array(
            'PHP'      => 5.3,
            'Doctrine' => 2.0,
        ),
    );

Il y a une chose importante à retenir lorsque vous utilisez l'indentation dans
un fichier YAML: *l'indentation doit être réalisée avec un ou plusieurs espaces,
mais jamais avec des tabulations*.

Vous pouvez imbriquer des séquences et des associations comme vous le souhaitez:

.. code-block:: yaml

    'Chapitre 1':
        - Introduction
        - Types d'évènements
    'Chapitre 2':
        - Introduction
        - Assistants

.. todo:: rendre ce paragraphe moins alambiqué
YAML peut aussi utiliser des dispositions de flux pour les collections, en
utilisant des indicateurs plutôt que des indentations pour en désigner le champ
d'application.

Une séquence peut être écrite comme une liste séparée par des virgules au sein
de crochets (``[]``):

.. code-block:: yaml

    [PHP, Perl, Python]

Une association peut être écrite comme une liste de clé/valeur séparées par une
virgule au sein d'accolades (``{}``):

.. code-block:: yaml

    { PHP: 5.2, MySQL: 5.1, Apache: 2.2.20 }

Vous pouvez alterner les styles pour obtenir une meilleure lisibilité:

.. code-block:: yaml

    'Chapter 1': [Introduction, Types d'évènements]
    'Chapter 2': [Introduction, Assistants]

.. code-block:: yaml

    "symfony 1.4": { PHP: 5.2, Doctrine: 1.2 }
    "Symfony2":    { PHP: 5.3, Doctrine: 2.0 }

Commentaires
~~~~~~~~~~~~

Des commentaires peuvent être ajoutés en YAML en les préfixant d'un dièse
(``#``):

.. code-block:: yaml

    # Commentaire sur une ligne
    "Symfony2": { PHP: 5.3, Doctrine: 2.0 } # Commentaire en fin de ligne

.. note::

    Les commentaires sont simplement ignorés par l'analyseur YAML et ne
    nécessitent pas d'être indentés au niveau de l'imbrication dans une
    collection.

Fichiers YAML dynamiques
~~~~~~~~~~~~~~~~~~~~~~~~

Dans Symfony2, un fichier YAML peut contenir du code PHP qui est évalué juste
avant que l'analyse syntaxique se produise:

.. code-block:: yaml

    1.0:
        version: <?php echo file_get_contents('1.0/VERSION')."\n" ?>
    1.1:
        version: "<?php echo file_get_contents('1.1/VERSION') ?>"

Faites attention à ne pas déformer l'indentation. Gardez à l'esprit les simples
astuces suivantes lorsque vous ajoutez du code PHP dans un fichier YAML:

* Les balises ``<?php ?>`` doivent toujours commencer la ligne où être
  incorporées dans une valeur.

* Si les balises ``<?php ?>`` terminent une ligne, vous devez explicitement
  indiquer une nouvelle ligne ("\n").

.. _YAML: http://yaml.org/
