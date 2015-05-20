# Reflux Manifesto
Thoughts on using Reflux as I try to come to terms with it.

## Actions:
  * [New Data](#new-data) **always** enters through actions
  * Actions represent one half of the [Public API](#public-api) of your business domain.
  * Actions do no modelling, Stores do.

## Stores:
  * Where the business domain model belongs
  * Can have direct method calls from other business modelling (other stores)
  * Actions could read state from a store, BUT that sounds like we're mixing business logic in actions
  * Stores can be dependent on [multiple events occuring](https://github.com/spoike/refluxjs#joining-parallel-listeners-with-composed-listenables)
    * from actions
    * from other stores
  * Shared modelling should be composed into helpers (other modeling functions in a seperate namespace)

## Components:
   * Are dumb or smart
   * Smart == ViewController
     * Map view events to Actions
     * Call Actions during component lifecycle - e.g. componentWillMount
     * Map Store Events to setState, causing invalidation
   * Dumb == Business Domain Free components, they talk about UI things, not business things
     * Know nothing of your Actions or stores
     * Are mutated via parent 'ViewControllers' setting proprties or state.


## Notes

### New Data

New data can:
  * Result from user actions
  * Result from service results
  * Result from domain changes (eg the hashchange event)

Even without new data, if the view wants state to change (eg SomeThing.toggleSelected), do so through actions.

### Public API

Your business domain get's a clear public API comprised of:
  * Actions: which are the only cause for state mutation.
  * Store Events: which allow ViewControllers to respond to changes in state
  * Store State: Which allows views to refresh based on state regardless of wether store events occured
 
I'm not sure about reading state from the stores. It might be possible to avoid using it in views, and keep the interface very narrow indeed.
