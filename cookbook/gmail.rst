.. index::
   single: Emails; Gmail

Comment utiliser Gmail pour l'envoi d'e-mails
=============================================

Durant la phase de développement, au lieu d'utiliser le traditionnel serveur
SMTP pour envoyer des courriels, vous considérez peut-être que Gmail est plus
simple et plus pratique. Le Bundle Swiftmailer est parfaitement rôdé pour ça.

.. tip::

    Au lieu d'utiliser votre compte Gmail final, il est bien sûr recommandé d'en
    créer un spécialement dédié à cet effet.

Dans le fichier de configuration de votre environnement de développement, changez
l'option de ``transport`` par ``gmail`` puis mentionnez le binôme ``username``
et ``password`` qui vous permettra de vous identifier avec succès au compte en
question:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config_dev.yml
        swiftmailer:
            transport: gmail
            username:  your_gmail_username
            password:  your_gmail_password

    .. code-block:: xml

        <!-- app/config/config_dev.xml -->

        <!--
        xmlns:swiftmailer="http://symfony.com/schema/dic/swiftmailer"
        http://symfony.com/schema/dic/swiftmailer http://symfony.com/schema/dic/swiftmailer/swiftmailer-1.0.xsd
        -->

        <swiftmailer:config
            transport="gmail"
            username="your_gmail_username"
            password="your_gmail_password" />

    .. code-block:: php

        // app/config/config_dev.php
        $container->loadFromExtension('swiftmailer', array(
            'transport' => "gmail",
            'username'  => "your_gmail_username",
            'password'  => "your_gmail_password",
        ));

Le tour est joué!

.. note::

    Le transport ``gmail`` est uniquement un raccourci qui utilise le transport
    ``smtp`` et qui intègre directement les ``encryption``, ``auth_mode`` et
    ``host`` requis par Gmail.
