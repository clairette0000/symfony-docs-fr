.. index::
   single: Event Dispatcher

Comment personnaliser une Méthode Comportementale sans utiliser d'Héritage
==========================================================================

Faire quelque chose avant ou après un Appel à une Méthode
---------------------------------------------------------

Si vous voulez faire quelque chose juste avant ou juste après qu'une méthode
soit invoquée, vous pouvez incorporer respectivement un évènement au tout début
ou à la toute fin de cette méthode::

    class Foo
    {
        // ...

        public function send($foo, $bar)
        {
            // do something before the method
            $event = new Event($this, 'foo.do_before_send', array('foo' => $foo, 'bar' => $bar));
            $this->dispatcher->notify($event);

            // the real method implementation is here
            // $ret = ...;

            // do something after the method
            $event = new Event($this, 'foo.do_after_send', array('ret' => $ret));
            $this->dispatcher->notify($event);

            return $ret;
        }
    }

Modification des Arguments d'une Méthode
----------------------------------------

Si vous voulez autoriser une classe tierce à modifier des arguments passés à une
méthode juste avant que celle-ci soit exécutée, ajoutez un évènement ``filter``
au tout début de cette méthode::

    class Foo
    {
        // ...

        public function render($template, $arguments = array())
        {
            // filter the arguments
            $event = new Event($this, 'foo.filter_arguments');
            $this->dispatcher->filter($event, $arguments);

            // get the filtered arguments
            $arguments = $event->getReturnValue();
            // the method starts here
        }
    }

Et ici un exemple de filtre::

    class Bar
    {
        public function filterFooArguments(Event $event, $arguments)
        {
            $arguments['processed'] = true;

            return $arguments;
        }
    }
