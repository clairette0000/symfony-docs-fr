:orphan:

Glossaire
========

.. glossary::
   :sorted:

   Projet
        Un *Projet* est une arborescence composée d'une Application, d'un
        certain nombre de "bundles", de librairies tierces, d'un autoloader et
        de scripts offrant une interface visible par l'utilisateur final.
        
   Application
        Une *Application* est une arborescence contenant la *configuration* pour
        un certain nombre de "Bundles" donnés.

   Bundle
        Un *Bundle* est un ensemble structuré de fichiers (scripts PHP, feuilles
        de style CSS, javascripts, images,...) qui *implémente* une fonction
        unique (un blog, un forum, etc...). Dans Symfony2, tout (*ou presque*)
        réside dans un bundle. (voir :ref:`page-creation-bundles`)
        
   Contrôleur Frontal
        Un Contrôleur Frontal est un fichier PHP laconique qui se situe dans le
        répertoire web de votre projet. En règle générale, *toutes* les requêtes
        sont traitées par l'exécution du même Contrôleur Frontal, dont la mission
        est d'amorcer votre application Symfony.
        
   Environnement
        {bientôt une définition }

   Service
        Un *Service* est un terme générique désignant tout objet PHP qui effectue
        une tâche spécifique. Un service est généralement utilisé «globalement»,
        comme un objet de connexion à la base de données ou un objet qui délivre
        des e-mails par exemple. Dans Symfony2, les services sont souvent
        configurés et récupérés à partir du conteneur de services. Une application
        qui a de nombreux services découplés est qualifiée d'`architecture
        orientée services`_.

   Conteneur de Services
        Un *Conteneur de Services*, aussi nommé *Conteneur d'Injection de
        Dépendances*, est un objet spécial qui gère les instances de services au
        cœur de l'application. Au lieu de créer des services directement, le
        développeur *éduque* le conteneur de services (en le paramétrant) sur
        la façon de créer des services. Le contrôleur de services prend en charge
        l'instantiation passive et l'injection des services qui en dépendent.

   Specification HTTP
        La *Specification HTTP* est un document de référence qui décrit le
        Protocole de Transfert HTTP (un ensemble de règles portant sur la
        communication requête-réponse d'un traditionnel client-serveur). Cette
        spécification définit le format utilisé pour une requête tout comme une
        réponse aussi exhaustivement que les HTTP headers peuvent l'accomplir.
        Pour plus d'informations, reportez-vous au `HTTP 1.1 RFC`_.

.. _`architecture orientée services`: http://fr.wikipedia.org/wiki/Architecture_orient%C3%A9e_services
.. _`HTTP 1.1 RFC`: http://www.w3.org/Protocols/rfc2616/rfc2616.html
