Le Validator
=============

La validation est une tâche commune aux applications web. Les données saisies
dans des formulaires nécessitent d'être validées. Les données ont aussi besoin
d'être validées avant des les enregistrer dans une base de données ou d'être
transmises à un service web.

Symfony2 est livré avec un composant Validator qui rend cette tâche facile. Ce
composant est basé sur la `spécification JSR303 Bean Validation`_. Kesako? Une
spécification de Java pour PHP? Vous avez tapé dans le mille mais ce n'est pas
aussi effroyable que ça ne le paraît. Jetons un coup d'œil sur la manière de
l'aborder avec PHP.

Le Validator valide les objets selon des :doc:`constraintes <constraints>`.
Démarrons avec une contrainte toute simple qui a une propriété ``$name`` dans la
classe ``Author`` qui ne doit jamais être laissée vide::

    // Sensio/HelloBundle/Author.php
    class Author
    {
        private $name;
    }

La liste suivante dévoile la configuration qui relie les propriétés de la classe
avec des contraintes; ce processus est qualifié de "mapping":

.. configuration-block::

    .. code-block:: yaml

        # Sensio/HelloBundle/Resources/config/validation.yml
        Sensio\HelloBundle\Author:
            properties:
                name:
                    - NotBlank: ~

    .. code-block:: xml

        <!-- Sensio/HelloBundle/Resources/config/validation.xml -->
        <class name="Sensio\HelloBundle\Author">
            <property name="name">
                <constraint name="NotBlank" />
            </property>
        </class>

    .. code-block:: php-annotations

        // Sensio/HelloBundle/Author.php
        class Author
        {
            /**
             * @validation:NotBlank()
             */
            private $name;
        }

    .. code-block:: php

        // Sensio/HelloBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Components\Validator\Constraints\NotBlank;

        class Author
        {
            private $name;
            
            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('name', new NotBlank());
            }
        }

Enfin, nous pouvons utiliser la classe :class:`Symfony\\Component\\Validator\\Validator`
de :doc:`validation <validation>`. Pour utiliser le validateur par défaut de
Symfony2, adaptez la configuration de votre application ainsi:

.. code-block:: yaml

    # hello/config/config.yml
    framework:
        validation:
            enabled: true

A présent, appellez la méthode ``validate()`` comme un service ce qui délivrera
une liste d'erreurs si la validation échoue.

.. code-block:: php

    $validator = $container->get('validator');
    $author = new Author();

    print $validator->validate($author);

Puisque la propriété ``$name`` est vide, vous devriez observer le message
d'erreur suivant:

.. code-block:: text

    Sensio\HelloBundle\Author.name:
        This value should not be blank

Insérez une valeur à cette propriété et le message d'erreur disparaîtra.

.. _spécification JSR303 Bean Validation: http://jcp.org/en/jsr/detail?id=303
