# Tapable
## Basic intro
As mention by Sean Larkinn in serveral conferences, it is a 235 line library which works like a node `EventEmitter` to provide hooks for plugins to tap into.

Tapable implements a `Hook interface` imported for all types Hooks.

### `HookCodeFactory` methods
- constructor(config)
- create(options)
- setup(instance, options)
- init(options)
- deinit()
- contentWithInterceptors(options)
- header()
- needContext()
- callTap(tapIndex, { onError, onResult, onDone, rethrowIfPossible })
- callTapsSeries({onError, onResult, resultReturns, onDone, doneReturns, rethrowIfPossible })
- callTapsLooping({ onError, onDone, rethrowIfPossible })
- callTapsParallel({ onError, onResult, onDone, rethrowIfPossible, onTap = (i, run) => run()})
- getTapFn(idx)
- getTap(idx)
- getInterceptor(idx)

## My doubts
- I am searching for libraries which uses tapable under the hood to understand the various ways this can be used. Just to realise its power.
