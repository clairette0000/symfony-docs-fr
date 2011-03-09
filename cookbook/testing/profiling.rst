.. index::
   single: Tests; Profiling

Comment utiliser le Profiler lors d'un Test Fonctionnel
=======================================================

Il est hautement recommandé qu'un test fonctionnel teste uniquement une Response.
Mais si vous écrivez des tests fonctionnels qui surveillent vos serveurs de
production, vous voudrez peut-être écrire des tests dans les données de profilage
qui vous donne une façon rassurante de vérifier de nombreuses choses et
d'appliquer certaines mesures.

Le :doc:`Profiler </book/internals/profiler>` de Symfony2 rassemble de nombreuses
informations pour chaque requête. Utilisez ces information pour vérifier le
nombre d'appels à la base de données, le temps de sollicitation du framework,...
Mais avant l'écriture d'assertions, vérifiez toujours que votre Profiler est en
réalité disponible (il est activé par défaut dans l'environnement de ``test``)::

    class HelloControllerTest extends WebTestCase
    {
        public function testIndex()
        {
            $client = $this->createClient();
            $crawler = $client->request('GET', '/hello/Fabien');

            // Write some assertions about the Response
            // ...

            // Check that the profiler is enabled
            if ($profiler = $client->getProfiler()) {
                // check the number of requests
                $this->assertTrue($profiler->get('db')->getQueryCount() < 10);

                // check the time spent in the framework
                $this->assertTrue( $profiler->get('timer')->getTime() < 0.5);
            }
        }
    }

Si un test échoue à cause des données de profilage (excès de requête à la base de
données par exemple), vous voudrez peut-être utiliser le Web Profiler pour
analyser la requête une fois les tests terminés. C'est facile à réaliser si vous
incorporez un token dans votre message d'erreur::

    $this->assertTrue(
        $profiler->get('db')->getQueryCount() < 30,
        sprintf('Checks that query count is less than 30 (token %s)', $profiler->getToken())
    );

.. caution::

    Le stockage du Profiler peut être différent selon l'environnement (à plus
    forte raison si vous utilisez une base SQLite, qui est par définition
    configurée par défaut solitairement).

.. note::

    Les informations du Profiler sont disponibles même si vous isolez un client
    ou si vous utilisez une couche HTTP pour vos tests.

.. tip::

    Consultez l'API du :doc:`data collectors</cookbook/profiler/data_collector>`
    pour en apprendre plus à propos de ces interfaces.
