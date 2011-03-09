.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

.. index::
   single: Tests
   pair: Isolation; Clients

Comment tester l'Interaction de plusieurs Clients
=================================================

Si vous devez simuler une interaction entre différents Clients (dans le cadre
d'un chat par exemple), créez plusieurs Clients::

    $harry = $this->createClient();
    $sally = $this->createClient();

    $harry->request('POST', '/say/sally/Hello');
    $sally->request('GET', '/messages');

    $this->assertEquals(201, $harry->getResponse()->getStatusCode());
    $this->assertRegExp('/Hello/', $sally->getResponse()->getContent());

Cela fonctionne sauf quand votre code maintient un état global ou s'il dépend
d'une librairie tierce qui aurait une sorte d'état global. Dans un tel cas, vous
pouvez isoler vos clients::

    $harry = $this->createClient();
    $sally = $this->createClient();

    $harry->insulate();
    $sally->insulate();

    $harry->request('POST', '/say/sally/Hello');
    $sally->request('GET', '/messages');

    $this->assertEquals(201, $harry->getResponse()->getStatusCode());
    $this->assertRegExp('/Hello/', $sally->getResponse()->getContent());

Les clients isolés exécutent de façon transparente leurs requêtes dans un
processus PHP dédié et propre, donc en évitant tout effet secondaire.

.. tip::
    
    Puisqu'un client isolé est plus lent, vous pouvez conserver un client dans le
    processus principal et isoler les autres.
