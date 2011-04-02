.. index::
   single: Configuration; Doctrine DBAL
   single: Doctrine; DBAL configuration

Configuration
=============

Un example de configuration avec une base de données MySQL pourrait ressembler
au code suivant:

.. code-block:: yaml

    # app/config/config.yml
    doctrine:
        dbal:
            driver:   pdo_mysql
            dbname:   Symfony2
            user:     root
            password: null

DoctrineBundle supporte tous les paramètres ques les pilotes par défaut de Doctrine
acceptent, convertis aux normes XML ou YAML que Symfony applique.
Consultez la `documentation`_  de Doctrine DBAL pour plus d'informations. 
En plus de cela, vous pouvez configurer certaines options liées à Symfony. 
Le bloc suivant montre toutes les clés de paramètre possibles, sans expliquer pour
l'instant leur signification:

.. configuration-block::

    .. code-block:: yaml

        doctrine:
            dbal:
                dbname:               database
                host:                 localhost
                port:                 1234
                user:                 user
                password:             secret
                driver:               pdo_mysql
                driver_class:         MyNamespace\MyDriverImpl
                options:
                    foo: bar
                path:                 %kernel.data_dir%/data.sqlite
                memory:               true
                unix_socket:          /tmp/mysql.sock
                wrapper_class:        MyDoctrineDbalConnectionWrapper
                charset:              UTF8
                logging:              %kernel.debug%
                platform_service:     MyOwnDatabasePlatformService

    .. code-block:: xml

        <!-- xmlns:doctrine="http://symfony.com/schema/dic/doctrine" -->
        <!-- xsi:schemaLocation="http://symfony.com/schema/dic/doctrine http://symfony.com/schema/dic/doctrine/doctrine-1.0.xsd"> -->

        <doctrine:config>
            <doctrine:dbal
                dbname="database"
                host="localhost"
                port="1234"
                user="user"
                password="secret"
                driver="pdo_mysql"
                driver-class="MyNamespace\MyDriverImpl"
                path="%kernel.data_dir%/data.sqlite"
                memory="true"
                unix-socket="/tmp/mysql.sock"
                wrapper-class="MyDoctrineDbalConnectionWrapper"
                charset="UTF8"
                logging="%kernel.debug%"
                platform-service="MyOwnDatabasePlatformService"
            />
        </doctrine:config>

Il existe également un certain nombre de paramètres lié à l'injecteur de dépendance
qui vous permettent de spécifier quelles classes sont utilisées (avec leurs valeurs
par défaut):

.. code-block:: yaml

    parameters:
        doctrine.dbal.logger_class: Symfony\Bundle\DoctrineBundle\Logger\DbalLogger
        doctrine.dbal.configuration_class: Doctrine\DBAL\Configuration
        doctrine.data_collector.class: Symfony\Bundle\DoctrineBundle\DataCollector\DoctrineDataCollector
        doctrine.dbal.event_manager_class: Doctrine\Common\EventManager
        doctrine.dbal.events.mysql_session_init.class: Doctrine\DBAL\Event\Listeners\MysqlSessionInit
        doctrine.dbal.events.oracle_session_init.class: Doctrine\DBAL\Event\Listeners\OracleSessionInit
        doctrine.dbal.logging: %kernel.debug%

Si vous voulez configurer plusieurs connexions, vous pouvez le faire très 
simplement en les listant sous la clé de paramètre ``connections``. 
Tous les paramètres spécifiés ci-dessus peuvent aussi être spécifiés ici.

.. code-block:: yaml

    doctrine:
        dbal:
            default_connection:       default
            connections:
                default:
                    dbname:           Symfony2
                    user:             root
                    password:         null
                    host:             localhost
                customer:
                    dbname:           customer
                    user:             root
                    password:         null
                    host:             localhost

Si vous avez défini plusieurs connexions, vous pouvez tout aussi bien utiliser 
``$this->get('doctrine.dbal.[connectionname]_connection)``, mais vous devez
passer en argument le nom de la connexion

.. code-block:: php

    class UserController extends Controller
    {
        public function indexAction()
        {
            $defaultConn1 = $this->get('doctrine.dbal.connection');
            $defaultConn2 = $this->get('doctrine.dbal.default_connection');
            // $defaultConn1 === $defaultConn2

            $customerConn = $this->get('doctrine.dbal.customer_connection');
        }
    }

.. _documentation: http://www.doctrine-project.org/docs/dbal/2.0/en
