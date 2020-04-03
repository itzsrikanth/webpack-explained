# `./bin/webpack.js`

## Objective
The main objective of this file is to spawn a process which will takeover the building process

## Main Dependencies
- `child_process`
  NodeJs internal implementation to spawn a `new process` to execute a command
  
## Methods
- `runCommand()`
  As seen in [Flow file](./flow.md), Webpack expects either of `webpack-cli` or `webpack-command`. Based on installed package the execution begins. If both installed, it is requested to remove either one.
  
