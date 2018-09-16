---
date: "2018-09-12"
title: "Setting up a NodeJS NPM Library"
category: "Javascript"
---

Recently at work, we needed to create a few new NPM libraries to be used in our NodeJS backend.
The first question that popped into my head was should i use a build tool?
If i can, can i use webpack for it?

## Why do i need a build tool for backend?
Build tools such as Webpack are commonly used in the frontend to bundle our javacripts files
before loading into the browser. It also provides a way for us to transpile,
uglify, minify and even hot reload any changes to our code. 

While Webpack is extremly useful in the front end, it is also useful in the backend as well.
Some use cases:
- optmizations such as removing comments, dead code, uglify etc
- code anaylsis such as eslint, flow
- includes polyfill and shims for older version of nodeJS

We chose Webpack because our frontend already uses webpack and we wanted to stick with one build
tool just to be consistent. Besides, do we really want to maintain two completely different build tools when they are solving the same problem?

## Things to look out for

Webpack by default tries to bundle all our dependencies into one file, we don't want that for our backend. Let's look at a simple webpack config file:

```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  target: 'node',
  output: {
    path: path.join(__dirname, 'dist'),
    filename: 'build.js'
  }
}
```
The most important line here is `target: 'node'`. It tells Webpack not to bundle any built-in NodeJS modules like fs or else you will get something like

    Can't resolve 'fs' webpack


There is one more thing, Webpack will also try to load and bundle node modules into our final build. A sign this is happening when you are getting error message like this: 

    WARNING in ./node_modules/colors/lib/colors.js 127:29-43
    Critical dependency: the request of a dependency is an expression
    @ ./node_modules/colors/safe.js

Luckily for us, there is a great library called [`webpack-node-externals`](https://github.com/liady/webpack-node-externals) which has provided a solution. All we need to do is
to add this to our webpack config.

```javascript
const nodeExternals = require('webpack-node-externals');
...
module.exports = {
    ...
    target: 'node', // in order to ignore built-in modules like path, fs, etc.
    externals: [nodeExternals()], // in order to ignore all modules in node_modules folder
    ...
};
```
Easy right?

Now the webpack will happily build our code and produce the build.js in our dist folder.

For reference here is our final webpack config:

```javascript
const webpack = require('webpack');
const nodeExternals = require('webpack-node-externals');
const path = require('path');
const env = require('yargs').argv.env; // use --env with webpack 2
const pkg = require('./package.json');

let libraryName = pkg.name;

let outputFile, mode;

if (env === 'build') {
  mode = 'production';
  outputFile = libraryName + '.min.js';
} else {
  mode = 'development';
  outputFile = libraryName + '.js';
}

const config = {
  mode: mode,
  entry: __dirname + '/src/index.js',
  devtool: 'source-map',
  output: {
    path: __dirname + '/lib',
    filename: outputFile,
    library: libraryName,
    libraryTarget: 'umd',
    umdNamedDefine: true
  },
  module: {
    rules: [
      {
        test: /(\.jsx|\.js)$/,
        loader: 'eslint-loader',
        exclude: /node_modules/
      }
    ]
  },
  resolve: {
    modules: [path.resolve('./node_modules'), path.resolve('./src')],
    extensions: ['.json', '.js']
  },
  target: 'node', // in order to ignore built-in modules like path, fs, etc.
  externals: [nodeExternals()]
};

module.exports = config;

```

## Conclusion

See! It wasn't that hard right? Now our shiny new npm libraries are getting built by webpack, pipe through to eslint for validations and then finally splits out a new js file in our dist folder.
