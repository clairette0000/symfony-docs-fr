.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

.. index::
   single: Tests; Authentification HTTP

Comment simuler une Authentification HTTP lors d'un Test Fonctionnel
====================================================================

Si votre application nécessite une authentification HTTP, transmettez le nom
d'utilisateur et le mot de passe comme variables de serveur via ``createClient()``::

    $client = $this->createClient(array(), array(
        'PHP_AUTH_USER' => 'username',
        'PHP_AUTH_PW'   => 'pa$$word',
    ));

Vous pouvez aussi le surcharger par une basique requête unitaire::

    $client->request('DELETE', '/post/12', array(), array(
        'PHP_AUTH_USER' => 'username',
        'PHP_AUTH_PW'   => 'pa$$word',
    ));
