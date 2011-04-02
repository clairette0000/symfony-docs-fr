.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

NotNull
=======

S'assure que la valeur n'est pas ``null``.

.. code-block:: yaml

    properties:
        firstName:
            - NotNull: ~

Options
-------

* ``message``: le message d'erreur lorsque la validation échoue
