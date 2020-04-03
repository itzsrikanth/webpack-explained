# Debug Webpack flow
The VS Code is an amazing tool to debug node code. I am now able to understand the flow.

What I learned for CLIs written in NodeJs is to read its `package.json`. It has the following added:
```json
// For webpack 
"bin": "./bin/webpack.js"

// For webpack-cli
"bin": {
    "webpack-cli": "./bin/cli.js"
 }
 ```
 This makes us understand the entrypoint for that package. So we arrive at simple `VS Code` `launch.json` as below:
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "webpack",
            "skipFiles": [
                "<node_internals>/**"
            ],
            "program": "${workspaceFolder}/node_modules/webpack/bin/webpack.js",
            "args": [
                "--mode", "development",
                "--config", "./webpack.test.config.js"
             ]
        }
    ]
}
```
Now, we can place breakpoints in `${workspaceFolder}/node_modules/webpack/bin/webpack.js`, followed by `${workspaceFolder}/node_modules/webpack-cli/bin/cli.js`.
