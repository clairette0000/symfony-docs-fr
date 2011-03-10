AssertTrue
==========

S'assure que la valeur est ``true``.

.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

.. code-block:: yaml

    properties:
        termsAccepted:
            - AssertTrue: ~

Options
-------

* ``message``: le message d'erreur lorsque la validation échoue

Exemple
-------

Cette contrainte est très utile quand on exécute une logique de validation
personnalisée. Vous pouvez mettre votre logique dans une méthode qui retournera
soit ``true`` soit ``false``.

.. code-block:: php

    // Sensio/HelloBundle/Author.php
    class Author
    {
        protected $token;

        public function isTokenValid()
        {
            return $this->token == $this->generateToken();
        }
    }

Puis vous pouvez contraindre cette méthode à l'aide de ``AssertTrue``.

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
            <getter name="tokenValid">
                <constraint name="True">
                    <option name="message">The token is invalid</option>
                </constraint>
            </getter>
        </class>

    .. code-block:: php-annotations

        // Sensio/HelloBundle/Author.php
        class Author
        {
            protected $token;

            /**
             * @validation:AssertTrue(message = "The token is invalid")
             */
            public function isTokenValid()
            {
                return $this->token == $this->generateToken();
            }
        }

    .. code-block:: php

        // Sensio/HelloBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\AssertTrue;
        
        class Author
        {
            protected $token;
            
            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addGetterConstraint('tokenValid', new AssertTrue(array(
                    'message' => 'The token is invalid',
                )));
            }

            public function isTokenValid()
            {
                return $this->token == $this->generateToken();
            }
        }

Si la validation de cette méthode échoue, vous devrez voir un message similaire
à ceci:

.. code-block:: text

    Sensio\HelloBundle\Author.tokenValid:
        This value should not be null
