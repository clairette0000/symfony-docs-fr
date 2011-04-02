.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

.. index::
   single: Contrôleur; Services

Comment définir des Contrôleurs en tant que Services?
=====================================================

Dans le Manuel, vous avez appris comment un contrôleur peut être aisément utilisé
quand il étend la classe de base
:class:`Symfony\\Bundle\\FrameworkBundle\\Controller\\Controller`. Puisque celà
fonctionne bien, les contrôleurs peuvent aussi être spécifiés en tant que services.

Pour invoquer un contrôleur qui jouerait le rôle de service, utilisez la
ponctuation double-point (:). Par exemple, supposons que nous avons défini un
service nommé ``my_controller`` et que nous avons renvoyé une méthode nommée
``indexAction()`` à l'intérieur du service::

    $this->forward('my_controller:indexAction', array('foo' => $bar));

Nous devons utiliser la même notation que la valeur définie dans la route
``_controller``:

.. code-block:: yaml

    my_controller:
        pattern:   /
        defaults:  { _controller: my_controller:indexAction }

Pour utiliser un contrôleur de cette façon, il doit être défini dans la
configuration du conteneur de service. Pour plus d'informations, consultez le
chapitre :doc:`Service Container </book/service_container>`.

Quand vous utilisez un contrôleur en tant que service, il n'étendra très
probablement pas la classe de base ``Controller``. Au lieu de se fier à ses
méthodes raccourcies, vous devrez interagir directement avec les services
auxquels vous avez besoin. Heureusement, c'est habituellement assez facile et la
classe de base ``Controller`` elle-même est une base stable sur laquelle vous
pouvez effectuer de nombreuses tâches courantes.

.. note::

    La spécification d'un contrôleur en tant que service requiert un petit peu
    plus de travail. L'avantage primordial est que le contrôleur entier ou
    n'importe quel service transmis au contrôleur peut être modifié via la
    configuration du conteneur de service. Cela est spécialement utile quand vous
    développez un Bundle open-source ou n'importe quel Bundle qui sera intégré
    dans une grande variété de projets. Donc, même si vous ne spécifiez pas votre
    contrôleur en tant que service, il fonctionnera tout de même bien
    conjointement aux Bundles open-source de Symfony2.
