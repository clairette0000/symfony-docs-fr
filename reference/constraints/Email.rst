.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

Email
=====

S'assure que la valeur est une adresse courrielle conforme.

.. code-block:: yaml

    properties:
        email:
            - Email: ~

Options
-------

* ``checkMX``: si les enregistrements MX du nom de domaine doivent être vérifiés. Par défaut: ``false``
* ``message``: le message d'erreur lorsque la validation échoue
