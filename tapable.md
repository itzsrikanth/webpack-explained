# Tapable
## Basic intro
As mention by Sean Larkinn in serveral conferences, it is a 235 line library which works like a node `EventEmitter` to provide hooks for plugins to tap into.

Tapable implements a `Hook interface` imported for all types Hooks.

### `HookCodeFactory` methods
- constructor(config)
- create(options) - creates function using `new Function` syntax based on options passed (sync/async/promise). Following that, `args()`, `headers()` and `contentWithInterceptors()` are used to create the function template.
- setup(instance, options)
- init({ type: "sync" | "promise" | "async", taps: Array<Tap>, interceptors: Array<Interceptor> }):
  save a copy of options in `this` context
- deinit(): unset all `this` context as `undefined`
- contentWithInterceptors(options)
  - Unable to figure out where `this.content` is set
  - got three events- `onError`, `onResult` and `onDone`
  - returns a string for each interceptor as below. Similarly for result and done.
    ```
    _interceptors[0].error([error Object]);
    ${onError(err)}
      .
      .
      .
    ```
- header() - adds certain variable declaration as header to all the function.
- needContext() - checks if any of `this.options.taps` contains truthy `context` key
- callTap(tapIndex, { onError, onResult, onDone, rethrowIfPossible })
- callTapsSeries({onError, onResult, resultReturns, onDone, doneReturns, rethrowIfPossible })
- callTapsLooping({ onError, onDone, rethrowIfPossible })
  - if every callback is not of type `sync`, code added in a function with a flag (_loopAsync) and function call made
- callTapsParallel({ onError, onResult, onDone, rethrowIfPossible, onTap = (i, run) => run()})
  - if `taps` are less than 2, `callTapsSeries` is triggered
- args({ before, after } = {}) - return comma separated list of arguments as string for `new Function` syntax
- getTapFn(idx) - return `_x[0]`
- getTap(idx) - return `_taps[0]`
- getInterceptor(idx) - return `_interceptors[0]`
  
### `Hook` interface methods
Prototype inheritance is disabled
- compile(): abstract method to be overridden
- _createCall(): returns an instance of compiler 
- _tap()
- tap()
- tapAsync()
- tapPromise()
- _runRegisterInterceptors()
- withOptions()
- isUsed()
- intercept()
- _resetCompilation()
- _insert()
  
## Coding style learnings:
- we can use `new Function` to create a function using string/templating.
- when we want to assign a value to key in object only if certain condition is true, we can do:
  ```javascript
  var obj = {
    key: condition && value
  };
  ```

## My doubts
- I am searching for libraries which uses tapable under the hood to understand the various ways this can be used. Just to realise its power.
