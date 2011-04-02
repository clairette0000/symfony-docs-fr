Champs de Formulaire
====================

Un formulaire est composé d'un voire plusieurs champs. Chaque champs est un
objet qui a une implémentation de la classe :class:`Symfony\\Component\\Form\\FormFieldInterface`.
Les champs convertissent les données entre les représentations humaines et
normalisées.

Intéressons nous à ``DateField`` par exemple. Quand votre application stocke des
dates comme des chaines de caractères ou des objets ``DateTime``, les
utilisateurs préfèrent choisir une date dans un calendrier contextuel graphique.
``DateField`` gère leur affichage et effectue la conversion à votre place.

Les Options des Champs de base
------------------------------

Tous les champs intégrés par défaut acceptent un tableau d'options au sein de
leur constructeur. Par commodité, ces champs de base sont une sous-classe de
:class:`Symfony\\Component\\Form\\Field` qui prédéfinissent un couple d'options.

``data``
~~~~~~~~

Quand vous créez un formulaire, chaque champs affiche initialement une valeur
d'une propriété correspondante d'un objet du formulaire en question. Si vous
voulez surcharger cette valeur initiale, vous pouvez le définit dans l'option
``data``.

.. code-block:: php

    use Symfony\Component\Form\HiddenField
    
    $field = new HiddenField('token', array(
        'data' => 'abcdef',
    ));
    
    assert('abcdef' === $field->getData());

.. note::

    Quand vous définissez une option ``data``, le champs n'écrira pas dans
    l'objet en question parce que l'option ``property_path`` sera implicitement
    ``null``. Lisez :ref:`form-field-property_path` pour davantage d'informations.

``required``
~~~~~~~~~~~~

Par défaut, chaque ``Field`` considère qu'une valeur soit obligatoire donc
qu'aucune valeur vide ne peut être soumise. Cette configuration affecte le
comportement et l'affichage de certains champs. Le ``ChoiceField``, par exemple,
inclut un choix vide s'il n'est pas requis.

.. code-block:: php

    use Symfony\Component\Form\ChoiceField
    
    $field = new ChoiceField('status', array(
        'choices' => array('wait' => 'En cours de traitement', 'ok' => 'Prêt'),
        'required' => false,
    ));

``disabled``
~~~~~~~~~~~~

Si vous ne souhaitez pas qu'un utilisateur modifie la valeur d'un champs, vous
pouvez définir l'option ``disabled`` à ``true``. Toute valeur soumise sera
ignorée.

.. code-block:: php

    use Symfony\Component\Form\TextField
    
    $field = new TextField('status', array(
        'data' => 'Ancienne données',
        'disabled' => true,
    ));
    $field->submit('Nouvelles données');
    
    assert('Ancienne données' === $field->getData());

``trim``
~~~~~~~~

De nombreux utilisateurs saisissent des espaces superfétatoires dans des champs
de formulaire. Le framework de formulaire supprime automatiquement ces espaces.
Si vous souhaitez les conserver, attribuez à l'option ``trim`` la valeur ``false``.

.. code-block:: php

    use Symfony\Component\Form\TextField
    
    $field = new TextField('status', array(
        'trim' => false,
    ));
    $field->submit('   Quelque chose   ');
    
    assert('   Quelque chose   ' === $field->getData());

.. _form-field-property_path:

``property_path``
~~~~~~~~~~~~~~~~~

Les champs affichent une valeur de propriété du formulaire de l'objet en
question par défaut. Quand le formulaire est soumis, la valeur soumise est
inscrite à l'intérieur de l'objet.

.. todo: Je laisse à quelqu'un d'autre le soin de traduire ce paragraphe 

If you want to override the property that a field reads from and writes to,
you can set the ``property_path`` option. Its default value is the field's
name.

.. code-block:: php

    use Symfony\Component\Form\Form
    use Symfony\Component\Form\TextField
    
    $author = new Author();
    $author->setFirstName('Votre prénom...');
    
    $form = new Form('author');
    $form->add(new TextField('name', array(
        'property_path' => 'firstName',
    )));
    $form->bind($request, $author);
    
    assert('Votre prénom...' === $form['name']->getData());


Pour un chemin de propriété, la classe du domaine en question nécessite d'avoir

1. Une propriété publique correspondante, ou
2. Un setter ou un getter public préfixé par "set"/"get", suivi du chemin de la propriété.

Les chemins de propriété peuvent aussi indiquer des objets imbriqués en
utilisant des points (``.``).

.. code-block:: php

    use Symfony\Component\Form\Form
    use Symfony\Component\Form\TextField
    
    $author = new Author();
    $author->getEmail()->setAddress('moi@exemple.tld');
    
    $form = new Form('author');
    $form->add(new EmailField('email', array(
        'property_path' => 'email.address',
    )));
    $form->bind($request, $author);
    
    assert('moi@exemple.tld' === $form['email']->getData());

Vous pouvez aussi indiquer des entrées de tableaux imbriqués ou d'objets en
implémentant ``\Traversable`` en utilisant des crochets.

.. code-block:: php

    use Symfony\Component\Form\Form
    use Symfony\Component\Form\TextField
    
    $author = new Author();
    $author->setEmails(array(0 => 'moi@exemple.tld'));
    
    $form = new Form('author');
    $form->add(new EmailField('email', array(
        'property_path' => 'emails[0]',
    )));
    $form->bind($request, $author);
    
    assert('moi@exemple.tld' === $form['email']->getData());

Si le chemin de la propriété est ``null``, le champs ne sera ni lu ni écrit par
l'objet en question. Cela est utile si vous voulez avoir des champs avec des
valeurs fixes.

.. code-block:: php

    use Symfony\Component\Form\HiddenField
    
    $field = new HiddenField('token', array(
        'data' => 'abcdef',
        'property_path' => null,
    ));

Puisqu'il s'agit d'un scénario commun, ``property_path`` est toujours ``null``
si vous établissez une option ``data``. Le dernier code d'exemple peut ainsi
être simplifié:

.. code-block:: php

    use Symfony\Component\Form\HiddenField
    
    $field = new HiddenField('token', array(
        'data' => 'abcdef',
    ));

.. note::

    Si vous voulez définir une valeur par défaut personnalisée mais encore
    écrite à l'objet en question, vous devez invoquer ``property_path``
    manuellement.
    
    .. code-block:: php

        use Symfony\Component\Form\TextField
        
        $field = new TextField('name', array(
            'data' => 'Quelque chose par défault personnalisé...',
            'property_path' => 'token',
        ));
    
    Habituellement cela n'est pas nécessaire car vous devriez plutôt avoir une
    valeur par défaut dans la propriété ``token`` dans votre objet en question.

Champs pré-intégrés
-------------------

Symfony2 est livré avec les champs suivants:

.. toctree::
    :hidden:

    fields/index

.. include:: fields/map.rst.inc
