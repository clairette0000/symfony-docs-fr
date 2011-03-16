.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

.. index::
   single: Validation

Le Validator
============

La validation est une tâche commune aux applications web. Les données saisies
dans des formulaires nécessitent d'être validées. Les données ont aussi besoin
d'être validées avant des les enregistrer dans une base de données ou d'être
transmises à un service web.

Symfony2 est livré avec un composant Validator qui rend cette tâche facile. Ce
composant est basé sur la `spécification JSR303 Bean Validation`_. Kesako? Une
spécification de Java pour PHP? Vous avez tapé dans le mille mais ce n'est pas
aussi effroyable que ça ne le paraît. Jetons un coup d'œil sur la manière de
l'aborder avec PHP.

.. index:
   single: Validation; Les bases

Les bases de la Validation
--------------------------

La meilleure façon de comprendre le mécanisme de validation est de le voir en
action. Pour commencer, supposons que vous avez créé un objet PHP comme dans
l'ancien temps et que vous avez besoin de l'utiliser quelque part dans votre
application:

.. code-block:: php

    // Acme/BlogBundle/Author.php
    class Author
    {
        public $name;
    }

Jusqu'à présent, il s'agit juste d'une classe ordinaire qui sert à l'intérieur
de votre application. Le but de la validation est de vous dire si les données
d'un objet sont valides ou non. Pour effectuer ce travail, vous avez besoin de
configurer une liste de règles (nommées :ref:`contraintes<validation-constraints>`)
que l'objet doivent suivre afin d'être valide. Ces règles peuvent être
spécifiques via un nombre de différents formats (YAML, XML, annotations ou PHP).
Pour garantir que la propriété ``$name`` n'est pas vide, ajoutez ce qui suit:

.. configuration-block::

    .. code-block:: yaml

        # Acme/BlogBundle/Resources/config/validation.yml
        Acme\BlogBundle\Author:
            properties:
                name:
                    - NotBlank: ~

    .. code-block:: xml

        <!-- Acme/BlogBundle/Resources/config/validation.xml -->
        <class name="Acme\BlogBundle\Author">
            <property name="name">
                <constraint name="NotBlank" />
            </property>
        </class>

    .. code-block:: php-annotations

        // Acme/BlogBundle/Author.php
        class Author
        {
            /**
             * @validation:NotBlank()
             */
            public $name;
        }

    .. code-block:: php

        // Acme/BlogBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;  
        use Symfony\Component\Validator\Constraints\NotBlank;
        
        class Author
        {
            private $name;
            
            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('name', new NotBlank());
            }
        }

.. tip::

    Les propriétés protégées et privées peuvent aussi être validée, tout comme
    les méthodes "getter" (voir `validator-constraint-targets`).

.. index::
   single: Validation; Utilisation d'un validator

Utilisation du service ``validator``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Pour valider normalement un objet ``Author``, utilisez la méthode ``validate``
du service ``validator`` (classe :class:`Symfony\\Component\\Validator\\Validator`).
Le rôle du ``validator`` est simple: lire les contraintes (par exemple, les
règles) d'une classe et de vérifier si les données d'un objet satisfont ou pas
ces contraintes. Si la validation échoue, un tableau d'erreurs est retourné.
Prenez cet exemple simple depuis l'intérieur d'un contrôleur:

.. code-block:: php

    use Symfony\Component\HttpFoundation\Response;
    // ...

    public function indexAction()
    {
        $author = new Acme\BlogBundle\Author();
        // ... do something to the $author object

        $validator = $container->get('validator');
        $errorList = $validator->validate($author);
        
        if (count($errorList) > 0) {
            return new Response(print_r($errorList, true));
        } else {
            return new Response('L'auteur est valide! Yes!');
        }
    }

Si la propriété ``$name`` est vide, vous verrez le message d'erreur suivant:

.. code-block:: text

    Acme\BlogBundle\Author.name:
        This value should not be blank

Si vous insérez une valeur à l'intérieur de la propriété ``name``, le joyeux
message confirmant le succès apparaitra.

Chaque erreur de validation (appelée "violation de contrainte") est représentée
par l'objet :class:`Symfony\\Component\\Validator\\ConstraintViolation`, qui fait
surgir un message descriptif de l'erreur. Par ailleurs, la méthode ``validate``
retourne un objet :class:`Symfony\\Component\\Validator\\ConstraintViolationList`,
qui agit comme un tableau. Ce qui est une façon biscornue de dire que vous
pouvez utiliser les erreurs retournées par ``validate`` de plusieurs manières
approffondies. Commençons par transformer un template en fournissant la variable
``$errorList``:

.. code-block:: php

    if (count($errorList) > 0) {
      return $this->render('AcmeBlogBundle:Author:validate.html.twig', array(
        'errorList' => $errorList,
      ));
    } else {
        // ...
    }

Au sein du template, vous pouvez afficher une liste des erreurs exactement comme
vous le désirez:

.. configuration-block::

  .. code-block:: html+jinja

    {# src/Acme/BlogBundle/Resources/views/Author/validate.html.twig #}

    <h3>L'auteur recueille ces différentes erreurs</h3>
    <ul>
    {% for error in errorList %}
      <li>{{ error.message }}</li>
    {% endfor %}
    </ul>

  .. code-block:: html+php
  
    <!-- src/Acme/BlogBundle/Resources/views/Author/validate.html.php -->

    <h3>L'auteur recueille ces différentes erreurs</h3>
    <ul>
    <?php foreach ($errorList as $error): ?>
      <li><?php echo $error->getMessage() ?></li>
    <?php endforeach; ?>
    </ul>

.. index::
   single: Validation; Validation au sein de formulaires

Validation et Formulaires
~~~~~~~~~~~~~~~~~~~~~~~~~

Le service ``validator`` peut être utilisé à tout moment pour valider n'importe
quel objet. En réalité, toutefois, vous devrez habituellement travailler avec le
``validator`` indirectement via la class ``Form``. La classe ``Form`` utilise le
service ``validator`` en interne pour valider un objet sous-jacent après que les
valeurs aient été soumises et liées. Les violations de contraintes d'un objet
sont converties à l'intérieur d'objets ``FieldError`` qui peuvent être ensuite
affichées dans votre formulaire:

.. code-block:: php

    $author = new Acme\BlogBundle\Author();
    $form = new Acme\BlogBundle\AuthorForm('author', $author, $this->get('validator');
    $form->bind($this->get('request')->request->get('customer'));
    
    if ($form->isValid()) {
        // process the Author object
    } else {
        // render the template with the errors
        $this->render('BlogBundle:Author:form.html.twig', array('form' => $form));
    }

Pour davantage d'informations, consultez le chapitre :doc:`Formulaires</book/forms/overview>`.

.. index::
   pair: Validation; Configuration

Configuration
-------------

Pour utiliser le validator de Symfony2, assurez vous qu'il est préalablement
activé dans la configuration de votre application:

.. configuration-block::

    .. code-block:: yaml

        # hello/config/config.yml
        framework:
            validation: { enabled: true, annotations: true }
                

    .. code-block:: xml

        <!-- app/config/config.xml -->
        <framework:config>
            <framework:validation enabled="true" annotations="true" />
        </framework:config>

    .. code-block:: php

        // app/config/config.php
        $container->loadFromExtension('framework', array('validation' => array(
            'enabled'     => true,
            'annotations' => true,
        ));

.. note::

    La configuration en ``annotations`` nécessite d'être paramétrée à ``true``
    si votre dressez des contraintes via des annotations.

.. index::
   single: Validation; Contraintes

.. _validation-constraints:

Contraintes
-----------

Le ``validator`` est conçu pour valider des objets selon *des contraintes* (par
exemple, des règles). Afin de valider un objet, listez une ou plusieurs
contraintes à ces classes et transmettez les ensuite à un service ``validator``.

Une contrainte est simplement un objet PHP qui fait une déclaration d'affirmation.
Dans la vie quotidienne, une contrainte pourrait être: "Le gâteau ne doit pas
sortir carbonisé". Dans Symfony2, les contraintes sont similaires: 

A constraint is simply a PHP object that makes an assertive statement. In
real life, a constraint could be: "The cake must not be burned". In Symfony2,
constraints are similar: ce sont des affirmations selon lesquelles une condition
est vraie. A une valeur donnée, une contrainte vous annoncera si oui ou non, une
valeur épouse les règles d'une contrainte.

Contraintes Supportées
~~~~~~~~~~~~~~~~~~~~~~

Symfony2 regroupe un grand nombre de contraintes courantes. La liste entière
des contraintes détaillées sont disponibles dans le
:doc:`chapitre de contraintes de validation de référence</reference/constraints>`.

.. index::
   single: Validation; Configuration des contraintes

Configuration des contraintes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Quelques contraintes, comme :doc:`NotBlank</reference/constraints/NotBlank>`,
sont simples tandis que d'autres, comme :doc:`Choice</reference/constraints/Choice>`,
possèdent plusieurs options de configuration disponibles. Celle-ci sont des
propriétés publiques d'une contrainte et chacune peut être configurée en
désignant un tableau d'options à la contrainte. Supposons que la classe ``Author``
détienne une autre propriété, ``gender`` qui peut être soit "male" soit
"female":

.. configuration-block::

    .. code-block:: yaml

        # Acme/BlogBundle/Resources/config/validation.yml
        Acme\BlogBundle\Author:
            properties:
                gender:
                    - Choice: { choices: [male, female], message: Désignez votre sexe. }

    .. code-block:: xml

        <!-- Acme/BlogBundle/Resources/config/validation.xml -->
        <class name="Acme\BlogBundle\Author">
            <property name="gender">
                <constraint name="Choice">
                    <option name="choices">
                        <value>male</value>
                        <value>female</value>
                    </option>
                    <option name="message">Désignez votre sexe.</option>
                </constraint>
            </property>
        </class>

    .. code-block:: php-annotations

        // Acme/BlogBundle/Author.php
        class Author
        {
            /**
             * @validation:Choice(
             *     choices = { "male", "female" },
             *     message = "Désignez votre sexe."
             * )
             */
            public $gender;
        }

    .. code-block:: php

        // Acme/BlogBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\NotBlank;
        
        class Author
        {
            public $gender;
            
            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('gender', new Choice(array(
                    'choices' => array('male', 'female'),
                    'message' => 'Désignez votre sexe.',
                ));
            }
        }

Les options d'une contrainte peuvent toujours être communiquées en tant que
tableau. Quelques contraintes vous autorisent aussi à transmettre une valeur de
l'option "par défaut" à la contrainte au lieu du tableau. Dans le cas d'une
contrainte ``Choice``, les options ``choices`` peuvent être spécifiées de cette
façon.

.. configuration-block::

    .. code-block:: yaml

        # Acme/BlogBundle/Resources/config/validation.yml
        Acme\BlogBundle\Author:
            properties:
                gender:
                    - Choice: [male, female]

    .. code-block:: xml

        <!-- Acme/BlogBundle/Resources/config/validation.xml -->
        <class name="Acme\BlogBundle\Author">
            <property name="gender">
                <constraint name="Choice">
                    <value>male</value>
                    <value>female</value>
                </constraint>
            </property>
        </class>

    .. code-block:: php-annotations

        // Acme/BlogBundle/Author.php
        class Author
        {
            /**
             * @validation:Choice({"male", "female"})
             */
            protected $gender;
        }

    .. code-block:: php

        // Acme/BlogBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\Choice;
        
        class Author
        {
            protected $gender;
            
            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('gender', new Choice(array('male', 'female')));
            }
        }

Assurez vous de ne pas laisser les deux différentes méthodes de spécifications
d'options vous embrouiller l'esprit. Si vous êtes incertain, vérifiez plutôt la
documentation de l'API pour la contrainte ou empruntez la voie rassurante de
toujours passer un tableau en options (la première méthode montrée ci-dessus).

.. index::
   single: Validation; Raison d'être des Contraintes

.. _validator-constraint-targets:

La raison d'être des Constraintes
---------------------------------

Les contraintes peuvent être appliqués à des propriétés de classes ou une
méthode publique getter (ex: ``getFullName``).

.. index::
   single: Validation; Contraintes des Properiétés

Propriétés
~~~~~~~~~~

La validation des propriétés d'une classe est la technique de validation
élémentaire. Symfony2 vous autorise à valider les propriétés privées, protégées
ou publiques. La liste suivante vous dévoile comment configurer les propriétés
``$firstName`` et ``$lastName`` d'une classe ``Author`` qui a au moins 3
caractères.

.. configuration-block::

    .. code-block:: yaml

        # Acme/BlogBundle/Resources/config/validation.yml
        Acme\BlogBundle\Author:
            properties:
                firstName:
                    - NotBlank: ~
                    - MinLength: 3
                lastName:
                    - NotBlank: ~
                    - MinLength: 3

    .. code-block:: xml

        <!-- Acme/BlogBundle/Resources/config/validation.xml -->
        <class name="Acme\BlogBundle\Author">
            <property name="firstName">
                <constraint name="NotBlank" />
                <constraint name="MinLength">3</constraint>
            </property>
            <property name="lastName">
                <constraint name="NotBlank" />
                <constraint name="MinLength">3</constraint>
            </property>
        </class>

    .. code-block:: php-annotations

        // Acme/BlogBundle/Author.php
        class Author
        {
            /**
             * @validation:NotBlank()
             * @validation:MinLength(3)
             */
            private $firstName;

            /**
             * @validation:NotBlank()
             * @validation:MinLength(3)
             */
            private $lastName;
        }

    .. code-block:: php

        // Acme/BlogBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\NotBlank;
        use Symfony\Component\Validator\Constraints\MinLength;
        
        class Author
        {
            private $firstName;

            private $lastName;

            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addPropertyConstraint('firstName', new NotBlank());
                $metadata->addPropertyConstraint('firstName', new MinLength(3));
                $metadata->addPropertyConstraint('lastName', new NotBlank());
                $metadata->addPropertyConstraint('lastName', new MinLength(3));
            }
        }

.. index::
   single: Validation; Contraintes de Getters

Getters
~~~~~~~

La technique de validation suivante peut aussi de restreindre la valeur
retournée par une méthode. Symfony2 vous autorise à contraindre toute méthode
publique dont le nom commence par "get" ou "is". Dans ce chapitre, ils sont
communément surnommés "getters".

L'avantage de cette technique est que cela autorise la validation de votre objet
dynamiquement. Selon l'état de votre objet, la méthode peut retourner
différentes valeurs qui sont ensuite validées.

La liste suivante vous dévoile comment utiliser la contrainte :doc:`AssertTrue
</reference/constraints/AssertTrue>` pour valider si le jeton généré
dynamiquement est correct:

.. configuration-block::

    .. code-block:: yaml

        # Acme/BlogBundle/Resources/config/validation.yml
        Acme\BlogBundle\Author:
            getters:
                tokenValid:
                    - AssertTrue: { message: "Le jeton est invalide" }

    .. code-block:: xml

        <!-- Acme/BlogBundle/Resources/config/validation.xml -->
        <class name="Acme\BlogBundle\Author">
            <getter property="tokenValid">
                <constraint name="AssertTrue">
                    <option name="message">le jeton est invalide</option>
                </constraint>
            </getter>
        </class>

    .. code-block:: php-annotations

        // Acme/BlogBundle/Author.php
        class Author
        {
            /**
             * @validation:AssertTrue(message = "Le jeton est invalide")
             */
            public function isTokenValid()
            {
                // return true or false
            }
        }

    .. code-block:: php

        // Acme/BlogBundle/Author.php
        use Symfony\Component\Validator\Mapping\ClassMetadata;
        use Symfony\Component\Validator\Constraints\AssertTrue;
        
        class Author
        {

            public static function loadValidatorMetadata(ClassMetadata $metadata)
            {
                $metadata->addGetterConstraint('tokenValid', new AssertTrue(array(
                    'message' => 'Le jeton est invalide',
                )));
            }
            
            public function isTokenValid()
            {
                // return true or false
            }
        }

La méthode publique ``isTokenValid`` effectuera n'importe quelle logique qui
déterminera si le jeton en question est valide puis retourne ``true`` ou
``false``.

.. note::

    Les plus chevronnés d'entre vous auront remarqué que le préfixe du getter
    ("get " ou "is") est omis dans le mapping. Cela vous permet de déplacer
    la contrainte à une propriété du même nom plus tard (ou vice versa) sans
    changer votre logique de validation.

Le mot de la fin
----------------

Le ``validator`` de Symfony2 est un outil puissant qui peut être exploité pour
garantir la conformité des données d'un quelconque objet. La force de la
validation se repose sur les "contraintes", qui sont des règles que vous pouvez
appliquer aux propriétés ou aux méthodes "get*" de votre objet. Et tandis que
vous utiliserez le plus fréquemment le framework de validation indirectement
quand vous utilisez des formulaires, gardez à l'esprit qu'il peut être utilisé à
tout endroit pour valider n'importe quel objet.

Référez-vous au Vadémécum pour plus d'approfondissements
--------------------------------------------------------

* :doc:`/cookbook/validation/custom_constraint`

.. _Validator: https://github.com/symfony/Validator
.. _spécification JSR303 Bean Validation: http://jcp.org/en/jsr/detail?id=303
