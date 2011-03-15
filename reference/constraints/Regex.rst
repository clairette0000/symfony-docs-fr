.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

Regex
=====

S'assure que la valeur corresponde à une expression rationnelle.

.. code-block:: yaml

    properties:
        title:
            - Regex: /\w+/

Options
-------

* ``pattern`` (**par défaut**, required): le motif de l'expression rationnelle
* ``match``: détermine si le motif doit correspondre ou non. Par défaut: ``true``
* ``message``: le message d'erreur lorsque la validation échoue
