### Setup react with webpack and babel ###

Guide to setup react from scratch, without using create-react-app and by configuring webpack and babel manually.

## Table of content ##

# Setup React
- Setup folder with npm and git
- Create HTML and Javascript (React) file
- Let’s write some code

# Setup webpack
- Install webpack
- Add configuration file
- Define entry point for webpack
- Define output path for the bundled file
- Add the bundled js file to the HTML file

# Setup Babel
- Install babel
- Configure webpack to use babel
- Defining rules (presets) for babel

# Build and run
# Extra useful configs
- Babel config for CSS files
- Babel config for images
- Babel config for SVG as react component

## Why do we need webpack?
If you have written react code you would know that it requires writing a lot of javascript files and importing these files to other files. We do this using some sort of module system, two most popular module systems are CommonJS’s require() and ESM’s import. Unfortunately, there is no browser support for CommonJs and support for ESM is not universal yet. So we need a tool that can take all the files that we have written and combine/bundle them into a single js file while keeping track of their dependencies. The tool that does this are called bundlers and webpack is one of the many javascript bundlers!

# Ok, But why Babel?

Good Question! The answer to this also has a similar(ish) theme. Let’s start this answer by asking a different question. What is babel? Babel is a transpiler! Cool, and what is a transpiler? Transpiler is a program that converts 1 type of source code to another type of source code. In our case, it takes all the fancy javascript (react, newer ECMAScript features, typescript etc) and converts it to old school browser understandable javascript.

Usually, all this is abstracted by react-react-app which is a good starting point. But we don’t want to use that, we love setting things up ourselves!

## Setup React
# Setup folder with npm and git

mkdir my-app
cd my-app
npm init

At this point, npm will ask you to answer some basic questions about your project and then will create a package.json file in the root directory.

Optionally you can also set up git for your project by using his command
git init .

# Create HTML and Javascript (React) file
Start by creating a new source folder

mkdir src
cd src


# Now let’s add an HTML and javascript file
touch index.html
touch index.js

## Let's write some code
# First, we need to install react and react-dom
npm install react react-dom
Now, we add code to the files we just created.

# Index.html

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>React App</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
  </body>
</html>


# Index.js

import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
    <h1>Hello World</h1>,
  document.getElementById('root')
);


## Setup webpack ##
# Install webpack
First, we need to install webpack to our project.
npm install webpack webpack-cli webpack-dev-server --save-dev
This install 3 packages main webpack package, webpack-cli to run webpack commands and webpack-dev-server to run react locally.

# Add configuration file
Once we install the webpack it's time to configure it. We can do that by adding webpack configuration file to the root folder.

touch webpack.config.js

# Define entry point for webpack
First, we need to tell webpack where to start bundling the javascript files, this we can do by specifying entry property. Learn more about the entry field.

const path = require('path');

module.exports = {
  entry: path.join(__dirname, "src", "index.js"),
}


# Here we are using path this is native NodeJs module, it helps us in the concatenation of file paths.

## Define output path for the bundled file 
const path = require('path');

module.exports = {
  entry: path.join(__dirname, "src", "index.js"),
  output: {
    path:path.resolve(__dirname, "dist"),
  }
}

# Here we tell webpack to create the final bundled file in dist folder in the root of the project. Learn more about the output field.

## Add the bundled js file to the HTML file
Once the bundled javascript file is created we need to tell webpack to inject it as a script tag to the HTML file. To do that we first need to install a webpack plugin that will help us do it.

npm install html-webpack-plugin --save-dev

# Now we are going to use this plugin.

const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: path.join(__dirname, "src", "index.js"),
  output: {
    path:path.resolve(__dirname, "dist"),
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.join(__dirname, "src", "index.html"),
    }),
  ],
}

# This will take the /public/index.html and inject script tag to it. And move that HTML file to the dist folder. Learn more about this plugin.

## Setup Babel

# Install babel
npm install @babel/core babel-loader --save-dev

# Here we are installing @babel/core which is the core transpiler. Then we have babel-loader which is a webpack loader that will help webpack to use babel transpiler. Learn more about @babel/core and babel-loader.

## Configure webpack to use babel
# We now need to tell webpack to transpile javascript files using babel before bundling them. To do that we need to define some rules for the module bundling.

const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: path.join(__dirname, "src", "index.js"),
  output: {
    path:path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
        test: /\.?js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
        }
      },
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.join(__dirname, "src", "index.html"),
    }),
  ],
}

# Here we tell webpack to use babel-loader to transpile files that end with .js. Learn more about the rules field.

## Defining rules for babel
# Babel is a transpiler so we need to tell it what to transpile, we do this using presets. These are predefined configuration that is used to transpile different type to javascript to browsers understandable one.



# Let’s install these presets first.
npm install @babel/preset-env @babel/preset-react --save-dev
Now let's use them in webpack.config.js


const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: path.join(__dirname, "src", "index.js"),
  output: {
    path:path.resolve(__dirname, "dist"),
  },
  module: {
    rules: [
      {
        test: /\.?js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ['@babel/preset-env', '@babel/preset-react']
          }
        }
      },
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.join(__dirname, "src", "index.html"),
    }),
  ],
}


Here we have 2 presets @babel/preset-env for transpiling ES2015+ syntax and we have @babel/preset-react for transpiling react code. Learn more about these presets.

## Build and run
Now that we have created the config file it is time to actually build and run the app. To do that we need to add some scripts to package.json.

"scripts": {
  "dev": "webpack serve",
  "build": "webpack",
  ...
}


dev will use the webpack dev server and run the application locally. build will create a bundle of assets that can be deployed to servers.

# Run locally
npm run dev

# Build bundle
npm run build

## Extra useful configs

# Babel config for CSS files
module.export = {
  ...
  "module": {
    "rules": [
      {
        test: /\.css$/i,
        use: ["style-loader", "css-loader"],
      },
      ....
    },
    ...
  },
  ...
}

# Babel config for images
module.export = {
  ...
  "module": {
    "rules": [
      {
        test: /\.(png|jp(e*)g|svg|gif)$/,
        use: ['file-loader'],
      },
      ....
    },
    ...
  },
  ...
}

# Babel config for SVG as react component
module.export = {
  ...
  "module": {
    "rules": [
      {
        test: /\.svg$/,
        use: ['@svgr/webpack'],
      },
      ....
    },
    ...
  },
  ...
}
