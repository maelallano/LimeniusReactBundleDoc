# Limenius ReactBundle

## Installation

### Create your project:
```
composer create-project symfony/framework-standard-edition yourProjectName "2.7.*"
```

### Download the bundle
```
composer require limenius/react-bundle
```

### Enable it
```php
// app/AppKernel.php

// ...
class AppKernel extends Kernel
{
    public function registerBundles()
    {
        $bundles = array(
            // ...

            new Limenius\ReactBundle\LimeniusReactBundle(),
        );

        // ...
    }

    // ...
}
```
## JavaScript and Webpack Set Up
In order to use React components you need to register them in your JavaScript. This bundle makes use of the React On Rails npm package to render React Components:
```
npm install react-on-rails
```
You can register a component using React On Rails like this:
```js
import ReactOnRails from "react-on-rails";
import App from "./App";

ReactOnRails.register({ App: App });
```

Insert a component in a twig template like so:
```js
{{ react_component('App')}}
```
*with props*:
```js
{{ react_component('App', {'props': props}) }}
```
Props can either be a JSON encoded string or an array. For instance, a controller action that will produce a valid props could be:
```php
/**
 * @Route("/recipes", name="recipes")
 */
public function homeAction(Request $request)
{
    $serializer = $this->get('serializer');
    return $this->render('recipe/home.html.twig', [
        'props' => $serializer->serialize(
            ['recipes' => $this->get('recipe.manager')->findAll()->recipes], 'json')
    ]);
}
```

*you can also choose where to render it:*
```js
{{ react_component('App', {'rendering': 'client_side'}) }}
```
```js
{{ react_component('App', {'rendering': 'server_side'}) }}
```
```js
{{ react_component('App', {'rendering': 'both'}) }}
```
## webpack-encore
Download it:
```
yarn add @symfony/webpack-encore --dev
```
Next, create a new webpack.config.js file at the root of your project:
```js
var Encore = require('@symfony/webpack-encore');

Encore
    // directory where all compiled assets will be stored
    .setOutputPath('web/build/')
    // what's the public path to this directory (relative to your project's document root dir)
    .setPublicPath('/build')
    // empty the outputPath dir before each build
    .cleanupOutputBeforeBuild()
    // will output as web/build/app.js
    .addEntry('js/app', ['babel-polyfill', './assets/js/entryPoint.js'])
    // allow legacy applications to use $/jQuery as a global variable
    .autoProvidejQuery()
    // create hashed filenames (e.g. app.abc123.css)
    .enableVersioning(Encore.isProduction())
    .enableSourceMaps(!Encore.isProduction())
    .enableReactPreset()
    .configureBabel(function(babelConfig) {

    //This is needed.

    babelConfig.plugins = ["transform-object-rest-spread","transform-class-properties"]
})

// export the final configuration
module.exports = Encore.getWebpackConfig()
```

And also a webpack.config.serverside.js file:
```js
var Encore = require('@symfony/webpack-encore');

Encore
    // directory where all compiled assets will be stored
    .setOutputPath('app/Resources/webpack/')
    // what's the public path to this directory (relative to your project's document root dir)
    .setPublicPath('/')
    // empty the outputPath dir before each build
    .cleanupOutputBeforeBuild()
    // will output as app/Resources/webpack/server-bundle.js
    .addEntry('server-bundle', ['babel-polyfill', './assets/js/entryPoint.js'])
    // allow legacy applications to use $/jQuery as a global variable
    .autoProvidejQuery()
    .enableReactPreset()
    .configureBabel(function(babelConfig) {

    //This is needed.

    babelConfig.plugins = ["transform-object-rest-spread","transform-class-properties"]
})

// export the final configuration
module.exports = Encore.getWebpackConfig()
```

To get all the necessary dependencies paste this in your **package.json** file:
```json
{
  "name": "symfony-react-sandbox",
  "version": "1.0.0",
  "description": "Symfony React Sandbox",
  "main": "index.js",
  "directories": {
    "test": "tests"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "webpack-serverside": "encore dev --config webpack.config.serverside.js --watch",
    "webpack": "encore dev-server",
    "lint": "eslint client/js"
  },
  "keywords": [
    "symfony",
    "react",
    "jsx",
    "reactjs",
    "server-side",
    "rendering",
    "isomorphic"
  ],
  "license": "MIT",
  "dependencies": {
    "babel-plugin-transform-class-properties": "^6.24.1",
    "babel-polyfill": "^6.26.0",
    "babel-preset-react": "^6.24.1",
    "babel-preset-stage-1": "^6.24.1",
    "bootstrap-sass": "^3.3.7",
    "classnames": "^2.2.5",
    "history": "^4.7.2",
    "jquery": "^3.2.1",
    "js-yaml": "^3.10.0",
    "jwt-decode": "^2.2.0",
    "liform-react": "^0.6.9",
    "lodash": "^4.17.4",
    "moment": "^2.18.1",
    "prop-types": "^15.6.2",
    "react": "^16.4.2",
    "react-dom": "^16.4.2",
    "react-helmet": "^5.2.0",
    "react-on-rails": "^10.0.1",
    "react-redux": "^5.0.6",
    "react-router-dom": "^4.3.1",
    "redux": "^3.7.2",
    "redux-thunk": "^2.2.0",
    "webpack": "^3.8.1",
    "whatwg-fetch": "^2.0.3",
    "yarn": "^1.9.4"
  },
  "devDependencies": {
    "@symfony/webpack-encore": "^0.16.0",
    "babel-cli": "^6.26.0",
    "babel-core": "^6.26.0",
    "babel-eslint": "^8.0",
    "babel-loader": "^7.1.2",
    "babel-plugin-transform-object-rest-spread": "^6.26.0",
    "babel-plugin-transform-react-jsx": "^6.24.1",
    "babel-preset-env": "^1.6.0",
    "babel-runtime": "^6.26.0",
    "case-sensitive-paths-webpack-plugin": "^2.1.1",
    "child_process": "^1.0.2",
    "eslint": "4.x",
    "eslint-config-rackt": "^1.1.1",
    "eslint-plugin-react": "^7.4.0",
    "node-sass": "^4.5.3",
    "sass-loader": "^6.0.6"
  }
}

```