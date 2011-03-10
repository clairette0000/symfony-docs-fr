.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

Min
===

S'assure que la valeur n'est pas inférieure à la limite imposée.

.. code-block:: yaml

    properties:
        age:
            - Min: 1

Options
-------

* ``limit`` (**par défaut**, required): la valeur minimum
* ``message``: le message d'erreur lorsque la validation échoue
