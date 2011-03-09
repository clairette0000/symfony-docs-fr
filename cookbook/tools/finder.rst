.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

.. index::
   single: Finder

Comment faire pour rechercher des Fichiers
==========================================

Le composant :namespace:`Symfony\\Component\\Finder` vous aide à trouver des
fichiers et des répertoires rapidement et facilement.

Utilisation
-----------

La classe :class:`Symfony\\Component\\Finder\\Finder` trouve des fichiers et/ou
des répertoires::

    use Symfony\Component\Finder\Finder;

    $finder = new Finder();
    $finder->files()->in(__DIR__);

    foreach ($finder as $file) {
        print $file->getRealpath()."\n";
    }

La variable ``$file`` est une instance de :phpclass:`SplFileInfo`.

Le code ci-dessus affiche les noms de tous les fichiers dans le répertoire
courant récursivement. La classe Finder utilise une interface fluide, de sorte
que toutes les méthodes retournent une instance Finder.

.. tip::

    Une instance Finder est un `Iterator`_ PHP. Donc, au lieu de parcourir les
    Finder avec ``foreach``, vous pouvez aussi le convertir en un tableau avec
    la méthode :phpfunction:`iterator_to_array`, ou obtenir le nombre
    d'éléments avec :phpfunction:`iterator_count`.

Critères
--------

Localisation
~~~~~~~~~~~~

La localisation est le seul critère indispensable. Il indique au Finder sur quel
répertoire concentrer sa recherche::

    $finder->in(__DIR__);

Cherchez dans plusieurs localisations en enchaînant les appels à la méthode
:method:`Symfony\\Component\\Finder\\Finder::in`::

    $finder->files()->in(__DIR__)->in('/elsewhere');

Excluez des répertoires donnés avec la méthode
:method:`Symfony\\Component\\Finder\\Finder::exclude`::

    $finder->in(__DIR__)->exclude('ruby');

Puisque le Finder emploie des iterators PHP, vous pouvez passez n'importe quelle
URL d'un `protocole`_ supporté::

    $finder->in('ftp://example.com/pub/');

Et il fonctionne aussi avec un Stream::

    use Symfony\Component\Finder\Finder;

    $s3 = new \Zend_Service_Amazon_S3($key, $secret);
    $s3->registerStreamWrapper("s3");

    $finder = new Finder();
    $finder->name('photos*')->size('< 100K')->date('since 1 hour ago');
    foreach ($finder->in('s3://bucket-name') as $file) {
        // do something

        print $file->getFilename()."\n";
    }

.. note::
    Lisez la documentation détaillant les `Streams`_ pour apprendre comment
    créer vos propres streams.
    
Fichiers ou Répertoires
~~~~~~~~~~~~~~~~~~~~~~~

Par défaut, le Finder retourne aussi bien des fichiers que des répertoires mais
la méthode
:method:`Symfony\\Component\\Finder\\Finder::files` ou
:method:`Symfony\\Component\\Finder\\Finder::directories` sait les distinguer::

    $finder->files();

    $finder->directories();

Si vous voulez suivre les liens, utilisez la méthode ``followLinks()``::

    $finder->files()->followLinks();

Par défaut, l'Iterator ignore les populaires fichiers VCS. Cela peut être
annulé par la méthode ``ignoreVCS()``::

    $finder->ignoreVCS(false);

Tris
~~~~

Triez le résultat par nom ou par type (d'abord les répertoires, ensuite les
fichiers)::

    $finder->sortByName();

    $finder->sortByType();

.. note::
    
    Notez bien que la méthode ``sort*`` nécessite d'obtenir tous les éléments
    correspondants pour bien fonctionner. Pour des Iterators trop larges, c'est
    trop lent.

Vous pouvez aussi définir votre propre algorithme avec la méthode ``sort()`` ::

    $sort = function (\SplFileInfo $a, \SplFileInfo $b)
    {
        return strcmp($a->getRealpath(), $b->getRealpath());
    };

    $finder->sort($sort);

Nom de fichier
~~~~~~~~~~~~~~

Restreignez les fichiers selon leur nom à l'aide de la méthode
:method:`Symfony\\Component\\Finder\\Finder::name`::

    $finder->files()->name('*.php');

La méthode ``name()`` accepte les globs, les strings ou les regexes::

    $finder->files()->name('/\.php$/');

La méthode ``notNames()`` exclut les fichiers correspondants à un certain
critère::

    $finder->files()->notName('*.rb');

Poids de Fichier
~~~~~~~~~~~~~~~~

Restreignez les fichiers selon leur poids à l'aide de la méthode
:method:`Symfony\\Component\\Finder\\Finder::size`::

    $finder->files()->size('< 1.5K');

Restreignez selon une fourchette de poids en enchaînant les appels::

    $finder->files()->size('>= 1K')->size('<= 2K');

Les opérateurs de comparaison valables sont ``>``, ``>=``, ``<``, ``<=`` et
``==``.

La valeur cible peut utiliser des grandeurs de kilobytes (``k``, ``ki``),
megabytes (``m``, ``mi``), ou gigabytes (``g``, ``gi``). Ceux suffixés par un
``i`` utilisent la version appropriée ``2**n`` conformément au `standard IEC`_.

Date de Fichier
~~~~~~~~~~~~~~~

Restreignez les fichiers par leur date de dernière modification à l'aide de la
méthode :method:`Symfony\\Component\\Finder\\Finder::date`::

    $finder->date('since yesterday');

Les opérateurs de comparaison valables sont ``>``, ``>=``, ``<``, ``<=`` et ``==``.
Vous pouvez aussi utiliser ``since`` ou  ``after`` comme alias de ``>`` et
``until`` ou ``before`` comme alias de ``<``.

La valeur cible peut être n'importe quelle date supportée par la fonction PHP
`strtotime`_.

Niveaux de Répertoires
~~~~~~~~~~~~~~~~~~~~~~

Par défaut, le Finder sonde les répertoires récursivement. Restreignez la
profondeur du sondage à l'aide de la méthode
:method:`Symfony\\Component\\Finder\\Finder::depth`::

    $finder->depth('== 0');
    $finder->depth('< 3');

Filtrage personnalisé
~~~~~~~~~~~~~~~~~~~~~

Pour restreindre un fichier correspondant à votre propre stratégie, utilisez la
méthode
:method:`Symfony\\Component\\Finder\\Finder::filter`::

    $filter = function (\SplFileInfo $file)
    {
        if (strlen($file) > 10) {
            return false;
        }
    };

    $finder->files()->filter($filter);

La méthode ``filter()`` prend un Closure comme argument. A chaque fichier
correspondant, il est appelé avec le fichier comme instance de
:phpclass:`SplFileInfo`. Le fichier est exclut du lot de résultats si le Closure
retourne ``false``.

.. _strtotime:    http://www.php.net/manual/en/datetime.formats.php
.. _Iterator:     http://www.php.net/manual/en/spl.iterators.php
.. _protocole:    http://www.php.net/manual/en/wrappers.php
.. _Streams:      http://www.php.net/streams
.. _standard IEC: http://physics.nist.gov/cuu/Units/binary.html
