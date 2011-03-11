Traductions
===========

La documentation de Symfony2 est écrite en anglais et de nombreuses personnes
sont impliquées dans le processus de traduction.

Contribution
------------

Tout d'abord, familiarisez vous avec le  :doc:`langage de structuration <format>`
utilisé dans la documentation.

.. todo:: À traduire
Then, subscribe to the `Symfony docs mailing-list`_, as collaboration happens
there.

Finalement, trouvez le dépôt *maître*  pour la langue à laquelle vous voulez
contribuer. Voici la liste des dépôts *maître* officiels:

.. todo:: peut-être ajouter le dépôt français
* *Anglais*:  http://github.com/symfony/symfony-docs
* *Russe*:  http://github.com/avalanche123/symfony-docs-ru
* *Roumain*: http://github.com/sebastian-ionescu/symfony-docs-ro

.. note::

    Si vous voulez contribuer aux traductions dans un nouveau langage,
    lisez la :ref:`section dédiée <translations-adding-a-new-language>`.

Rejoindre l'équipe de traduction
--------------------------------

Si vous voulez aidez à traduire quelques documents pour votre langue ou corriger
quelques bugs, songez à nous rejoindre ; la procédure est très simple:

* Présentez-vous sur la `mailing-list deSymfony docs`_;
* *(Facultatif)* Demandez sur quels documents vous pouvez travailler ;
* Forkez le dépôt *maître* pour votre langue (cliquez sur le bouton "Fork" sur
  la page de GitHub) ; 
* Traduisez quelques documents ;
* Effectuez une demande de pull (cliquez sur le bouton "Demande de pull" depuis
  votre dépôt *forké* GitHub) ;
* Le chef d'équipe accepte vos modifications et les fusionne dans le dépôt
  maître ;
* Le site web de la documentation est mis à jour chaque nuit à partir du dépôt
  maître.

.. _translations-adding-a-new-language:

Adding a new Language
Ajout d'une nouvelle langue
---------------------------

Cette section donne quelques pistes pour commencer la traduction de la
documentation de Symfony2 dans une nouvelle langue.

Comme démarrer une traduction est un travail de grande ampleur, parlez de votre
projet sur la `mailing-list Symfony docs`_ et essayez de trouver des gens motivés
et prêts à aider.

Quand l'équipe est prête, nommé un chef d'équipe ; il sera responsable du
dépôt *maître*.

Créez le dépôt et copiez les documents *anglais*.

L'équipe peut maintenant débuter le processus de traduction.

Lorsque l'équipe pense que le dépôt est stable et consistant (tout  est traduit,
ou les documents non traduits ont été retirés des sommaires -- les fichiers
nommés ``index.rst`` et ``map.rst.inc``), le chef d'équipe peut demander à ce
que le dépôt soit ajouté à la liste des dépôts *maîtres* officiels en envoyant
un email à Fabien (fabien.potencier at symfony-project.org).

Support
-------

.. todo: cible mouvante à remanier

La traduction ne se termine pas lorsque tout est traduit. La documentation est
une cible mouvante (de nouveaux documents sont ajoutés, des bugs sont corrigés,
des paragraphes sont réorganisés, etc.). L'équipe de traduction doit suivre de
très près le dépôt anglais et appliquer les changements le plus tôt possible
aux documents traduits.

.. caution::

    Les langues non-maintenues sont retirées de la liste officiel des dépôts,
    car une documentation obsolète est dangereuse.

.. _mailing-list Symfony docs: http://groups.google.com/group/symfony-docs
