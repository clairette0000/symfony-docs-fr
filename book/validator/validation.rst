Contrainte de Validation
========================

Les objets avec des contraintes sont validées par la classe
:class:`Symfony\\Component\\Validator\\Validator`. Si vous utilisez Symfony2,
cette classe est déjà enregistrée en tant que service dans le conteneur
d'Injection de Dépendances. Pour activer ce service, ajoutez les lignes
suivantes à votre configuration:

.. code-block:: yaml

    # hello/config/config.yml
    framework:
        validation:
            enabled: true

Puis vous pouvez bénéficier de la validation depuis le conteneur et commencer à
valider vos objets:

.. code-block:: php

    $validator = $container->get('validator');
    $author = new Author();

    print $validator->validate($author);

La méthode ``validate()`` retourne un objet
:class:`Symfony\\Component\\Validator\\ConstraintViolationList`. Cet objet se
comporte exactement comme un tableau. Vous pouvez le parcourir et vous pouvez
même l'afficher avec un format tout à fait conforme. Chaque élément de la liste
correspond à une erreur de validation. Si cette liste est vide, vous pouvez
extérioriser des cris de joie car la validation s'est déroulée sans encombre.

L'appel ci-dessus va afficher quelque chose semblable à ceci:

.. code-block:: text

    Sensio\HelloBundle\Author.firstName:
        This value should not be blank
    Sensio\HelloBundle\Author.lastName:
        This value should not be blank
    Sensio\HelloBundle\Author.fullName:
        This value is too short. It should have 10 characters or more

Si vous remplissez l'objet avec uniquement des valeurs conformes, les erreurs de
valeurs disparaîtront.
