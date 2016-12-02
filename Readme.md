# README

The following work is based on the following tutorial:
[A Beginner's Guide to Setup React.js Environment Using Npm, Babel 6 and Webpack](http://blog.tamizhvendan.in/blog/2015/11/23/a-beginner-guide-to-setup-react-dot-js-environment-using-babel-6-and-webpack/)

```shell
mkdir react-hello-world
cd react-hello-world
npm init
npm i webpack -S
touch webpack.config.js


``
**webpack.config.js**
```javascript
var webpack = require('webpack');
var path = require('path');

var BUILD_DIR = path.resolve(__dirname, 'src/client/public');
var APP_DIR = path.resolve(__dirname, 'src/client/app');

var config = {
  entry: APP_DIR + '/index.jsx',
  output: {
    path: BUILD_DIR,
    filename: 'bundle.js'
  }
};

module.exports = config;
```
- Create an index.jsx file in ./src/client/app folder
```shell
touch ./src/client/app/index.jsx
./node_modules/.bin/webpack -d
touch src/client/index.html
```
**index.html**
```html
<html>
  <head>
    <meta charset="utf-8">
    <title>React.js using NPM, Babel6 and Webpack</title>
  </head>
  <body>
    <div id="app" />
    <script src="public/bundle.js" type="text/javascript"></script>
  </body>
</html>
```
```shell
npm i babel-loader babel-core babel-preset-es2015 babel-preset-react -S
```
  - **Note:** I was getting an error until I installed the 'babel-core' package
```shell
touch .babelrc
```
**.babelrc**
```
{
  "presets" : ["es2015", "react"]
}
```

**webpack.config.js**
```javascript
var config = {
  entry: APP_DIR + '/index.jsx',
  output: {
    path: BUILD_DIR,
    filename: 'bundle.js'
  },
  // new code
  module : {
      loaders : [
        {
          test : /\.jsx?/,
          include : APP_DIR,
          loader : 'babel'
        }
      ]
    }
};
```
```shell
npm i react react-dom -S
```
**index.jsx**
```javascript
import React from 'react';
import {render} from 'react-dom';

class App extends React.Component {
  render () {
    return <p> Hello React!</p>;
  }
}

render(<App/>, document.getElementById('app'));
```
```shell
./node_modules/.bin/webpack -d
./node_modules/.bin/webpack -d --watch
```
**packages.json**
```json
	// code
	"scripts": {
	    "dev": "webpack -d --watch",
	    "build" : "webpack -p"
	  },
	// code
```
```shell
touch ./src/client/app/AwesomeComponent.jsx
```
```javascript
import React from 'react';

class AwesomeComponent extends React.Component {

  constructor(props) {
    super(props);
    this.state = {likesCount : 0};
    this.onLike = this.onLike.bind(this);
  }

  onLike () {
    let newLikesCount = this.state.likesCount + 1;
    this.setState({likesCount: newLikesCount});
  }

  render() {
    return (
      <div>
        Likes : <span>{this.state.likesCount}</span>
        <div><button onClick={this.onLike}>Like Me</button></div>
      </div>
    );
  }

}

export default AwesomeComponent;
```
**index.jsx**
```javascript
// ...
import AwesomeComponent from './AwesomeComponent.jsx';
// ...
class App extends React.Component {
  render() {
    return (
        <div>
          <p> Hello React!</p>
          <AwesomeComponent />
        </div>
    );
  }
}
// ...
```











