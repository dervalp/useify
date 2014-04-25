# Useify
---

`Useify` is a middleware implementation that extends objects.

## Example

```javascript
// var Useify = require( "Useify" ),
//   should = require( "should" );

var MyClass = function () {
  this.hasInitialised = false;
  this.value = 0;
}

MyClass.prototype.init = function () {
  this.hasInitialised = true;
  this.middleware( "postInit", function () {
    this.execute();
  } );
};

MyClass.prototype.execute = function () {
  this.middleware( function ( _sum ) {

    this.hasInitialised.should.be.true;
    should( _sum ).equal( 22 );
    _done();

  }, 1, 2 );
}

// `Useify` the class
Useify( MyClass );

// Adds the first middleware function.
MyClass.use( function ( _one, _two, _next ) {

  // The context `this` is a reference to the context class. In this example it is `myClass`
  var aValue = myClass.value + _one + _two;

  // Trigger the callback when this functions tasks are complete. Pass a dynmaic amount of
  // arguments to the next function.
  _next( aValue );

} );

// Adds the second middleware function. Note that the first argument is the paramater from the
// previous middleware function. The last argument should always be the callback to the next
// middleware function.
MyClass.use( function ( _three, _next ) {

  _next( _three, 4 );

} ).use( function ( _three, _four, _next ) { // `use` can be chained

  _next( _three + _four + 5 );

} );

// This is a named middleware function. By default, middleware functions are named "all". This
// will give you the ability to add multiple middleware injection points.
MyClass.use( "postInit", function ( _next ) {

  this.value = 10;
  _next();

} );

// Runs the middleware queue of functions. The last argument of `run` is a callback that is 
// triggered after the queue is emptied.

var myClass = new MyClass();

myClass.init();
```

## API
---

### Useify(object)

Add `Useify` to an object

### object.use([params...], [fn])

Adds a middleware function to the queue. A dynamic amount of params can be passed from the previous middleware function. The callback must be the last paramater. `use` is chainable.

### object.middleware([params...], [fn])

Runs the queue of middleware functions in the order they were added. A dynamic amount of params can be passed from the previous middleware function. The callback must be the last paramater.