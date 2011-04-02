.. codeauthor:: D. CHARTIER <denis.chartier+symfony-docs-fr@bonjour-tic.com>

.. index::
   single: Contrôleur
   single: MVC; Contrôleur

Le Contrôleur
=============

Toujours avec nous après ces deux premières parties? Vous êtes déjà un
inconditionnel de Symfony2! Sans plus tarder, nous allons voir ce que les
contrôleurs peuvent faire pour vous.

.. index::
   single: Formats
   single: Contrôleur; Formats
   single: Routage; Formats
   single: Vue; Formats

Mosaïque des Formats
--------------------

De nos jours, une application Web doit être en mesure de livrer plus que de
simples fichiers HTML. Du XML pour les flux RSS ou des Web Services, du JSON
pour les requêtes Ajax,... Bref, il y a beaucoup de formats différents à choisir.
Manipuler ces formats dans Symfony2 est simple. Modifiez ``routing.yml`` et
ajoutez un ``_format`` avec une valeur d'attribut ``xml``:

.. configuration-block::

    .. code-block:: yaml

        # src/Sensio/HelloBundle/Resources/config/routing.yml
        hello:
            pattern:  /hello/{name}
            defaults: { _controller: HelloBundle:Hello:index, _format: xml }

    .. code-block:: xml

        <!-- src/Sensio/HelloBundle/Resources/config/routing.xml -->
        <route id="hello" pattern="/hello/{name}">
            <default key="_controller">HelloBundle:Hello:index</default>
            <default key="_format">xml</default>
        </route>

    .. code-block:: php

        // src/Sensio/HelloBundle/Resources/config/routing.php
        $collection->add('hello', new Route('/hello/{name}', array(
            '_controller' => 'HelloBundle:Hello:index',
            '_format'     => 'xml',
        )));

Puis, ajoutez un template ``index.xml.twig`` aux côtés de
``index.html.twig``:

.. code-block:: xml+php

    <!-- src/Sensio/HelloBundle/Resources/views/Hello/index.xml.twig -->
    <hello>
        <name>{{ name }}</name>
    </hello>

Enfin, comme le modèle doit être choisi en fonction du format, effectuez les
changements suivants dans le contrôleur:

.. code-block:: php

    // src/Sensio/HelloBundle/Controller/HelloController.php
    public function indexAction($name, $_format)
    {
        return $this->render(
            'HelloBundle:Hello:index.'.$_format.'.twig',
            array('name' => $name)
        );
    }

C'est tout ce qu'il y a à faire. Pour les formats standards, Symfony2 choisira
automatiquement le meilleur en-tête ``Content-Type`` pour la réponse. Si vous
voulez prendre en charge des formats différents pour une seule action, utilisez
l'emplacement ``{_format}`` dans le pattern à la place:

.. configuration-block::

    .. code-block:: yaml

        # src/Sensio/HelloBundle/Resources/config/routing.yml
        hello:
            pattern:      /hello/{name}.{_format}
            defaults:     { _controller: HelloBundle:Hello:index, _format: html }
            requirements: { _format: (html|xml|json) }

    .. code-block:: xml

        <!-- src/Sensio/HelloBundle/Resources/config/routing.xml -->
        <route id="hello" pattern="/hello/{name}.{_format}">
            <default key="_controller">HelloBundle:Hello:index</default>
            <default key="_format">html</default>
            <requirement key="_format">(html|xml|json)</requirement>
        </route>

    .. code-block:: php

        // src/Sensio/HelloBundle/Resources/config/routing.php
        $collection->add('hello', new Route('/hello/{name}.{_format}', array(
            '_controller' => 'HelloBundle:Hello:index',
            '_format'     => 'html',
        ), array(
            '_format' => '(html|xml|json)',
        )));

Le contrôleur sera désormais appelé par des URLs comme ``/hello/Fabien.xml`` ou
``/hello/Fabien.json``.

L'entrée ``requirements`` définit les expressions régulières qui doivent
correspondre à des emplacements réservés. Dans cet exemple, si vous essayez de
demander la ressource ``/hello/Fabien.js``, vous obtiendrez une erreur HTTP 404,
car elle ne correspond pas à l'exigence ``_format``.

.. index::
   single: Response
   single: Réponse

L'Objet Response
----------------

Retournons maintenant à notre contrôleur ``Hello``::

    // src/Sensio/HelloBundle/Controller/HelloController.php

    public function indexAction($name)
    {
        return $this->render('HelloBundle:Hello:index.html.twig', array('name' => $name));
    }

La méthode ``render()`` produit un template et retourne un objet ``Response``.
La réponse peut être modifié avant d'être envoyée au navigateur, par exemple,
nous allons changer le ``Content-Type``::

    public function indexAction($name)
    {
        $response = $this->render('HelloBundle:Hello:index.html.twig', array('name' => $name));
        $response->headers->set('Content-Type', 'text/plain');

        return $response;
    }

Pour des templates simples, vous pouvez même créer un objet ``Response`` à la
main et gagner quelques millisecondes::

    public function indexAction($name)
    {
        return new Response('Hello '.$name);
    }

Cela est vraiment utile quand le contrôleur nécessite le retour d'une réponse
JSON pour une requête AJAX.

.. index::
   single: Exceptions

Gestion des Erreurs
-------------------

Quand une ressource n'est pas trouvée, vous devriez tirer pleinement parti du
protocole HTTP et retourner une réponse 404. Cela se fait facilement en
envoyant une exception native HTTP::

    use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;

    public function indexAction()
    {
        $product = // retrieve the object from database
        if (!$product) {
            throw new NotFoundHttpException('Ce produit n\'existe pas.');
        }

        return $this->render(...);
    }

Le ``NotFoundHttpException`` retournera une réponse HTTP 404 (page non trouvée) 
au navigateur.

.. index::
   single: Contrôleur; Redirection
   single: Contrôleur; Renvoi

Redirections et Renvois
-----------------------

Si vous voulez rediriger un utilisateur vers une autre page, utilisez la classe
``RedirectResponse``::

    return new RedirectResponse($this->generateUrl('hello', array('name' => 'Lucas')));

La méthode ``generateUrl()`` est la même que la méthode ``generate()`` que nous
avions utilisée avec le helper ``router`` auparavant. Elle prend le nom de la route
et un tableau de paramètres comme arguments et retourne la jolie adresse qui
convient.

Vous pouvez facilement renvoyer une action vers une autre avec la méthode
``forward()``. Tout comme le helper ``actions``, elle réalise une sous-requête
interne mais elle retourne un objet ``Response`` pour permettre de prochaines
modifications::

    $response = $this->forward('HelloBundle:Hello:fancy', array('name' => $name, 'color' => 'green'));

    // do something with the response or return it directly

.. index::
   single: Request
   single: Requête

L'Objet Request
---------------

Outre les valeurs des paramètres du routage, le contrôleur a également accès à
l'objet ``Request``::

    $request = $this->get('request');

    $request->isXmlHttpRequest(); // is it an Ajax request?

    $request->getPreferredLanguage(array('en', 'fr'));

    $request->query->get('page'); // get a $_GET parameter

    $request->request->get('page'); // get a $_POST parameter

Dans un template, vous pouvez aussi accéder à l'objet ``Request`` via une
variable ``app.request``:

.. code-block:: html+php

    {{ app.request.query.get('page') }}

    {{ app.request.parameter('page') }}

La Session
----------

Même si le protocole HTTP est sans états, Symfony2 fournit un objet sympathique
de sessions qui représente le client (qu'il s'agisse d'une personne réelle à
l'aide d'un navigateur, d'un bot ou d'un web service). Entre deux demandes,
Symfony2 stocke les attributs dans un cookie en utilisant les sessions natives
de PHP.

Stocker et récupérer des informations depuis une session peut être facilement
réalisés à partir de n'importe quel contrôleur::

    $session = $this->get('request')->getSession();

    // store an attribute for reuse during a later user request
    $session->set('foo', 'bar');

    // in another controller for another request
    $foo = $session->get('foo');

    // set the user locale
    $session->setLocale('fr');

Vous pouvez même stocker de courts messages qui seront seulement disponibles
durant la toute prochaine requête::

    // store a message for the very next request (in a controller)
    $session->setFlash('notice', 'Bravo, votre action a été accomplie !');

    // display the message back in the next request (in a template)
    {{ app.session.flash('notice') }}

Le mot de la fin
----------------

C'est tout ce qu'il y a à faire et je ne suis même pas sûr que nous avons passé
les 10 minutes que l'on s'était allouées. Nous avons brièvement présenté les 
Bundles dans la première partie et toutes les caractéristiques que nous avons 
apprises jusqu'à maintenant font partie du "core framework Bundle".

Mais grâce aux Bundles, tout peut être prolongé ou remplacé dans Symfony2.
C'est le thème de la prochaine partie de ce tutoriel.
