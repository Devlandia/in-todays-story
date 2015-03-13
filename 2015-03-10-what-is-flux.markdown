---
layout: post
title: "What is Flux"
date: 2015-03-03 16:00:00 -0300
comments: true
author: Pedro Martos
categories: [flux]
---

_by [@pedromartos](https://github.com/pedromartos)_

This is just a summary of the article [Flux For Stupid People](http://blog.andrewray.me/flux-for-stupid-people/) that I wrote to help me understand better how Flux works.

> **Should I use Flux?**  
> If your application deals with **dynamic data** then **yes**, you should probably use Flux.  
> If your application is just **static views** that don't share state, and you never save nor update data, then **no**, Flux won't give you any benefit.

> **Why Flux?**  
> No one knows exactly how to structure a FrontEnd application. We are always studying new "libraries" like jQuery, Angular, Backbone and so the data flow is ignored which usually comes back to haunt us when the application becomes more complex. That's why we should focus on "best practices" instead. Therefore Flux.

## What is Flux?
There is no Flux library. Flux is just a way to describe a "one way" data flow.  
**Don't try to compare Flux to Model View Controller (MVC) architecture. Drawing parallels will only be confusing.**

## How to Flux?
### 1. Your views "Dispatch" actions
Even though Flux isn't a library we still need the [Flux Dispatcher](https://github.com/facebook/flux/blob/master/src/Dispatcher.js). This guy is the cornerstone behind the Flux concept.  
A "dispatcher" is essentially an event system. It broadcasts events and registers callbacks.  

    var AppDispatcher = new Dispatcher();  
    
Let's say we wish to add a new item to a list. When we click on a "new item" button our view will dispatch an event with the event name and the new item data.

    <button onClick={ this.createNewItem }>New Item</button>

We have a React button component that has a click event above. Below we have the event handler:

    createNewItem: function( evt ) {

        AppDispatcher.dispatch({
            eventName: 'new-item',
            newItem: { name: 'Marco' } // example data
        });
    
    }
    
    
### 2. Your "Store" responds to dispatched events
This is where we will keep all the logic behind our application. In the example above we are adding a new item to a List. So we can have a Store called ListStore that will be an global object:

    // Global object representing list data and logic
    var ListStore = {
    
        // Actual collection of model data
        items: [],
    
        // Accessor method we'll use later
        getAll: function() {
            return this.items;
        }
    };
    
Then we have to register the event **inside our ListStore**. It is here that our data is changed.

    var ListStore = {
        ...
        
        AppDispatcher.register( function( payload ) {
        
            switch( payload.eventName ) {
                case 'new-item':
                    // We get to mutate data!
                    ListStore.items.push( payload.newItem );
                    break;
            }
            
            return true; // Needed for Flux promise resolution
        }); 
    }
    
Each payload contains an event name and data.  
Only Stores can register dispatcher callbacks. Remember all logic behind our app is inside our Stores.

### Key notes about Stores:
* A store is not a model. A store contains models
* A store is the only thing in your application that knows how to update data. **This is the most important part of Flux.**    The event we dispatched doesn't know how to add or remove items.
 
### 3. Your Store emits a "Change" event
Now we have to tell the view that our data has changed. For this the Store will emit an event, but not using the **Dispatcher**. We can do this using libraries like [MicroEvent.js](http://notes.jetienne.com/2011/03/22/microeventjs.html) or [EventEmitter3](https://github.com/primus/eventemitter3)

Using MicroEvent.js
    
    MicroEvent.mixin( ListStore );  
    
Then, inside our Store, we will add the emitter right after we update our data

    case 'new-item':

        ListStore.items.push( payload.newItem );

        // Tell the world we changed!
        ListStore.trigger( 'change' );

        break;

### 4. Your View Responds to the "Change" Event

With React this one is easy too. We will re-render the view to show the changes on our list.

This is a callback from a React component. The `componentDidMount` is called after the component is successfully rendered on the view

    componentDidMount: function() {  
        ListStore.bind( 'change', this.listChanged );
    },
    
Now creating the `listChanged` method we will call ReactComponent `forceUpdate` method to re-render the component, our list.

    listChanged: function() {  
        // Since the list changed, trigger a new render.
        this.forceUpdate();
    },
    
And just in case we don't need this component anymore, let's remove this event listener

    componentWillUnmount: function() {  
        ListStore.unbind( 'change', this.listChanged );
    },

And now we will render our component:

    render: function() {

        // Remember, ListStore is global!
        // There's no need to pass it around
        var items = ListStore.getAll();
    
        // Build list items markup by looping
        // over the entire list
        var itemHtml = items.map( function( listItem ) {
    
            // "key" is important, should be a unique
            // identifier for each list item
            return <li key={ listItem.id }>
                { listItem.name }
              </li>;
    
        });
    
        return (
            <div>
                <ul>
                    { itemHtml }
                </ul>
                <button onClick={ this.createNewItem }>New Item</button>
            </div>
        );
    }
    
## In today story...

We learned how the Flux works. In our example we clicked on a button to add a new item, then the view dispatched an action, the ListStore responded to that action updating the list of items and then the store triggered a change event, and then the view responded to this change event re-rendering the items list.
