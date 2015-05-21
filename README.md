# Reflux.js Manifesto

Finding the idiomattic approach to using Reflux.js.

Please, treat these as heuristics, because: 
  0. I do not necessarily know what I'm talking about.
  0. Idioms are not always the best way to break down a problem.

Got feedback?
  * [Fork Me on github](https://github.com/bunniesandbeatings/RefluxManifesto/fork) and submit pull requests.
  * [Open an issue](https://github.com/bunniesandbeatings/RefluxManifesto/issues/new).

* * *

## Actions:
  * [New Data](#new-data) **always** enters through actions
  * Actions represent one half of the [Public API](#public-api) of your business domain.
  * [Actions do no modelling](#dont-model-in-actions), Stores do.
  * Do not listen to stores (although stores can trigger actions)

## Stores:
  * Where the business domain model belongs
  * Can contain [state](#state)
  * Stores can be dependent on [multiple events occuring](https://github.com/spoike/refluxjs#joining-parallel-listeners-with-composed-listenables)
    * from actions
    * from other stores
  * [Shared modelling](#shared-modelling) should be composed into mixins
  * Emit events so that views and other stores can react to changes.
  * Emit action events so that other dataflows can be triggered. [I have some concerns](#stores-triggering-actions)

## Components:
   * Are dumb or smart
   * Smart == ViewController
     * Map view events to Actions
     * Call Actions during component lifecycle - e.g. componentWillMount
     * Map Store Events to setState, causing invalidation
   * Dumb == Business Domain Free components, they talk about UI things, not business things
     * Know nothing of your Actions or stores
     * Are mutated via parent 'ViewControllers' setting proprties or state.

* * *

# Notes

## New Data

New data can:
  * Result from user actions
  * Result from service results
  * Result from domain changes (eg the hashchange event)

Even without new data, if the view wants state to change (eg SomeThing.toggleSelected), do so through actions.

## Public API

Your business domain get's a clear public API comprised of:
  * Actions: which are the only cause for state mutation.
  * Store Events: which allow ViewControllers to respond to changes in state
  * Store State: Which allows views to refresh based on state regardless of wether store events occured
 
I'm not sure about reading state from the stores. It might be possible to avoid using it in views, and keep the interface very narrow indeed.

## Shared Modelling
From [An Issue](https://github.com/spoike/refluxjs/issues/252) where Spoike discusses typical store->component updates and shared behaviour.

> `trigger` passes the arguments over to the listeners... and I personally prefer to pass all the data that the components need through the event. I have passed the store itself (for quick prototype hacks) and use setters and getters. Both approaches are equal valid implementations and I don't want to enforce one over the other.

> However I don't personally encourage the latter approach (of setters and getters on the store) since that usually has been a slipper slope to create stores that are "god classes". "God class" or "big ball of mud" are anti-patterns that I like to avoid.

> As to how to reuse, I use mixins when applicable. If you think about them as traits, it makes it easy to attach functionality that you may want to reuse. If you need to add common methods to all stores you may add them to the `Reflux.StoreMixin` which is used during creation of stores.

## State

It seems that the practice is to bring all your inital state and/or service state into stores, and then map reduce through stores to end up with calculated data representations your views can consume easily. 

### Regarding the kind of data in stores

Spoike suggests:
> > As far as Flux goes is it okay to store the global var map = Object instance inside a store as a plain JS variable and then have a getter/setter action to get that map instance across multiple components in my app, including Maps component itself?

> Sure, if that works for you then it is okay. Stores are supposed to hold all the data that the components need between each other.

I'm of the opinion that you would try to avoid storing a whole map component (google map object), and have a controller view share it's state with the business model through busines-centric actions. This way you find yourself modelling your domain, not Googles. 

## Stores triggering actions
This one seems like it could become hard to reason about.

My concern is that this would introduce an action that:
  * is triggered without new information being introduced into the business modelling
  * is possibly only a 'sub-flow' of the public API, making it something that views would never directly consume, ergo, not a Public API

I want to use these, they seem valuable.

[There is a discussion here](https://github.com/spoike/refluxjs/issues/158#issuecomment-67444098)


## Don't model in actions
Actions are the cause, stores are the effect.
  * Don't read state from a store in an action, that sounds like we're mixing business logic in actions.
  * Don't respond to the payload from components triggering the action, that's also business logic.
