.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

Collection
==========

S'assure que des entrées d'un tableau répondent favorablement à différentes
contraintes.

.. code-block:: yaml

    - Collection:
        fields:
            key1:
                - NotNull: ~
            key2:
                - MinLength: 10

Options
-------

* ``fields`` (obligatoire): un tableau associatif de clés avec une ou plusieurs contraintes
* ``allowMissingFields``: autorise ou non que certaines clés soient absentes du tableau. Par défaut: ``false``
* ``allowExtraFields``: autorise ou non que le tableau dispose de certaines clés absentes de l'option ``fields``. Par défaut: ``false``
* ``missingFieldsMessage``: le message d'erreur lorsque la validation de ``allowMissingFields`` échoue
* ``allowExtraFields``: le message d'erreur lorsque la validation de ``allowExtraFields`` échoue

Exemple:
--------

Validons ensemble un tableau avec deux indexes ``firstName`` et ``lastName``. La
valeur de ``firstName`` ne peut être vide puisque la valeur de ``lastName`` ne
doit pas être vide avec une longueur de chaine mininum de quatre caractères. En
outre, ce lot de clés peut être absent dans un tableau.

.. configuration-block::

    .. code-block:: yaml

        # Sensio/HelloBundle/Resources/config/validation.yml
        Sensio\HelloBundle\Author:
            properties:
                options:
                    - Collection:
                        fields:
                            firstName:
                                - NotBlank: ~
                            lastName:
                                - NotBlank: ~
                                - MinLength: 4
                        allowMissingFields: true

    .. code-block:: xml

        <!-- Sensio/HelloBundle/Resources/config/validation.xml -->
        <class name="Sensio\HelloBundle\Author">
            <property name="options">
                <constraint name="Collection">
                    <option name="fields">
                        <value key="firstName">
                            <constraint name="NotNull" />
                        </value>
                        <value key="lastName">
                            <constraint name="NotNull" />
                            <constraint name="MinLength">4</constraint>
                        </value>
                    </option>
                    <option name="allowMissingFields">true</option>
                </constraint>
            </property>
        </class>

    .. code-block:: php-annotations

        // Sensio/HelloBundle/Author.php
        class Author
        {
            /**
             * @validation:Collection(
             *   fields = {
             *     "firstName" = @validation:NotNull(),
             *     "lastName" = { @validation:NotBlank(), @validation:MinLength(4) }
             *   },
             *   allowMissingFields = true
             * )
             */
            private $options = array();
        }

    .. code-block:: php

        // Sensio/HelloBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\Collection;
        use Symfony\Component\Validator\Constraints\NotNull;
        use Symfony\Component\Validator\Constraints\NotBlank;
        use Symfony\Component\Validator\Constraints\MinLength;
        
        class Author
        {
            private $options = array();
            
            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('options', new Collection(array(
                    'fields' => array(
                        'firstName' => new NotNull(),
                        'lastName' => array(new NotBlank(), new MinLength(4)),
                    ),
                    'allowMissingFields' => true,
                )));
            }
        }

L'object suivant échouera lors de la validation:

.. code-block:: php

    $author = new Author();
    $author->options['firstName'] = null;
    $author->options['lastName'] = 'foo';

    print $validator->validate($author);

Vous devriez voir les messages d'erreurs suivants:

.. code-block:: text

    Sensio\HelloBundle\Author.options[firstName]:
        This value should not be null
    Sensio\HelloBundle\Author.options[lastName]:
        This value is too short. It should have 4 characters or more
