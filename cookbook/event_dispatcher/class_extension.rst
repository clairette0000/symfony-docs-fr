.. index::
   single: Event Dispatcher

Comment faire pour étendre une Classe sans utiliser d'Héritage
==============================================================

Pour autoriser plusieurs classes à ajouter des méthodes à une autre classe, vous
devez définir la méthode magique ``__call()`` dans la classe que vous voulez
étendre de la façon suivante::

    class Foo
    {
        // ...

        public function __call($method, $arguments)
        {
            // create an event named 'foo.method_is_not_found'
            // and pass the method name and the arguments passed to this method
            $event = new Event($this, 'foo.method_is_not_found', array('method' => $method, 'arguments' => $arguments));

            // calls all listeners until one is able to implement the $method
            $this->dispatcher->notifyUntil($event);

            // no listener was able to process the event? The method does not exist
            if (!$event->isProcessed()) {
                throw new \Exception(sprintf('Call to undefined method %s::%s.', get_class($this), $method));
            }

            // return the listener returned value
            return $event->getReturnValue();
        }
    }

Puis, créez une classe qui hébergera le point d'écoute::

    class Bar
    {
        public function addBarMethodToFoo(Event $event)
        {
            // we only want to respond to the calls to the 'bar' method
            if ('bar' != $event->get('method']) {
                // allow another listener to take care of this unknown method
                return false;
            }

            // the subject object (the foo instance)
            $foo = $event->getSubject();

            // the bar method arguments
            $arguments = $event->get('parameters');

            // do something
            // ...

            // set the return value
            $event->setReturnValue($someValue);

            // tell the world that you have processed the event
            return true;
        }
    }

Eventuellement, ajoutez une nouvelle méthode ``bar`` à la classe ``Foo``::

    $dispatcher->connect('foo.method_is_not_found', array($bar, 'addBarMethodToFoo'));
