.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

.. index::
    single: Cache; Varnish

Comment utiliser Varnish pour doper la vitesse de mon site?
===========================================================

Puisque le cache de Symfony2 utilise les en-têtes standards du cache HTTP, le
:ref:`symfony-gateway-cache` peut facilement être remplacé par un autre proxy
inverse. Varnish est le plus puissant accélérateur open-source HTTP capable de
servir le contenu du cache rapidement et incluant le support de :ref:`Edge Side
Includes<edge-side-includes>`.

.. index::
    single: Varnish; configuration

Configuration
-------------

Comme nous l'avons vu précédemment, Symfony2 est assez futé pour détecter s'il
dialogue avec un proxy inverse qui comprend l'ESI ou non. Il fonctionne clé-en-main
quand vous utilisez le proxy inverse de Symfony2 mais vous avez besoin d'une
configuration spéciale pour le faire fonctionner avec Varnish. Heureusement,
Symfony2 s'appuie déjà sur une autre norme écrite par Akamaï (`Edge Architecture`_)
donc cette astuce de configuration dans ce chapitre vous sera utile même si vous
n'utilisez pas Symfony2.

.. note::

    Varnish supporte uniquement l'attribut ``src`` pour les tags ESI (les
    attributs ``onerror`` et ``alt`` sont ignorés).

Tout d'abord, configurez Varnish pour qu'il annonce son support d'ESI en ajoutant
un en-tête de ``Surrogate-Capability`` aux requêtes renvoyées à l'application
backend:

.. code-block:: text

    sub vcl_recv {
        set req.http.Surrogate-Capability = "abc=ESI/1.0";
    }

Puis, optimisez Varnish afin qu'il analyse uniquement dès qu'il y a au moins un
tag ESI en vérifiant l'en-tête ``Surrogate-Control`` que Symfony2 ajoute
automatiquement:

.. code-block:: text

    sub vcl_fetch {
        if (beresp.http.Surrogate-Control ~ "ESI/1.0") {
            unset beresp.http.Surrogate-Control;
            esi;
        }
    }

.. caution::
    
    N'utilisez pas de compression avec ESI : Varnish ne sera plus capable
    d'analyser le contenu de la réponse. Si vous voulez tout de même conserver
    la compression des données, mettez votre serveur web devant Varnish pour
    faire le job.

.. index::
    single: Varnish; Invalidation

Invalidation du Cache
---------------------

Vous ne devriez jamais avoir besoin d'invalider les données cachées car
l'invalidation est déjà prise en compte nativement dans le modèle de cache HTTP
(voir :ref:`http-cache-invalidation`).

Pourtant, Varnish peut être configuré pour accepter une méthode spéciale HTTP
``PURGE`` qui invalidera le cache pour une ressource donnée:

.. code-block:: text

    sub vcl_hit {
        if (req.request == "PURGE") {
            set obj.ttl = 0s;
            error 200 "Purged";
        }
    }

    sub vcl_miss {
        if (req.request == "PURGE") {
            error 404 "Not purged";
        }
    }

.. caution::
    
    Vous devez protéger la méthode HTTP ``PURGE`` afin de vous prémunir que des
    gens au hasard purgent vos données cachées.
