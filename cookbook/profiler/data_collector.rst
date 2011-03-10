.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

.. index::
   single: Profiling; Profilage; Data Collector; Collecteur de données

Comment créer un Collecteur de Données personnalisé
===================================================

Le :doc:`Profiler </book/internals/profiler>` de Symfony2 délègue la collection
de données aux collecteurs de données. Symfony2 est livré avec des Bundles avec peu
d'entre eux, mais vous pouvez facilement créer le vôtre.

Création d'un Collecteur de Données personnalisé
------------------------------------------------

La création d'un collecteur de données personnalisé est aussi simple que d'implémenter le
:class:`Symfony\\Component\\HttpKernel\\DataCollector\\DataCollectorInterface`::

    interface DataCollectorInterface
    {
        /**
         * Collects data for the given Request and Response.
         *
         * @param Request    $request   A Request instance
         * @param Response   $response  A Response instance
         * @param \Exception $exception An Exception instance
         */
        function collect(Request $request, Response $response, \Exception $exception = null);

        /**
         * Returns the name of the collector.
         *
         * @return string The collector name
         */
        function getName();
    }

La méthode ``getName()`` doit retourner un nom unique. Il est utilisé pour
accéder aux informations ultérieurement collectées (voir la section à propos
des tests fonctionnels ci-dessous par exemple).

La méthode ``collect()`` est responsable du stockage des données auxquelles elle
souhaite offrir l'accès aux propriétés locales.

.. caution::
    
    Comme le Profiler sérialise les instances du collecteur de données, vous ne devriez
    pas stocker des objets qui n'auraient pas été sérialisés (comme des PDO (PHP
    Data Objects) ou sinon vous devez fournir votre propre méthode ``serialize()``).

La plupart du temps, il est plus opportun d'étendre
:class:`Symfony\\Component\\HttpKernel\\DataCollector\\DataCollector` et de
peupler la propriété ``$this->data`` (il prend soin de sérialiser la propriété
``$this->data``)::

    class MemoryDataCollector extends DataCollector
    {
        public function collect(Request $request, Response $response, \Exception $exception = null)
        {
            $this->data = array(
                'memory' => memory_get_peak_usage(true),
            );
        }

        public function getMemory()
        {
            return $this->data['memory'];
        }

        public function getName()
        {
            return 'memory';
        }
    }

.. _data_collector_tag:

Activation de Collecteurs de Données personnalisés
--------------------------------------------------

Pour activer un collecteur de données, ajoutez-le comme un service habituel dans
une de vos configurations puis taguez-le avec ``data_collector``:

.. configuration-block::

    .. code-block:: yaml

        services:
            data_collector.your_collector_name:
                class: Fully\Qualified\Collector\Class\Name
                tags:
                    - { name: data_collector }

    .. code-block:: xml

        <service id="data_collector.your_collector_name" class="Fully\Qualified\Collector\Class\Name">
            <tag name="data_collector" />
        </service>

    .. code-block:: php

        $container
            ->register('data_collector.your_collector_name', 'Fully\Qualified\Collector\Class\Name')
            ->addTag('data_collector')
        ;

Ajout de Templates au Web Profiler
----------------------------------

Quand vous voulez afficher les données collectées par votre collecteur de données
sur la barre de debug ou votre Web Profiler, créez un template Twig suivant ce
gabarit:

.. code-block:: jinja

    {% extends 'WebProfilerBundle:Profiler:layout.html.twig' %}

    {% block toolbar %}
        {# the web debug toolbar content #}
    {% endblock %}

    {% block head %}
        {# if the web profiler panel needs some specific JS or CSS files #}
    {% endblock %}

    {% block menu %}
        {# the menu content #}
    {% endblock %}

    {% block panel %}
        {# the panel content #}
    {% endblock %}

Chaque bloc est optionnel. Le bloc ``toolbar`` est utilisé pour la barre de
debug et ``menu`` et ``panel`` sont utilisés pour ajouter un panneau au Web
Profiler.

Tous les blocs ont accès à l'objet ``collector``.

.. tip::

    Les templates intégrés utilisent une image encodée en base64 pour la barre
    d'outils (``<img src="data:image/png;base64,..."``). Vous pouvez facilement
    calculer la valeur en base64 d'une image à l'aide de ce petit script:
    ``echo base64_encode(file_get_contents($_SERVER['argv'][1]));``.

Pour activer le template, ajoutez un attribut ``template`` au tag ``data_collector``
dans votre configuration:

.. configuration-block::

    .. code-block:: yaml

        services:
            data_collector.your_collector_name:
                class: Fully\Qualified\Collector\Class\Name
                tags:
                    - { name: data_collector, template: "YourBundle:Collector:templatename", id: "your_collector_name" }

    .. code-block:: xml

        <service id="data_collector.your_collector_name" class="Fully\Qualified\Collector\Class\Name">
            <tag name="data_collector" template="YourBundle:Collector:templatename" id="your_collector_name" />
        </service>

    .. code-block:: php

        $container
            ->register('data_collector.your_collector_name', 'Fully\Qualified\Collector\Class\Name')
            ->addTag('data_collector', array('template' => 'YourBundle:Collector:templatename', 'id' => 'your_collector_name'))
        ;
