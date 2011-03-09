.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

.. index::
   single: Courriels
   single: E-mails

Comment envoyer un e-mail ?
===========================

L'envoi de courriels est une tâche classique pour toute application web et peut
être source de complications spécifiques et d'éventuels écueils.

Au lieu de réinventer la roue, une des solutions consiste à utiliser le
``SwiftmailerBundle`` qui met à profit la puissance de la librairie `Swiftmailer`_.

.. note::

    N'omettez pas d'activer ce Bundle dans votre kernel avant de l'utiliser::

        public function registerBundles()
        {
            $bundles = array(
                // ...
                new Symfony\Bundle\SwiftmailerBundle\SwiftmailerBundle(),
            );

            // ...
        }

.. _swift-mailer-configuration:

Configuration
-------------

Avant d'employer Swiftmailer, assurez vous d'y inclure sa configuration. Le seul
paramètre de configuration indispensable est ``transport``:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        swiftmailer:
            transport:  smtp
            encryption: ssl
            auth_mode:  login
            host:       smtp.gmail.com
            username:   your_username
            password:   your_password

    .. code-block:: xml

        <!-- app/config/config.xml -->

        <!--
        xmlns:swiftmailer="http://symfony.com/schema/dic/swiftmailer"
        http://symfony.com/schema/dic/swiftmailer http://symfony.com/schema/dic/swiftmailer/swiftmailer-1.0.xsd
        -->

        <swiftmailer:config
            transport="smtp"
            encryption="ssl"
            auth-mode="login"
            host="smtp.gmail.com"
            username="your_username"
            password="your_password" />

    .. code-block:: php

        // app/config/config.php
        $container->loadFromExtension('swiftmailer', array(
            'transport'  => "smtp",
            'encryption' => "ssl",
            'auth_mode'  => "login",
            'host'       => "smtp.gmail.com",
            'username'   => "your_username",
            'password'   => "your_password",
        ));

Les principaux paramètres de configuration de Swiftmailer indiquent comment les
messages seront expédiés.

Les attributs de configuration suivants sont disponibles:

* ``transport``         (``smtp``, ``mail``, ``sendmail``, ou ``gmail``)
* ``username``
* ``password``
* ``host``
* ``port``
* ``encryption``        (``tls``, ou ``ssl``)
* ``auth_mode``         (``plain``, ``login``, ou ``cram-md5``)
* ``spool``

  * ``type`` (quelle règle d'enchaînement doit être supportée ; seul ``file`` est actuellement proposé)
  * ``path`` (où stocker les messages)
* ``delivery_address``  (une adresse e-mail qui collectera TOUS les e-mails sortants)
* ``disable_delivery``  (indiquez true pour empêcher complètement toute expédition)

L'envoi d'e-mails
-----------------

La librairie Swiftmailer assure la création, la configuration puis l'envoi
d'objets ``Swift_Message``. Le "mailer" est responsable de livraison effective
du message et est accessible via le service ``mailer``. Dans l'ensemble,
l'envoi d'e-mails est assez simple::

    public function indexAction($name)
    {
        // get the mailer first (mandatory to initialize Swift Mailer)
        $mailer = $this->get('mailer');

        $message = \Swift_Message::newInstance()
            ->setSubject('Hello Email')
            ->setFrom('send@example.com')
            ->setTo('recipient@example.com')
            ->setBody($this->renderView('HelloBundle:Hello:email', array('name' => $name)))
        ;
        $mailer->send($message);

        return $this->render(...);
    }

Pour conserver le découplage, le corps de l'e-mail a été stocké dans un template
et est converti par la méthode ``renderView()``.

L'objet ``$message`` supporte une kyrielle d'options, comme l'inclusion de
pièces-jointes, le format HTML dans le corps du message, etc... Heureusement,
Swiftmailer couvre le sujet de la `Création de messages`_ en détail dans sa
documentation.

.. tip::

    Lisez la fiche ":doc:`gmail`" si vous souhaitez utiliser Gmail en tant que
    ``transport`` dans un environnement de développement.

.. _`Swiftmailer`: http://www.swiftmailer.org/
.. _`Création de messages`: http://swiftmailer.org/docs/messages
