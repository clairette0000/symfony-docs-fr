.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

Date
====

S'assure que la valeur est une date valide épousant le format "YYYY-MM-DD".

.. code-block:: yaml

    properties:
        birthday:
            - Date: ~

Options
-------

* ``message``: le message d'erreur lorque la validation échoue
