# CachePlugin
In case of multi-compiler setup, a new instance of `CachePlugin` is instanciated for each of them.

## thisCompilation
Cache is added recursived to all `childCompiler`.

## run
- During run, if cache (`_lastCompilationFileDependencies`) is empty, plugin exits by calling the callback
- else timestamp details are updated for the dependencies, then callback

## afterCompile
After each compile, file and context dependencies are stored in cache.