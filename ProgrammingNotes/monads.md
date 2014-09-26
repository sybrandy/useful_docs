# Standard Functions

* function unit(value)
* function bind(monad, f(value))

All three functions return a monad.

# Simple definition

Monad == Object/other

unit == constructor

# Axioms (Standard Notation)

* bind(unit(value), f) == f(value)
* bind(monad, unit) == monad
* bind(bind(monad, f), g) == bind(monad, function(val) { return bind(f(val), g); })

# Syntax

bind(monad, func) == monad.bind(func)

Procedural/Functional -> OO Syntax

# Axioms (Methodical/OO Notation)

* unit(value).bind(f) == f(value)
* monad.bind(unit) == monad
* monad.bind(f).bind(g) == monad.bind(function(val) { return f(value).bind(g); })

# Code Example 1

Javascript:

    function MONAD()
    {
        return function unit(val)
        {
            var monad = Object.create(null);
            monad.bind = function(func)
            {
                return func(value);
            };
            return monad;
        };

        return unit;
    }

    var identity = MONAD();
    var monad = identity("Hello world.");
    monad.bind(alert); // Calls alert to display "Hello world."

# Expanding bind

    monad.bind(func) -> monad.bind(func, [a, b, c])

    monad.method() -> monad.method(a, b, c)

    function MONAD()
    {
        var prototype = Object.create(null); // Object that contains the
                                             // methods for the monad.
        return function unit(val)
        {
            var monad = Object.create(prototype); // monads inherit from prototype
            monad.bind = function(func, args)
            {
                return func(value, ...args);  // ES6 syntax for passing
                                              // arguments from args to func.
            };
            return monad;
        };

        // This adds new methods to prototype.
        unit.method = function(name, func)
        {
            prototype[name] = func;
            return unit;
        };

        // Supercedes unit.method
        // Takes an ordinary function and makes it act as if it knows about
        // monads.
        unit.lift = function(name, func)
        {
            prototype[name] = function(...args)
            {
                return unit(this.bind(func, args));
            };
            return unit;
        };
    }

    var ajax = MONAD().lift('alert', alert);
    var monad = ajax("Hello world.");
    monad.alert();

# Maybe monad

    function MONAD(modifier)
    {
        var prototype = Object.create(null); // Object that contains the
                                             // methods for the monad.
        return function unit(val)
        {
            var monad = Object.create(prototype); // monads inherit from prototype
            monad.bind = function(func, args)
            {
                return func(value, ...args);  // ES6 syntax for passing
                                              // arguments from args to func.
            };
            if (typeof modifier === 'function')
            {
                modifier(monad, value);
            }
            return monad;
        };
    }

    var maybe = MONAD(function (monad, value)
    {
        if (value === null || value === undefined)
        {
            monad.is_null = true;
            // Changes the bind function to simply return the monad.
            monad.bind = function()
            {
                return monad;
            };
        }
    });

    var monad = maybe(null);
    monad.bind(alert);  // Now, nothing happens if the monad is null.
