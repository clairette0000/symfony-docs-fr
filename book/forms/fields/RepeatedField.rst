RepeatedField
=============

Le champs ``RepeatedField`` est un groupe de champs étendus qui vous permet
d'afficher un champs deux fois. Le champs répété ne sera validé que lorsque
l'utilisateur entrera une valeur identitique à ces deux champs simultanément::

    use Symfony\Component\Form\RepeatedField;

    $form->add(new RepeatedField(new TextField('email')));

Cela est vraiment utile pour approuver la conformité des adresses courrielles ou
des mots de passe!
