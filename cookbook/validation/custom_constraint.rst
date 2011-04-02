.. index::
   single: Validation; Constraintes personnalisées

Comment puis-je personnaliser une Contrainte de Validation?
-----------------------------------------------------------

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

En d'autres termes, si vous créez une ``Constraint`` personnalisée (ex:
``MyConstraint``), Symfony2 cherchera automatiquement une autre classe,
``MyConstraintValidator`` quand vous procédez normalement à une validation.

La classe de validation est aussi simple et n'a seulement qu'une seule méthode
obligatoire: ``isValid``. Prenons le ``NotBlankValidator`` est un exemple:

.. code-block:: php

    class NotBlankValidator extends ConstraintValidator
    {
        public function isValid($value, Constraint $constraint)
        {
            if (null === $value || '' === $value) {
                $this->setMessage($constraint->message);

                return false;
            }

            return true;
        }
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
