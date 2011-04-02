.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

Valid
=====

S'assure qu'un objet associé est lui-même validé.

.. code-block:: yaml

    properties:
        address:
            - Valid: ~

Exemple: Validation d'un ObjectGraph
------------------------------------

Cette contrainte aide à valider un ObjectGraph entier. Dans l'exemple suivant,
nous avons créé deux classes ``Author`` et ``Address`` qui ont toutes deux des
contraintes sur leurs propriétés. En outre, ``Author`` stocke une instance
d'``Address`` dans la propriété ``$address``.

.. code-block:: php

    // Sensio/HelloBundle/Address.php
    class Address
    {
        protected $street;
        protected $zipCode;
    }

.. code-block:: php

    // Sensio/HelloBundle/Author.php
    class Author
    {
        protected $firstName;
        protected $lastName;
        protected $address;
    }

.. configuration-block::

    .. code-block:: yaml

        # Sensio/HelloBundle/Resources/config/validation.yml
        Sensio\HelloBundle\Address:
            properties:
                street:
                    - NotBlank: ~
                zipCode:
                    - NotBlank: ~
                    - MaxLength: 5

        Sensio\HelloBundle\Author:
            properties:
                firstName:
                    - NotBlank: ~
                    - MinLength: 4
                lastName:
                    - NotBlank: ~

    .. code-block:: xml

        <!-- Sensio/HelloBundle/Resources/config/validation.xml -->
        <class name="Sensio\HelloBundle\Address">
            <property name="street">
                <constraint name="NotBlank" />
            </property>
            <property name="zipCode">
                <constraint name="NotBlank" />
                <constraint name="MaxLength">5</constraint>
            </property>
        </class>

        <class name="Sensio\HelloBundle\Author">
            <property name="firstName">
                <constraint name="NotBlank" />
                <constraint name="MinLength">4</constraint>
            </property>
            <property name="lastName">
                <constraint name="NotBlank" />
            </property>
        </class>

    .. code-block:: php-annotations

        // Sensio/HelloBundle/Address.php
        class Address
        {
            /**
             * @validation:NotBlank()
             */
            protected $street;

            /**
             * @validation:NotBlank
             * @validation:MaxLength(5)
             */
            protected $zipCode;
        }

        // Sensio/HelloBundle/Author.php
        class Author
        {
            /**
             * @validation:NotBlank
             * @validation:MinLength(4)
             */
            protected $firstName;

            /**
             * @validation:NotBlank
             */
            protected $lastName;
            
            protected $address;
        }

    .. code-block:: php

        // Sensio/HelloBundle/Address.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\NotBlank;
        use Symfony\Component\Validator\Constraints\MaxLength;
        
        class Address
        {
            protected $street;

            protected $zipCode;
            
            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('street', new NotBlank());
                $metadata->addPropertyConstraint('zipCode', new NotBlank());
                $metadata->addPropertyConstraint('zipCode', new MaxLength(5));
            }
        }

        // Sensio/HelloBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\NotBlank;
        use Symfony\Component\Validator\Constraints\MinLength;
        
        class Author
        {
            protected $firstName;

            protected $lastName;
            
            protected $address;
            
            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('firstName', new NotBlank());
                $metadata->addPropertyConstraint('firstName', new MinLength(4));
                $metadata->addPropertyConstraint('lastName', new NotBlank());
            }
        }

Avec ce "mapping", il est possible de valider avec succès un auteur qui a
une adresse invalide. Pour se prémunir de cela, nous ajoutons une contrainte
``Valid`` dans la propriété ``$address``.

.. configuration-block::

    .. code-block:: yaml

        # Sensio/HelloBundle/Resources/config/validation.yml
        Sensio\HelloBundle\Author:
            properties:
                address:
                    - Valid: ~

    .. code-block:: xml

        <!-- Sensio/HelloBundle/Resources/config/validation.xml -->
        <class name="Sensio\HelloBundle\Author">
            <property name="address">
                <constraint name="Valid" />
            </property>
        </class>

    .. code-block:: php-annotations

        // Sensio/HelloBundle/Author.php
        class Author
        {
            /* ... */
            
            /**
             * @validation:Valid
             */
            protected $address;
        }

    .. code-block:: php

        // Sensio/HelloBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\Valid;
        
        class Author
        {
            protected $address;
            
            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('address', new Valid());
            }
        }

Si vous validez un auteur qui a une adresse invalide à présent, vous devriez
observer que la validation du champs ``Address`` échoue.

    Sensio\HelloBundle\Author.address.zipCode:
        This value is too long. It should have 5 characters or less
