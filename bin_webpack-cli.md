# .node_modules/webpack-cli/bin/cli.js

In this package or its sub-modules, `webpack` is invoked several times.

## .node_modules/webpack-cli/bin/utils/errorHelpers.js
### Methods
- `cutOffWebpackOptions()`:\
  Filter `error.stack` lines containing `WEBPACK_OPTIONS` only
- `cutOffMultilineMessage()`:\
  Merge `error.message` to `error.stack` which are not already present in `error.stack`
- `cleanUpWebpackOptions()`:\
  Call above two methods
  
## Main IIFE
- find if `argv` contains any other value except `serve`.
  ```javascript
  const NON_COMPILATION_ARGS = ["init", "migrate", "serve", "generate-loader", "generate-plugin", "info"];
  const NON_COMPILATION_CMD = process.argv.find(arg => {
		if (arg === "serve") {
			global.process.argv = global.process.argv.filter(a => a !== "serve");
			process.argv = global.process.argv;
		}
		return NON_COMPILATION_ARGS.find(a => a === arg);
	});
  ```
- Once the `argv` parsing is done, the `webpack` `compiler` is invoked with `options` as argument.
- `compilerCallback()`
  - if `argv.progress` is truthy, it invokes `ProgressPlugin`
  - if `verbose` then `hooks.done` is tapped with `compilation` information
  - if `watch` is enabled, `hooks.watchRun` is tapped, else `hooks.beforeRun` is tapped.
  - `compilerCallback(err, stats)`
    - When `watch` is disabled or when error occurs, we can clear the cache
  - In `watch` mode, `compiler.watch` is triggered, else `compiler.run`
- While invoking compiler, `compilerCallback` is passed as callback.

## .node_modules/webpack-cli/bin/utils/prompt-command.js
Objective is to prompt users to install necessary packages for the command. It seems that it used to be part of existing package and got moved as part of separate package.
- Say, if we use `webpack init`, it will check if `@webpack-cli/init` is install. If not it will try to install the package.


## Doubts
- ~~Unable to find source of `compiler.purgeInputFileSystem()` which removes cache~~\
It is `Compilation.purgeInputFileSystem` which calls `CachedInputFileSystem.purge()`
