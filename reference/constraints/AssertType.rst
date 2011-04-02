.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

AssertType
==========

S'assure que la valeur correspond à un certain type spécifique de données.

.. code-block:: yaml

    properties:
        age:
            - AssertType: integer

Options
-------

* ``type`` (**par défaut**, required): un nom de classe ou un de types de données PHP tels que déterminé par les fonctions PHP ``is_``.

  * `array <http://php.net/is_array>`_
  * `bool <http://php.net/is_bool>`_
  * `callable <http://php.net/is_callable>`_
  * `float <http://php.net/is_float>`_ 
  * `double <http://php.net/is_double>`_
  * `int <http://php.net/is_int>`_ 
  * `integer <http://php.net/is_integer>`_
  * `long <http://php.net/is_long>`_
  * `null <http://php.net/is_null>`_
  * `numeric <http://php.net/is_numeric>`_
  * `object <http://php.net/is_object>`_
  * `real <http://php.net/is_real>`_
  * `resource <http://php.net/is_resource>`_
  * `scalar <http://php.net/is_scalar>`_
  * `string <http://php.net/is_string>`_
* ``message``: le message d'erreur lorsque la validation échoue
