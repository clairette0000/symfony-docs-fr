.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

NotBlank
========

S'assure que la valeur n'est pas vide (tel que déterminé par le constructeur 
`empty <http://php.net/empty>`).

.. code-block:: yaml

    properties:
        firstName:
            - NotBlank: ~

Options
-------

* ``message``: le message d'erreur lorsque la validation échoue
