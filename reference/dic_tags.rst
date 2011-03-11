Les tags d'Injection de Dépendances
===================================

Tags:

* ``data_collector``
* ``kernel.listener``
* ``templating.helper``
* ``templating.renderer``
* ``routing.loader``
* ``twig.extension``

Activation d'assistants de templates PHP personnels
---------------------------------------------------

Pour activer un assistant de templates personnel, ajoutez-le comme service
habituel dans l'une de vos configurations, taguez-le avec ``templating.helper``
et définissez un ``alias`` (l'assistant sera accessible par cet alias dans les
templates):

.. configuration-block::

    .. code-block:: yaml

        services:
            templating.helper.nom_de_votre_assistant:
                class: Nom\De\Classe\Complet\De\Votre\Assistant
                tags:
                    - { name: templating.helper, alias: nom_alias }

    .. code-block:: xml

        <service id="templating.helper.nom_de_votre_assistant" class="Nom\De\Classe\Complet\De\Votre\Assistant">
            <tag name="templating.helper" alias="nom_alias" />
        </service>

    .. code-block:: php

        $conteneur
            ->register('templating.helper.nom_de_votre_assistant', 'Nom\De\Classe\Complet\De\Votre\Assistant')
            ->addTag('templating.helper', array('alias' => 'nom_alias'))
        ;

Activation d'extensions Twig personnelles
-----------------------------------------

Pour activer une extension Twig, ajoutez-la comme service habituel dans l'une
de vos configuration et taguez-la avec ``twig.extension``:

.. configuration-block::

    .. code-block:: yaml

        services:
            twig.extension.nom_de_votre_extension:
                class: Nom\De\Classe\Complet\De\Votre\Extension
                tags:
                    - { name: twig.extension }

    .. code-block:: xml

        <service id="twig.extension.nom_de_votre_extension" class="Nom\De\Classe\Complet\De\Votre\Extension">
            <tag name="twig.extension" />
        </service>

    .. code-block:: php

        $conteneur
            ->register('twig.extension.nom_de_votre_extension', 'Nom\De\Classe\Complet\De\Votre\Extension')
            ->addTag('twig.extension')
        ;

Activation de listeners personnels
----------------------------------

Pour activer un listener personnel, ajoutez-le comme service habituel dans l'une
de vos configurations et taguez-le avec ``kernel.listener``:

.. configuration-block::

    .. code-block:: yaml

        services:
            kernel.listener.nom_de_votre_listener:
                class: Nom\De\Classe\Complet\De\Votre\Listener
                tags:
                    - { name: kernel.listener }

    .. code-block:: xml

        <service id="kernel.listener.nom_de_votre_listener" class="Nom\De\Classe\Complet\De\Votre\Listener">
            <tag name="kernel.listener" />
        </service>

    .. code-block:: php

        $conteneur
            ->register('kernel.listener.nom_de_votre_listener', 'Nom\De\Classe\Complet\De\Votre\Listener')
            ->addTag('kernel.listener')
        ;

Activation de moteur de templates personnels
--------------------------------------------

Pour activer un moteur de templates personnel, ajoutez-le comme service habituel
dans l'une de vos configuration et taguez-le avec ``templating.engine``:

.. configuration-block::

    .. code-block:: yaml

        services:
            templating.engine.nom_de_votre_moteur:
                class: Nom\De\Classe\Complet\De\Votre\Moteur
                tags:
                    - { name: templating.engine }

    .. code-block:: xml

        <service id="templating.engine.nom_de_votre_moteur" class="Nom\De\Classe\Complet\De\Votre\Moteur">
            <tag name="templating.engine" />
        </service>

    .. code-block:: php

        $conteneur
            ->register('templating.engine.nom_de_votre_moteur', 'Nom\De\Classe\Complet\De\Votre\Moteur')
            ->addTag('templating.engine')
        ;

Activation de chargeurs de routage personnels
---------------------------------------------

Pour activer un chargeur de routage personnel, ajoutez-le comme service habituel
dans l'une de vos configuration et taguez-le avec ``routing.loader``:

.. configuration-block::

    .. code-block:: yaml

        services:
            routing.loader.nom_de_votre_chargeur:
                class: Nom\De\Classe\Complet\De\Votre\Chargeur
                tags:
                    - { name: routing.loader }

    .. code-block:: xml

        <service id="routing.loader.nom_de_votre_chargeur" class="Nom\De\Classe\Complet\De\Votre\Chargeur">
            <tag name="routing.loader" />
        </service>

    .. code-block:: php

        $conteneur
            ->register('routing.loader.nom_de_votre_chargeur', 'Nom\De\Classe\Complet\De\Votre\Chargeur')
            ->addTag('routing.loader')
        ;
