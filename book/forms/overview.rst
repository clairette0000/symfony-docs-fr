.. todo: Se mettre d'accord = classe de domaine / classe en question

.. index::
   single: Formulaires

Travailler avec des Formulaires
===============================

Symfony2 est livré avec des composants de formulaire pré-intégrés. Ils prennent
en charge l'affichage, le traitement et la soumission des formulaires HTML.

Puisqu'il est possible de traiter les soumissions de formulaire avec la simple
classe :class:`Symfony\\Component\\HttpFoundation\\Request` de Symfony2, le
composant de formulaires prend en compte un certain nombre de tâche habituelles
liés aux formulaires, comme:

1. l'Affichage d'un formulaire HTML avec des champs de formulaires automatiquement générés
2. la Conversion des données soumises en format PHP
3. la Lecture et l'Ecriture de données dans des POPOs (plain old PHP objects)
4. la Validation de données de formulaires soumises avec le ``Validator`` de Symfony2
5. la Protection des soumissions de formulaires contre les attaques CSRF

Présentation
------------

Le composant couvre ces concepts:

*Field*
  Une classe qui convertit les données soumises en données normalisées.

*Form*
  Une collection de champs qui savent comment se valider elles-mêmes.

*Template*
  Un fichier qui transforme un formulaire ou un champs en HTML.

*Objet en question*
  Un objet qu'un formulaire utilise pour peupler les valeurs par défaut et où
  les données soumises sont écrites.

Le composant de formulaire se fie seulement aux composants HttpFoundation et
Validator pour fonctionner. Si vous voulez utiliser les caractéristiques
d'internationalisation, l'extension intl de PHP est aussi requise.

les Objets de Formulaire
------------------------

Un objet de Formulaire encapsule une collection de champs qui convertit les
données soumises en format utilisé dans votre application. Les classes de
formulaires sont créées comme des sous-classes de :class:`Symfony\\Component\\Form\\Form`.
Vous devriez implémenter la méthode ``configure()`` pour initialiser le formulaire
avec un ensemble de champs.

.. code-block:: php

    // src/Sensio/HelloBundle/Contact/ContactForm.php
    namespace Sensio\HelloBundle\Contact;

    use Symfony\Component\Form\Form;
    use Symfony\Component\Form\TextField;
    use Symfony\Component\Form\TextareaField;
    use Symfony\Component\Form\CheckboxField;
    
    class ContactForm extends Form
    {
        protected function configure()
        {
            $this->add(new TextField('subject', array(
                'max_length' => 100,
            )));
            $this->add(new TextareaField('message'));
            $this->add(new TextField('sender'));
            $this->add(new CheckboxField('ccmyself', array(
                'required' => false,
            )));
        }
    }

Un formulaire est composé d'objets ``Field``. Dans ce cas, notre formulaire
recense les champs ``subject``, ``message``, ``sender`` et ``ccmyself``.
``TextField``, ``TextareaField`` et ``CheckboxField`` sont seulement trois des
champs de formulaire disponibles; une liste complète peut être consultée dans
:doc:`Champs de Formulaire <fields>`.

Emploi d'un Formulaire dans un Contrôleur
-----------------------------------------

Le motif standard pour utiliser un formulaire dans un contôleur ressemble à ceci:

.. code-block:: php

    // src/Sensio/HelloBundle/Controller/HelloController.php
    public function contactAction()
    {
        $contactRequest = new ContactRequest($this->get('mailer'));
        $form = ContactForm::create($this->get('form.context'), 'contact');
        
        // If a POST request, write the submitted data into $contactRequest
        // and validate the object
        $form->bind($this->get('request'), $contactRequest);
        
        // If the form has been submitted and is valid...
        if ($form->isValid()) {
            $contactRequest->send();
        }

        // Display the form with the values in $contactRequest
        return $this->render('HelloBundle:Hello:contact.html.twig', array(
            'form' => $form
        ));
    }

Il y a deux chemins de code ici:

1. Si le formulaire n'a pas été soumis ou est invalide, il est tout simplement
   transmis au template.
   
2. Si le formulaire a été soumis et est valide, la requête contact est envoyée.

Nous avons créé un formulaire avec la méthode statique ``create()``. Cette
méthode attend un contexte de formulaire qui contient tous les services par
défaut (par exemple un ``Validator``) et les configurations que le formulaire
nécessite pour son bon fonctionnement.

.. note:

    Si vous n'utilisez pas Symfony2 ou ses conteneurs de service, ne vous
    inquiétez pas. Vous pouvez aisément créer un ``FormContext`` et un
    ``Request`` manuellement:
    
    .. code-block:: php
    
        use Symfony\Component\Form\FormContext;
        use Symfony\Component\HttpFoundation\Request;
        
        $context = FormContext::buildDefault();
        $request = Request::createFromGlobals();

Formaires et Objets en question
-------------------------------

Dans le dernier exemple, un ``ContactRequest`` a été lié au formulaire. Les
valeurs de propriétés de cet objet sont utilisées pour peupler les champs de
formulaire. Après liaison, les valeurs soumises sont écrites à l'intérieur de
l'objet une nouvelle fois. La classe ``ContactRequest`` pourrait ressembler à
ceci:

.. code-block:: php

    // src/Sensio/HelloBundle/Contact/ContactRequest.php
    namespace Sensio\HelloBundle\Contact;

    class ContactRequest
    {
        protected $subject = 'Sujet...';
        
        protected $message;
        
        protected $sender;
        
        protected $ccmyself = false;
        
        protected $mailer;
        
        public function __construct(\Swift_Mailer $mailer)
        {
            $this->mailer = $mailer;
        }
        
        public function setSubject($subject)
        {
            $this->subject = $subject;
        }
        
        public function getSubject()
        {
            return $this->subject;
        }
        
        // Setters and getters for the other properties
        // ...
        
        public function send()
        {
            // Send the contact mail
            $message = \Swift_Message::newInstance()
                ->setSubject($this->subject)
                ->setFrom($this->sender)
                ->setTo('moi@exemple.tld')
                ->setBody($this->message);
                
            $this->mailer->send($message);
        }
    }
    
.. note::

    Voir :doc:`Emails </cookbook/email>` pour davantage d'informations sur
    l'envoi de courriels.

Pour chaque champs de votre formulaire, la classe de votre objet en question
nécessite d'avoir

1. Une propriété publique avec le nom du champs, ou
2. Un setter et un getter publics préfixés par "set"/"get", suivi du nom du
   champs avec sa première lettre en majuscule.
   
Validation des Données Soumises
-------------------------------

Le formulaire utlise le composant ``Validator`` pour valider les valeurs soumises
du formulaire. Toutes les contraintes dans l'objet en question, dans le
formulaire et dans ces champs seront validées quand ``bind()`` sera appelé.
Nous allons ajouter quelques contraintes à ``ContactRequest`` pour s'assurer que
personne ne puisse soumettre le formulaire avec des données non conformes.

.. code-block:: php

    // src/Sensio/HelloBundle/Contact/ContactRequest.php
    namespace Sensio\HelloBundle\Contact;

    class ContactRequest
    {
        /**
         * @validation:MaxLength(100)
         * @validation:NotBlank
         */
        protected $subject = 'Sujet...';
        
        /**
         * @validation:NotBlank
         */
        protected $message;
        
        /**
         * @validation:Email
         * @validation:NotBlank
         */
        protected $sender;
        
        /**
         * @validation:AssertType("boolean")
         */
        protected $ccmyself = false;
        
        // Other code...
    }

Si la moindre contrainte échoue, une erreur est affichée près du champs de
formulaire correspondant. Vous pouvez en apprendre plus à propos des contraintes
dans :doc:`Contraintes de Validation </book/validator/constraints>`.

Création de Champs de Formulaire Automatiquement
------------------------------------------------

Si vous utilisez Doctrine2 ou le ``Validator`` de Symfony2, Symfony est déjà
suffisament omniscient concernant vos classes de domaine. Il sait quel type de
données est utilisé pour persister une propriété dans la base de données, quelles
contraintes de validation la propriété obéit etc. Le composant de Formulaire
peut utiliser ces informations pour "deviner" quel type de champs devrait être
créé avec quelles configurations.

Pour utiliser cette fonctionnalité, un formulaire nécessite de savoir la classe
de l'objet en question. Vous pouvez définir cette classe au sein de la méthode
``configure()`` du formulaire en utilisant ``setDataClass()`` et en fournissant
le nom de la classe pleinement qualifiée en tant que chaine de caractères.
L'appel ``add()`` avec uniquement le nom de la propriété créera automatiquement
par la suite le champs opportun.

.. code-block:: php

    // src/Sensio/HelloBundle/Contact/ContactForm.php
    class ContactForm extends Form
    {
        protected function configure()
        {
            $this->setDataClass('Sensio\\HelloBundle\\Contact\\ContactRequest');
            $this->add('subject');  // TextField with max_length=100 because
                                    // of the @MaxLength constraint
            $this->add('message');  // TextField
            $this->add('sender');   // EmailField because of the @Email constraint
            $this->add('ccmyself'); // CheckboxField because of @AssertType("boolean")
        }
    }

Ces supputations de champs sont évidemment pas toujours parfaites. Pour la
propriété ``message``, Symfony créé un ``TextField`` alors qu'un ``TextareaField``
serait préférable. Donc vous devez modifier ce champs manuellement. Vous pouvez
aussi affiner les options des champs générés en leur fournissant un second
paramètre. Nous allons ajouter une option ``max_length`` au champs ``sender``
pour limiter sa longueur.

.. code-block:: php

    // src/Sensio/HelloBundle/Contact/ContactForm.php
    class ContactForm extends Form
    {
        protected function configure()
        {
            $this->setDataClass('Sensio\\HelloBundle\\Contact\\ContactRequest');
            $this->add('subject'); 
            $this->add(new TextareaField('message'));
            $this->add('sender', array('max_length' => 50));
            $this->add('ccmyself');
        }
    }

La génération automatique des champs de formulaire vous aide à améliorer votre
vitesse de développement et réduit les duplications de code. Vous pouvez stocker
des informations à propos des propriétés une seule fois et laisser Symfony2 faire
le travail à votre place.

Transformation de Formulaires en HTML
-------------------------------------

Dans un contrôleur, nous avons confié le formulaire à notre template dans la
variable ``form``. Dans le template, nous pouvons utiliser le helper
``form_field`` pour générer un prototype du formulaire brut de décoffrage.

.. code-block:: html+jinja

    # src/Sensio/HelloBundle/Resources/views/Hello/contact.html.twig
    {% extends 'HelloBundle::layout.html.twig' %}

    {% block content %}
    <form action="#" method="post">
        {{ form_field(form) }}
        
        <input type="submit" value="Envoyer!" />
    </form>
    {% endblock %}
    
Personnalisation de la génération HTML
--------------------------------------

Dans la plupart des cas, vous souhaiterez personnaliser le HTML de votre
formulaire. Vous pouvez faire ça en utilisant d'autres helpers pré-intégrés
de transformation de formulaires.

.. code-block:: html+jinja

    # src/Sensio/HelloBundle/Resources/views/Hello/contact.html.twig
    {% extends 'HelloBundle::layout.html.twig' %}

    {% block content %}
    <form action="#" method="post" {{ form_enctype(form) }}>
        {{ form_errors(form) }}
        
        {% for field in form %}
            {% if not field.ishidden %}
            <div>
                {{ form_errors(field) }}
                {{ form_label(field) }}
                {{ form_field(field) }}
            </div>
            {% endif %}
        {% endfor %}

        {{ form_hidden(form) }}
        <input type="submit" />
    </form>
    {% endblock %}
    
Symfony2 est livré avec les helpers suivants:

*``form_enctype``*
  affiche l'attribut ``enctype`` d'une balise de formulaire. Requis pour uploader un fichier.

*``form_errors``*
  affiche la balise ``<ul>`` avec les erreurs d'un champs ou d'un formulaire.

*``form_label``*
  affiche la balise ``<label>`` d'un champs.

*``form_field``*
  affiche le HTML d'un champs ou d'un formulaire.

*``form_hidden``*
  affiche tous les champs hidden d'un formulaire.

La transformation d'un formulaire est couverte en détails dans :doc:`Formulaires dans les Templates <view>`.

Félicitations! Vous venez juste de créer votre premier formulaire pleinement
fonctionnel avec Symfony2.

En apprendre davantage dans le Vadémécum
----------------------------------------

* :doc:`/cookbook/email`
* :doc:`/cookbook/gmail`
* :doc:`/cookbook/templating/PHP`
