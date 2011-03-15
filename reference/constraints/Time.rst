.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

Time
====

S'assure que la valeur est une chaine de caractères épousant le format "HH:MM:SS".

.. code-block:: yaml

    properties:
        createdAt:
            - DateTime: ~

Options
-------

* ``message``: le message d'erreur lorsque la validation échoue
