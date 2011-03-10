.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

MaxLength
=========

S'assure que la longueur d'une chaine de caractères n'est pas supérieure à la
limite imposée.

.. code-block:: yaml

    properties:
        firstName:
            - MaxLength: 20

Options
-------

* ``limit`` (**par défaut**, required): la valeur maximum
* ``message``: le message d'erreur lorsque la validation échoue
