.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

Choice
======

S'assure que la valeur figure bel et bien parmi un ou plusieurs choix d'une
liste donnée.

.. code-block:: yaml

    properties:
        gender:
            - Choice: [male, female]

Options
-------

* ``choices`` (**par défaut**, required): les choix disponibles
* ``callback``: peut être utilisée au lieu de ``choices``. La méthode statique callback retourne les choix. Si vous mentionnez une chaine de caractères, il est attendu que le nom de la méthode statique soit une classe de validation.
* ``multiple``: autorise les choix multiples. Par défaut: ``false``
* ``min``: le nombre mininum de choix sélectionnables
* ``max``: le nombre maximum de choix sélectionnables
* ``message``: le message d'erreur lorsque la validation échoue
* ``minMessage``: le message d'erreur si la validation ``min`` échoue
* ``maxMessage``: le message d'erreur si la validation ``max`` échoue

Exemple 1: Choix en tant que tableau statique
---------------------------------------------

Si les choix sont peu nombreux et faciles à déterminer, ils peuvent être
communiqués à la définition de contraintes comme un tableau.

.. configuration-block::

    .. code-block:: yaml

        # Sensio/HelloBundle/Resources/config/validation.yml
        Sensio\HelloBundle\Author:
            properties:
                gender:
                    - Choice: [male, female]

    .. code-block:: xml

        <!-- Sensio/HelloBundle/Resources/config/validation.xml -->
        <class name="Sensio\HelloBundle\Author">
            <property name="gender">
                <constraint name="Choice">
                    <value>male</value>
                    <value>female</value>
                </constraint>
            </property>
        </class>

    .. code-block:: php-annotations

        // Sensio/HelloBundle/Author.php
        class Author
        {
            /**
             * @validation:Choice({"male", "female"})
             */
            protected $gender;
        }

    .. code-block:: php

        // Sensio/HelloBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\Choice;
        
        class Author
        {
            protected $gender;
            
            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('gender', new Choice(array('male', 'female')));
            }
        }

Exemple 2: Choix depuis un callback
-----------------------------------

Quand vous avez aussi besoin de choix dans d'autres contextes (tels qu'une
balise ``select`` dans un formulaire), il est plus flexible de les lier à votre
modèle de domaine en utilisant une méthode statique callback.

.. code-block:: php

    // Sensio/HelloBundle/Author.php
    class Author
    {
        public static function getGenders()
        {
            return array('male', 'female');
        }
    }

Vous pouvez mentionner le nom de cette méthode à l'option ``callback`` de votre
contrainte ``Choice``.

.. configuration-block::

    .. code-block:: yaml

        # Sensio/HelloBundle/Resources/config/validation.yml
        Sensio\HelloBundle\Author:
            properties:
                gender:
                    - Choice: { callback: getGenders }

    .. code-block:: xml

        <!-- Sensio/HelloBundle/Resources/config/validation.xml -->
        <class name="Sensio\HelloBundle\Author">
            <property name="gender">
                <constraint name="Choice">
                    <option name="callback">getGenders</option>
                </constraint>
            </property>
        </class>

    .. code-block:: php-annotations

        // Sensio/HelloBundle/Author.php
        class Author
        {
            /**
             * @validation:Choice(callback = "getGenders")
             */
            protected $gender;
        }

Si le callback statique est stocké dans une classe différente, par exemple
``Util``, vous pouvez mentionner le nom de la classe et la méthode en tant que
tableau.

.. configuration-block::

    .. code-block:: yaml

        # Sensio/HelloBundle/Resources/config/validation.yml
        Sensio\HelloBundle\Author:
            properties:
                gender:
                    - Choice: { callback: [Util, getGenders] }

    .. code-block:: xml

        <!-- Sensio/HelloBundle/Resources/config/validation.xml -->
        <class name="Sensio\HelloBundle\Author">
            <property name="gender">
                <constraint name="Choice">
                    <option name="callback">
                        <value>Util</value>
                        <value>getGenders</value>
                    </option>
                </constraint>
            </property>
        </class>

    .. code-block:: php-annotations

        // Sensio/HelloBundle/Author.php
        class Author
        {
            /**
             * @validation:Choice(callback = {"Util", "getGenders"})
             */
            protected $gender;
        }
