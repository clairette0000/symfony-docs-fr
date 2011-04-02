.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

DateTime
========

S'assure que la valeur est une chaine de caratères datetime épousant le format
"YYYY-MM-DD HH:MM:SS".

.. code-block:: yaml

    properties:
        createdAt:
            - DateTime: ~

Options
-------

* ``message``: le message d'erreur lorsque la validation échoue
