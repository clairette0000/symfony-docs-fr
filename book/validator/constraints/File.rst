.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

File
====

S'assure que la valeur correspond au chemin d'un fichier existant.

.. code-block:: yaml

    properties:
        filename:
            - File: ~

Options
-------

* ``maxSize``: la poids maximum toléré du fichier. Peut être défini en bytes, en kilobytes (avec le suffixe "k") ou en megabytes (avec le suffixe "M")
* ``mimeTypes``: un ou plusieurs types MIME autorisés
* ``notFoundMessage``: le message d'erreur lorsque le fichier n'a pas été trouvé
* ``notReadableMessage``: le message d'erreur lorsque le fichier n'a pu être lu
* ``maxSizeMessage``: le message d'erreur lorsque l'option de validation ``maxSize`` échoue
* ``mimeTypesMessage``: le message d'erreur lorsque l'option de validation ``mimeTypes`` échoue

Example: Validation du poids et du type MIME d'un fichier
---------------------------------------------------------

Dans cet exemple, nous allons utiliser la contrainte ``File`` pour vérifier que
ce fichier n'excède pas le poids de 128 kilobytes et qu'il s'agit bien d'un
document PDF.

.. configuration-block::

    .. code-block:: yaml

        properties:
            filename:
                - File: { maxSize: 128k, mimeTypes: [application/pdf, application/x-pdf] }

    .. code-block:: xml

        <!-- Sensio/HelloBundle/Resources/config/validation.xml -->
        <class name="Sensio\HelloBundle\Author">
            <property name="filename">
                <constraint name="File">
                    <option name="maxSize">128k</option>
                    <option name="mimeTypes">
                        <value>application/pdf</value>
                        <value>application/x-pdf</value>
                    </option>
                </constraint>
            </property>
        </class>

    .. code-block:: php-annotations

        // Sensio/HelloBundle/Author.php
        class Author
        {
            /**
             * @validation:File(maxSize = "128k", mimeTypes = {
             *   "application/pdf",
             *   "application/x-pdf"
             * })
             */
            private $filename;
        }

    .. code-block:: php

        // Sensio/HelloBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\File;
        
        class Author
        {
            private $filename;
            
            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('filename', new File(array(
                    'maxSize' => '128k',
                    'mimeTypes' => array(
                        'application/pdf',
                        'application/x-pdf',
                    ),
                )));
            }
        }

Lorsque vous validez l'objet avec un fichier qui n'a pas satisfait à une de ces
exigences, un messages d'erreur approprié est retourné au validateur:

.. code-block:: text

    Sensio\HelloBundle\Author.filename:
        The file is too large (150 kB). Allowed maximum size is 128 kB
