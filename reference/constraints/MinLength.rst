.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

MinLength
=========

S'assure que la longueur de la chaine de caractères n'est pas inférieure à la
limite imposée.

.. code-block:: yaml

    properties:
        firstName:
            - MinLength: 3

Options
-------

* ``limit`` (**par défaut**, required): la valeur minimum
* ``message``: le message d'erreur lorsque la validation échoue
