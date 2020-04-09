# Webpack
- As other modules, schema validation is done
- Initially check is done for multi-compiler option by testing if option/config passed is and array. If yes, then for each option is passed through webpack constructor.
- Inside the main file, the options are fetched and remaining options are supplied from `WebpackOptionsDefaulter`
- Get compiler instance using current configuration
- Apply `NodeEnvironmentPlugin`
- Apply/invoke all plugins with `Compiler` context
## WebpackOptionsApply
This module applies corresponding module based on the option with config passed to them as argument. For the optimization configs, they are conditionally loaded.
- web:
  - `JsonpTemplatePlugin`
  - `FetchCompileWasmTemplatePlugin`
  - `FunctionModulePlugin`
  - `NodeSourcePlugin`
  - `LoaderTargetPlugin`
- webworker:
  - `WebWorkerTemplatePlugin`
  - `FetchCompileWasmTemplatePlugin`
  - `FunctionModulePlugin`
  - `NodeSourcePlugin`
  - `LoaderTargetPlugin`
- node, async-node:
  - `NodeTemplatePlugin`
  - `ReadFileCompileWasmTemplatePlugin`
  - `FunctionModulePlugin`
  - `NodeTargetPlugin`
  - `LoaderTargetPlugin`
- node-webkit:
  - `JsonpTemplatePlugin`
  - `FunctionModulePlugin`
  - `NodeTargetPlugin`
  - `ExternalsPlugin`
  - `LoaderTargetPlugin`
- electron-main:
  - `NodeTemplatePlugin`
  - `FunctionModulePlugin`
  - `NodeTargetPlugin`
  - `ExternalsPlugin`
  - `LoaderTargetPlugin`
- electron-renderer, electron-preload:
  - `JsonpTemplatePlugin`
  - `NodeTemplatePlugin`
  - `FetchCompileWasmTemplatePlugin`
  - `FunctionModulePlugin`
  - `NodeTargetPlugin`
  - `ExternalsPlugin`
  - `LoaderTargetPlugin`
## Source
A separate data-structure exists to store the source-code of the modules which are compiled
## OptionsDefaulter
- `defaults`:\
  self explanatory
- `config`:\
  It is stored as `call` | `make` | `append`. Based on this value, `defaults` is modified using `process()`.

## WebpackOptionsDefaulter extends OptionsDefaulter

## Compiler
### Methods
- `constructor`
  - Hooks declared and defined
  - `ResolverFactory` instance created
- `readRecords(callback)`:\
  - In my understanding, this method reads JSON from `this.recordsInputPath` , if value set, using `this.inputFileSystem` which is `CachedInputFileSystem`
  - calls `callback` passed
  - Value is set inside `WebpackOptionsApply`
- `createChildCompiler()`:\
  - There is a option to create child compiler using the same context as main compiler. The existing properties are added to the childCompiler and extended as part of main Compiler instance. The same can done from `Compilation` instance by using the method with same name.
- `compile(callback)`:\
  - params = `newCompilationParams()`
  - trigger `hooks.beforeCompile` with `params` passed
  - trigger `hooks.compile`
  - create new `Compilation` object
  - `hooks.make` with `compilation` passed. Once module building is done, `compilation.finish` is called.
  - call `compilation.seal`
  - call callback
- `newCompilationParams()`:\
  - call `createNormalModuleFactory` and `createContextModuleFactory`
- `createNormalModuleFactory()`:\
  - new `NormalModuleFactory` instance
  - `hooks.normalModuleFactory` triggered with `normalModuleFactory` instance
- `createContextModuleFactory()`:\
  - new `ContextModuleFactory` instance
  - `hooks.ContextModuleFactory` triggered
- `newCompilation()`:\
  - create new `Compilation` object
  - trigger `hooks.thisCompilation` with `compilation`
  - trigger `hooks.compilation` with `compilation`
- `run(callback)`:\
  If already running, `ConcurrentCompilationError` is thrown
  - `startTime` recorded
  - triggers `beforeRun` hook which purge inputFileSystem.
    - `finalCallback()` is called on error. (This method is explained in [here](./bin_webpack-cli.md))
  - inside `hooks.beforeRun`, `hooks.run` hook is tapped. Here also, error triggeres `finalCallback()`
    - `this.compile(onCompiled)` is called
  - triggers `this.compile` which inturn calls `onCompiled` as callback
  - `onCompiled()`:\
    - It checks the plugin which hooks on `shouldEmit`.
      - If this returns `false`, it generates new `Stats` with `compilation` and ends the process with `hooks.done` being triggered.
      - Else `emitAssets()` called
- `emitAssets()`:\
  - call `emitRecords()`
- `emitRecords()`:\
## Compilation
### Methods
- `constructor(compiler)`
  - various properties are referenced from compiler instance
- `finish()`:\
  - when all modules are built, it triggers `hooks.finishModules` in `Compilation` with `this.modules` array
  - call `reportDependencyErrorsAndWarnings`
- `reportDependencyErrorsAndWarnings(module, blocks)`:\
  - For each module, their dependencies are checked and their warnings and error, if any, are reported **recursively**.
- `seal()`:\
  - contains huge number of events.
  - It also contains many optimizations and hashing process
  - Calls `unseal`
- `unseal()`
- `moduleTemplates` = `{ javascript, webassembly }`

### Event order
- `beforeRun`
  - `NodeEnvironmentPlugin` inputFileSystem.purge()
- `run`
- `normalModuleFactory`
- `contextModuleFactory`
- `beforeCompile`
- `compile`
  - `ExternalsPlugin`
- `thisCompilation`:\
  - `JsonpTemplatePlugin` (web)
  - `FetchCompileWasmTemplatePlugin` (web)
  - `LibraryTemplatePlugin`
- `compilation`:\
  - `FunctionModulePlugin` (web)
  - `NodeSourcePlugin.addExpression` (web)
  - `LoaderTargetPlugin` (web)
  - `EvalDevToolModulePlugin`
  - `JavascriptModulesPlugin`
  - `JsonModulesPlugin`
  - `WebAssemblyModulesPlugin`
- `make`
- `seal`
- `optimizeDependenciesBasic` || `optimizeDependencies` || `optimizeDependenciesAdvanced`
- `afterOptimizeDependencies`
- `beforeChunks`
- `optimizeModulesBasic` || `optimizeModules` || `optimizeModulesAdvanced`
- `afterOptimizeModules`
- `optimizeChunksBasic` || `optimizeChunks` || `optimizeChunksAdvanced`
- `afterOptimizeChunks`
- `optimizeTree`
- `afterOptimizeTree`
- `optimizeChunkModulesBasic` || `optimizeChunkModules` || `optimizeChunkModulesAdvanced`
- `afterOptimizeChunkModules`
- `shouldRecord`
- `reviveModules`
- `optimizeModuleOrder`
- `advancedOptimizeModuleOrder`
- `beforeModuleIds`
- `moduleIds`
- `optimizeModuleIds`
- `afterOptimizeModuleIds`
- `reviveChunks`
- `optimizeChunkOrder`
- `beforeChunkIds`
- `optimizeChunkIds`
- `afterOptimizeChunkIds`
- `recordModules`
- `recordChunks`
- `beforeHash`
- `afterHash`
- `recordHash`
- `beforeModuleAssets`
- `shouldGenerateChunkAssets`
- `beforeChunkAssets`
- `additionalChunkAssets`
- `record`
- `additionalAssets`
- `optimizeChunkAssets`
- `afterOptimizeChunkAssets`
- `optimizeAssets`
- `afterOptimizeAssets`
- `needAdditionalSeal`
- `unseal`
- `afterSeal`
- `afterCompile`
- `shouldEmit` || `done`
- `shouldEmit` && `needAdditionalPass` && `done` && `additionalPass`
- `callAsync`

#### Unsorted Events
- `contentHash`
  - `JavascriptModulesPlugin`
- `compilation.afterChunks`
  - `WebAssemblyModulesPlugin`
- `entryOptions`
  - `EntryOptionPlugin`
- `NormalModuleFactory.factory`
- `NormalModuleFactory.resolver`

## ResolverFactory.js
It has a Map, direct(WeakMap) or stringified(Map) which stores the `resolver` instance with options passed as second argument as key. If `resolver` is not found in cache, a new instance is created, stored in cache and returned.

## NormalModuleFactory
- `constructor()`
  - new `RuleSet` is created with passed in `module.rules` in config file, concatenated with some default rules loaded by `WebpackOptionsApply`.

## RuleSet
- The default and `module.rules` written in config files are resolved and converted into necessary data structures here
- If `rule` is a simple string, loader with same name is used
- Following parameters can be added as part of rules:
  - test
  - or
  - and
  - not
  - include
  - exclude
  - resource
  - realResource
  - resourceQuery
  - compiler
  - issuer
  - loader
  - loaders
  - query
  - options
  - use
  - rules
  - oneOf
  - type
  - resolve
- in `rules`, `test` & `include` creates an array of `matchers`. Whereas `exclude` create inverted match function pushed inside same array.

## RequestShortener.js
- `constructor(directoryName)`:\
  - Windows style path is converted to unix
  - Trailing slash removed
  - Special char escaped with slash. New regexp created out of it
  - current directory's parent directory is extracted and a new regexp is created for it if both parent and current directory are not same
  - Webpack current file is checked if is exists within the directory passed. If yes, then a new regexp is created for this path as well
- `shorten()`:\

## ConcatSource.js

## MainTemplate.js
This is the comment given in file itself:
```javascript
// __webpack_require__.s = the module id of the entry point
// __webpack_require__.c = the module cache
// __webpack_require__.m = the module functions
// __webpack_require__.p = the bundle public path
// __webpack_require__.i = the identity function used for harmony imports
// __webpack_require__.e = the chunk ensure function
// __webpack_require__.d = the exported property define getter function
// __webpack_require__.o = Object.prototype.hasOwnProperty.call
// __webpack_require__.r = define compatibility on export
// __webpack_require__.t = create a fake namespace object
// __webpack_require__.n = compatibility get default export
// __webpack_require__.h = the webpack hash
// __webpack_require__.w = an object containing all installed WebAssembly.Instance export objects keyed by module id
// __webpack_require__.oe = the uncaught error handler for the webpack runtime
// __webpack_require__.nc = the script nonce
```

## Doubts
- Not sure where it is added `_pluginCompat` but it is tapped by number of modules