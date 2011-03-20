.. index::
   pair: Doctrine; Migrations

Migrations
==========

L'outil de migration de bases de données est une extention de la couche d'abstraction
et vous offre la possibilité de programmer puis déployer de nouvelles versions 
de votre schéma de base de données de ma manière sécurisée et standardisée.

.. tip::
   
    Vous pouvez en savoir plus sur les migrations Doctrine en consultant la
    `documentation`_ du projet.

Toutes les fonctionnalités liées aux migrations tiennent dans quelques commandes:

.. code-block:: bash

    doctrine:migrations
      :diff     Génère une nouvelle migration en comparant votre base de données 
actuelle et vos informations de mapping.
      :execute  Exécute une seule migration (upgrade ou downgrade) manuellement.
      :generate Génère une classe de migration vide.
      :migrate  Exécute une migration vers une version spécifique ou vers la dernière version.
      :status   Affiche le status d'un lot de migrations.
      :version  Ajoute ou supprime manuellement des versions de migration de la 
table des versions.

Chaque bundle gère ses propres migrations. Lorsque vous travaillez avec l'une des
commandes ci-dessus, vous devez spécifier le bundle qui est concerné. Par exemple,
pour voir le status des migrations d'un bundle, exécutez la commande ``status``:

.. code-block:: bash

    $ php app/console doctrine:migrations:status

     == Configuration

        >> Name:                                               HelloBundle Migrations
        >> Configuration Source:                               manually configured
        >> Version Table Name:                                 hello_bundle_migration_versions
        >> Migrations Namespace:                               Application\Migrations
        >> Migrations Directory:                               /path/to/symfony-sandbox/app/DoctrineMigrations
        >> Current Version:                                    0
        >> Latest Version:                                     0
        >> Executed Migrations:                                0
        >> Available Migrations:                               0
        >> New Migrations:                                     0

Maintenant, nous pouvons commencer à travailler avec les migrations en générant
une nouvelle classe de migration vide:

.. code-block:: bash

    $ php app/console doctrine:migrations:generate
    Generated new migration class to "/path/to/project/app/DoctrineMigrations/Version20100621140655.php"

.. tip::

    Vous devrez créer le dossier ``/path/to/project/app/DoctrineMigrations``
    avant de lancer la commande ``doctrine:migrations:generate``.

Jetez un oeil à la classe de migration nouvellement générée et vous verrez 
quelque chose qui ressemble à::

    namespace Application\Migrations;

    use Doctrine\DBAL\Migrations\AbstractMigration,
        Doctrine\DBAL\Schema\Schema;

    class Version20100621140655 extends AbstractMigration
    {
        public function up(Schema $schema)
        {

        }

        public function down(Schema $schema)
        {

        }
    }

Si vous lancier la commande ``status``, elle vous dira que vous avez une nouvelle
migration à exécuter:

.. code-block:: bash

    $ php app/console doctrine:migrations:status

     == Configuration

       >> Name:                                               HelloBundle Migrations
       >> Configuration Source:                               manually configured
       >> Version Table Name:                                 hello_bundle_migration_versions
       >> Migrations Namespace:                               Application\Migrations
       >> Migrations Directory:                               /path/to/symfony-sandbox/app/DoctrineMigrations
       >> Current Version:                                    0
       >> Latest Version:                                     2010-06-21 14:06:55 (20100621140655)
       >> Executed Migrations:                                0
       >> Available Migrations:                               1
       >> New Migrations:                                     1

    == Migration Versions

       >> 2010-06-21 14:06:55 (20100621140655)                not migrated

Maintenant, vous pouvez ajouter du code aux méthodes ``up()`` et ``down()`` et 
lancer la migration:

.. code-block:: bash

    $ php app/console doctrine:migrations:migrate

.. _documentation: http://www.doctrine-project.org/docs/migrations/2.0/en
