# Reflux Manifesto
Thoughts on using Reflux as I try to come to terms with it.

## Actions:
  * [New Data](#new_data) **always** enters through actions
    * information from services
    * information from user actions
  * Actions represent the mutating api 
    * Even without new data, if the view wants state to change (eg toggleSelected), do so through actions
  * Actions do no modelling, Stores do.
## Stores:
  * Are where the business domain model belongs
  * Can have direct method calls from other business modelling (read stores)
  * Actions can read state from a store, BUT that sounds like business logic in actions
  * Stores can be dependent on multiple events occuring (the join api https://github.com/spoike/refluxjs#joining-parallel-listeners-with-composed-listenables)
    * from actions
    * from other stores
  * Shared modelling can be composed into helpers
## Components:
   * Are dumb or smart
   * Smart == ViewController - maps events to actions
   * Dumb == Business Domain Free components, they talk about UI things, not business things


### New Data
