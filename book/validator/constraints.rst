.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

Contraintes
===========

Le Validator est conçu pour valider des objets selon des *contraintes*.
Dans la vie quotidienne, une contrainte peut être: "Le gâteau ne doit pas être
carbonisé". Dans Symfony2, les contraintes sont similaires: ce sont des
affirmations selon lesquelles une condition est vraie.

Contraintes supportées
----------------------

Les contraintes suivantes sont disponibles nativement dans Symfony2:

.. toctree::
    :hidden:

    constraints/index

* :doc:`AssertFalse <constraints/AssertFalse>`
* :doc:`AssertTrue <constraints/AssertTrue>`
* :doc:`AssertType <constraints/AssertType>`
* :doc:`Choice <constraints/Choice>`
* :doc:`Collection <constraints/Collection>`
* :doc:`Date <constraints/Date>`
* :doc:`DateTime <constraints/DateTime>`
* :doc:`Email <constraints/Email>`
* :doc:`File <constraints/File>`
* :doc:`Max <constraints/Max>`
* :doc:`MaxLength <constraints/MaxLength>`
* :doc:`Min <constraints/Min>`
* :doc:`MinLength <constraints/MinLength>`
* :doc:`NotBlank <constraints/NotBlank>`
* :doc:`NotNull <constraints/NotNull>`
* :doc:`Regex <constraints/Regex>`
* :doc:`Time <constraints/Time>`
* :doc:`Url <constraints/Url>`
* :doc:`Valid <constraints/Valid>`

La raison d'être des Contraintes
--------------------------------

Les contraintes peut être insérées dans les propriétés de votre classe, dans les
getters publics ou au sein même d'une classe. L'avantage des contraintes de
classe est qu'ils peuvent valider l'état global d'un objet en une seule fois
avec toutes les propriétés et méthodes.

Propriétés
~~~~~~~~~~

La validation des propriétés d'une classe est la technique de validation
élémentaire. Symfony2 vous autorise à valider les propriétés privées, protégées
ou publiques. La liste suivante vous dévoile comment configurer les propriétés
``$firstName`` et ``$lastName`` d'une classe ``Author`` qui a au moins 3
caractères.

.. configuration-block::

    .. code-block:: yaml

        # Sensio/HelloBundle/Resources/config/validation.yml
        Sensio\HelloBundle\Author:
            properties:
                firstName:
                    - NotBlank: ~
                    - MinLength: 3
                lastName:
                    - NotBlank: ~
                    - MinLength: 3

    .. code-block:: xml

        <!-- Sensio/HelloBundle/Resources/config/validation.xml -->
        <class name="Sensio\HelloBundle\Author">
            <property name="firstName">
                <constraint name="NotBlank" />
                <constraint name="MinLength">3</constraint>
            </property>
            <property name="lastName">
                <constraint name="NotBlank" />
                <constraint name="MinLength">3</constraint>
            </property>
        </class>

    .. code-block:: php-annotations

        // Sensio/HelloBundle/Author.php
        class Author
        {
            /**
             * @validation:NotBlank()
             * @validation:MinLength(3)
             */
            private $firstName;

            /**
             * @validation:NotBlank()
             * @validation:MinLength(3)
             */
            private $lastName;
        }

    .. code-block:: php

        // Sensio/HelloBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\NotBlank;
        use Symfony\Component\Validator\Constraints\MinLength;
        
        class Author
        {
            private $firstName;

            private $lastName;

            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('firstName', new NotBlank());
                $metadata->addPropertyConstraint('firstName', new MinLength(3));
                $metadata->addPropertyConstraint('lastName', new NotBlank());
                $metadata->addPropertyConstraint('lastName', new MinLength(3));
            }
        }

Getters
~~~~~~~

La technique de validation suivante est de restreindre la valeur retournée par
une méthode. Symfony2 vous autorise à contraindre toute méthode publique dont le
nom commence par "get" ou "is". Dans ce chapitre, ils sont communément surnommés
"getters".

L'avantage de cette technique est que cela autorise la validation de votre objet
dynamiquement. Selon l'état de votre objet, la méthode peut retourner
différentes valeurs qui sont ensuite validées.

La liste suivante vous dévoile comment utiliser la contrainte :doc:`AssertTrue
<constraints/AssertTrue>` pour valider si le jeton généré dynamiquement est
correct:

.. configuration-block::

    .. code-block:: yaml

        # Sensio/HelloBundle/Resources/config/validation.yml
        Sensio\HelloBundle\Author:
            getters:
                tokenValid:
                    - AssertTrue: { message: "The token is invalid" }

    .. code-block:: xml

        <!-- Sensio/HelloBundle/Resources/config/validation.xml -->
        <class name="Sensio\HelloBundle\Author">
            <getter property="tokenValid">
                <constraint name="AssertTrue">
                    <option name="message">The token is invalid</option>
                </constraint>
            </getter>
        </class>

    .. code-block:: php-annotations

        // Sensio/HelloBundle/Author.php
        class Author
        {
            /**
             * @validation:AssertTrue(message = "The token is invalid")
             */
            public function isTokenValid()
            {
                // return true or false
            }
        }

    .. code-block:: php

        // Sensio/HelloBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\AssertTrue;
        
        class Author
        {

            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addGetterConstraint('tokenValid', new AssertTrue(array(
                    'message' => 'The token is invalid',
                )));
            }
            
            public function isTokenValid()
            {
                // return true or false
            }
        }

.. note::

    Les plus chevronnés d'entre vous auront remarqué que le préfixe du getter
    ("get " ou "is") est omis dans le mapping. Cela vous permet de déplacer
    la contrainte à une propriété du même nom plus tard (ou vice versa) sans
    changer votre logique de validation.
    
Contraintes personnalisées
--------------------------

Vous pouvez créer une contrainte personnalisée en étendant la classe de base
``constraint`` :class:`Symfony\\Component\\Validator\\Constraint`. Les options
de votre contrainte sont représentées par la propriété publique de la classe
``constraint``. Par exemple, la contrainte ``Url`` inclut les propriétés
``message`` et ``protocoles``.

.. code-block:: php

    namespace Symfony\Component\Validator\Constraints;

    class Url extends \Symfony\Component\Validator\Constraint
    {
        public $message = 'This value is not a valid URL';
        public $protocols = array('http', 'https', 'ftp', 'ftps');
    }

Comme vous pouvez le constater, une classe de contrainte est étonnemment simple.
La validation normale est assurée par une autre classe de "validateur de
contrainte". Celle-ci est spécifiée par la méthode de contrainte
``validatedBy()`` qui inclut quelques logiques basiques par défaut:

.. code-block:: php

    // in the base Symfony\Component\Validator\Constraint class
    public function validatedBy()
    {
        return get_class($this).'Validator';
    }

Validateur de Contraintes avec Dépendances
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Si votre validateur de contraintes détient des dépendances telles qu'une
connexion à une base de données, vous aurez besoin de configurer comme un
service le conteneur d'injection de dépendances. Ce service doit contenir le
tag ``validator.constraint_validator`` et un attribut ``alias``:

.. configuration-block::

    .. code-block:: yaml

        services:
            validator.unique.your_validator_name:
                class: Fully\Qualified\Validator\Class\Name
                tags:
                    - { name: validator.constraint_validator, alias: alias_name }

    .. code-block:: xml

        <service id="validator.unique.your_validator_name" class="Fully\Qualified\Validator\Class\Name">
            <argument type="service" id="doctrine.orm.default_entity_manager" />
            <tag name="validator.constraint_validator" alias="alias_name" />
        </service>

    .. code-block:: php

        $container
            ->register('validator.unique.your_validator_name', 'Fully\Qualified\Validator\Class\Name')
            ->addTag('validator.constraint_validator', array('alias' => 'alias_name'))
        ;

Votre classe de contrainte devrait désormais utiliser un alias pour référencer
le validateur approprié::

    public function validatedBy()
    {
        return 'alias_name';
    }
