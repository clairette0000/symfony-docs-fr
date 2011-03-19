.. index::
   pair: Doctrine; DBAL

DBAL
====

`Doctrine`_ Database Abstraction Layer (DBAL) est une couche d'abstraction qui
se trouve par dessus `PDO`_ et qui fournit une API flexible et intuitive afin de 
communiquer avec les bases de données relationnelles les plus populaires qui 
existent aujourd'hui!

.. tip::

    Vous pouvez consulter la `documentation`_ officielle afin d'en savoir plus 
    sur Doctrine DBAL.

Pour commencer, vous avez juste besoin d'activer et de configurer DBAL:

.. code-block:: yaml

    # app/config/config.yml

    doctrine:
        dbal:
            driver:   pdo_mysql
            dbname:   Symfony2
            user:     root
            password: null

Vous pouvez accéder à la connexion Doctrine DBAL en accédant au service
``database_connection``:

.. code-block:: php

    class UserController extends Controller
    {
        public function indexAction()
        {
            $conn = $this->get('database_connection');

            $users = $conn->fetchAll('SELECT * FROM users');
        }
    }

.. _PDO:           http://www.php.net/pdo
.. _documentation: http://www.doctrine-project.org/docs/dbal/2.0/en
.. _Doctrine:      http://www.doctrine-project.org
