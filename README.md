# EventPy
A multiprocessing Python event API.

## Requirements
Python 3.5.x +

## Usage
Below are the basic steps for using the event API in your own scripts.

An example script is also included in the repository files.

### Steps for proper usage:
- Write your own event classes which inherit from `Event`.
  - IMPORTANT: _These must be pickable classes._
- Write your event dispatcher class, inheriting from the `EventDispatcher` interface.
  - `EventDispatcher`'s execute a loop on a seperate process.
  - `EventDispatcher` subclasses must implement their own `update` and `finish` methods. The `update` method is invoked once each loop execusion. The `finish` should contain any necessary final logic, such as closing I/O connections.
  - Optionally, you may implement the `init` method to initialize data or objects that are passed to the `update` and `finish` methods.
- Write your event listener class, inheriting from `EventListener`.
  - `EventListener` subclasses need a callback function for each of the events it subscribes to.
- Instantiate objects for your `EventDispatcher`'s and `EventListener`'s in your main thread.
- Instantiate an `EventDispatchManager` object in your main thread, passing the `EventDispatcher` and `EventListener` objects as arguements to the constructor. `EventDispatcher`'s passed as arguments to the constructor are registered with the `EventDispatchManager` object. `EventListener`'s are then registered with any `EventDispatcher`'s which dispatch at least one event for which the listener susbscribes.
- When ready to begin the event dispatcher processes, call the `deploy_dispatchers` method of the `EventDispatchManager` object.
- To initiate a dispatch for any new events, call the `invoke_dispatch` method of the `EventDispatchManager`. Typically, you will have a loop on your main thread where this will be called once every loop execusion.
- Before termination of your program, it is best to call the `end_dispatchers` method of the `EventDispatchManager`. This will insure the `finish` method of each registered dispatcher is called.
