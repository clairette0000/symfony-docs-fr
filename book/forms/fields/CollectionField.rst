CollectionField
===============

``CollectionField`` est un groupe de champs spéciaux pour manipuler des
tableaux ou des objets qui implémentent l'interface ``Traversable``. Pour faire
une démonstration, nous allons étendre la classe ``Customer`` pour stocker trois
adresses courrielles::

    class Customer
    {
        // other properties ...

        public $emails = array('', '', '');
    }

Nous allons maintenant ajouter un ``CollectionField`` pour manipuler trois
adresses::

    use Symfony\Component\Form\CollectionField;

    $form->add(new CollectionField('emails', array(
        'prototype' => new EmailField(),
    )));

Si vous désignez l'option "modifiable" à ``true``, vous pouvez même ajouter ou
supprimer des colonnes de la collection via javascript! Le ``CollectionField``
vous avertira et redimensionnera le tableau sous-jacent en conséquence.
