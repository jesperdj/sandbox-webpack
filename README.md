# Setting up a basic webpack project

A simple example that shows you how to setup a HTML / SASS / JavaScript project with [webpack](https://webpack.js.org/).

Useful [video tutorial by DesignCourse](https://www.youtube.com/watch?v=TzdEpgONurw).

## Preparation

Create a directory for the project and create the project with `npm init`:

```bash
mkdir sandbox-webpack
cd sandbox-webpack
npm init
```

Install `webpack` and `webpack-cli` as development dependencies:

```bash
npm i -D webpack webpack-cli
```

Create source folder and main source file:

```bash
mkdir src
touch src/index.js
```

Edit `package.json`. Edit the `"scripts"` section; remove the `"test"` script and add a `"build"` script so that it looks like this:

```json
"scripts": {
    "build": "webpack"
}
```

The line `"main": "index.js"` can also be removed.

Now it's possible to build the project with:

```bash
npm run build
```

## Add a webpack configuration file

Create a webpack configuration file:

```bash
touch webpack.config.js
```

Basic structure of this file:

```javascript
module.exports = {
    module: {
        rules: [
            // add rules here
        ]
    },
    plugins: [
        // add plugins here
    ],
    mode: "production"
}
```

## Support for HTML

Install the HTML webpack plugin and HTML loader as development dependencies:

```bash
npm i -D html-webpack-plugin html-loader
```

Add a rule to the module in the webpack configuration file and also configure the plugin:

```javascript
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
    module: {
        rules: [
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: "./src/index.html",
            filename: "./index.html"
        })
    ],
    mode: "production"
}
```

## Support for SASS

Install the necessary packages:

```bash
npm i -D node-sass style-loader css-loader sass-loader mini-css-extract-plugin
```

Add a directory and create a SASS file:

```bash
mkdir -p src/css
touch src/css/main.scss
```

When you are working with webpack, you don't directly put a `<link rel=stylesheet ...>` link in the header of your HTML file. Instead, you import it in `index.js`:

```javascript
import './css/main.scss';
```

Add a rule and configure the plugin, similar to what was necessary for HTML. The webpack configuration file now looks like this:

```javascript
const HtmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
    module: {
        rules: [
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            },
            {
                test: /\.scss$/,
                use: [
                    "style-loader",
                    "css-loader",
                    "sass-loader"
                ]
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: "./src/index.html",
            filename: "./index.html"
        }),
        new MiniCssExtractPlugin({
            filename: "[name].css",
            chunkFilename: "[id].css"
        })
    ],
    mode: "production"
}
```

## Support for images

For images, install `file-loader`:

```bash
npm i -D file-loader
```

Add the necessary configuration to the webpack configuration file.

NOTE: There is a bug in the current version of webpack and/or `file-loader` that will cause image URLs to be rendered the wrong way. Instead of a correct URL, you'll get `<img src="[object Module]">` in the generated code. The workaround for now is to add `options: { esModule: false }` to the configuration for `file-loader`.

More info [on StackOverflow](https://stackoverflow.com/questions/59070216/webpack-file-loader-outputs-object-module) and in this [bug report](https://github.com/webpack/webpack/issues/10053).

The complete configuration file should look like this:

```javascript
const HtmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
    module: {
        rules: [
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            },
            {
                test: /\.scss$/,
                use: [
                    "style-loader",
                    "css-loader",
                    "sass-loader"
                ]
            },
            {
                test: /\.(png|svg|jpg|gif)$/,
                use: [
                    {
                        loader: "file-loader",
                        options: { esModule: false } // workaround for bug
                    }
                ]
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: "./src/index.html",
            filename: "./index.html"
        }),
        new MiniCssExtractPlugin({
            filename: "[name].css",
            chunkFilename: "[id].css"
        })
    ],
    mode: "production"
}
```

## Webpack development server

Install the webpack development server as a development dependency:

```bash
npm i -D webpack-dev-server
```

Add a line to `"scripts"` in `package.json`:

```json
"scripts": {
    "build": "webpack",
    "start:dev": "webpack-dev-server"
}
```

Now you can start the development server, with live reload, with:

```bash
npm run start:dev
```
