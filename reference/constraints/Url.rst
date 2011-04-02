.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

Url
===

S'assure que la valeur est une URL conforme.

.. code-block:: yaml

    properties:
        website:
            - Url: ~

Options
-------

* ``protocols``: une liste de protocoles tolérés. Par défaut: "http", "https", "ftp" et "ftps".
* ``message``: le message d'erreur lorsque la validation échoue
