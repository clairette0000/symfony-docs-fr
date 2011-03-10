.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

Max
===

S'assure que la valeur n'est pas supérieure à la limite imposée.

.. code-block:: yaml

    properties:
        age:
            - Max: 99

Options
-------

* ``limit`` (**par défaut**, required): la valeur maximum
* ``message``: le message d'erreur lorsque la validation échoue
