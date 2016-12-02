# README

The following work is based on the following tutorial:
[A Beginner's Guide to Setup React.js Environment Using Npm, Babel 6 and Webpack](http://blog.tamizhvendan.in/blog/2015/11/23/a-beginner-guide-to-setup-react-dot-js-environment-using-babel-6-and-webpack/)

```shell
mkdir react-hello-world
cd react-hello-world
npm init
npm i webpack -S			# installs Webpack
touch webpack.config.js 	# creates Webpack config file
```
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
- APP_DIR => holds the directory path of the React app's codebase
  - src/client/app/index.jsx
- BUILD_DIR => represents the directory path of the bundle file output
- Create an index.jsx file in ./src/client/app folder
```shell
touch ./src/client/app/index.jsx
```
**index.jsx**
```javascript
console.log('Hello World');
```

```shell
# runs Webpack in development
./node_modules/.bin/webpack -d 
# This generates the bundle.js file and it's map file bundle.js.map
# Looking at the config file, notice how the 'entry' is the APP_DIR we defined plus
#   the name of the file we're loading, index.jsx
# This generated bundle.js file comes from the 'output' which takes the path of where
#   we want to send the file, BUILD_DIR, and the name of the file, 'bundle.js'


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
- Open index.html in your browser and see the 'Hello World!' in the console.
  - bundle.js now holds the code from index.jsx
- React is easier when written in JSX and ES6 BUT ES6 is not supported in all browser so we
need to have a JavaScript preprocessor...hence Babel
  - more specifically, we are bundling the Babel-loader which Webpack uses to translate our
  ES6 
```shell
# I added the 'babel-core' package; I was getting an error before that
npm i babel-loader babel-core babel-preset-es2015 babel-preset-react -S
```

```shell
touch .babelrc 				# create the Babel configuration file
```
**.babelrc**
```
{
  "presets" : ["es2015", "react"]
}
```
- Now that we have Babel, we need to update our Webpack config file and tell it to use the
Babel-loader while bundling files
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
- Now we are all set and can start writing React
```shell
npm i react react-dom -S  		# install react and react-dom
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
./node_modules/.bin/webpack -d 		# updates the bundle.js file
									# see the changes in index.html

# To avoid running the above command EVERY time you make a change, add the --watch flag
# to make it automatic.
# He also recommends using the react-hot-loader to refresh the browser
./node_modules/.bin/webpack -d --watch
```
- Quick note: './node_modules/.bin/webpack -d' is a command run from the terminal.  We can
simplify it by adding some code to the "scripts" part of the packages.json file
**packages.json**
```json
	// code
	"scripts": {
	    "dev": "webpack -d --watch",
	    "build" : "webpack -p"
	  },
	// code
```
- Now we can run Webpack in production mode (npm run build) or run it in watch mode 
(npm run dev)
```shell
touch ./src/client/app/AwesomeComponent.jsx  	# create your first component file
```
**AwesomeComponent.jsx**
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
- Update the index.jsx file to include it
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

## Breakdown

### HTML Elements
- The html of the page is very simple.  As index.html shows, it's just one div with id="app"
and a script tag that grabs bundle.js.  That's it.
- The #app div has just one other div with no idea but with what appears to be a custom attribute
called 'data-reactroot'.  As the name suggests, it's probably just the div that holds ALL the 
resulting elements...which it is.
- Inside the 'data-reactroot' div we see the code that we've laid out in *index.jsx* which
consists of a &lt;p&gt; tag and my created component, &lt;AwesomeComponent /&gt;
  - The &lt;p&gt; tag is just a &lt;p&gt; tag BUT....
  - The &lt;AwesomeComponent /&gt; has the layout shown in the AwesomeComponent.jsx file:
    - div > 'Likes' span + div > buton
- Whenever you click 'Like me', the 'Likes' number increases by one

### index.js
- 

### AwesomeComponent.jsx
- Here is where we see what I think is ES6...
- I believe the first 'import' line is simply stating that we need the React library
- There are two main parts to the code:
  - the class AwesomeComponent which contains three methods/functions but one is the 
  constructor and one is render, so one custom function
  - the export line which I'm entirely sure what it does
- **render() function** - 1st-view
  - I am returning everything in the parentheses which is the layout described above:
    - div > 'Likes' span + div > buton
  - In the span, I am accessing the 'likesCount' in 'state'
  - In the button, onclick I am calling my other function 'onLike()'
- **onLike() function** - 1st-view
  - it looks like we are defining a new variable called 'newLikesCount' and setting it equal
  to this.state.likesCount + 1
  - we are then changing the this.state.likesCount by doing 'this.setState'
- **constructor(props) function** - 1st-view
  - This looks like an initializer...the middle line makes sense but I don't understand
  what line 1 does ('super(props);') or line 3
  - Line 2 appears to be initializing my likesCount which I increment in the onLike() function
  and render in the render() function



## Questions
1. npm init
2. what is '-S' when installing WebPack
3. how does the webpack.config file work? 
  - what are the basics?
4. what is the data-reactroot attribute?
5. why doesn't AwesomeComponent import react-dom? (just because we're not displaying it?)
6. what does the 'export default' line do?
*7. Explain a little bit about a constructor, super(), and super(props).*
  - source: [React ES6 class constructor super()](http://cheng.logdown.com/posts/2016/03/26/683329)
  - It is ES6 syntax to write '<code>class MyClass extends React.component { ... }<code>' and so you may
  be wondering...**Q1**: is it necessary to call super() inside my constructor function?
  - **A1.** Calling super() is only necessary if you must have a constructor.  In other words, you do
  NOT always need to have a constructor.
    - *so when or why do I need one??  I don't know right now...*
    - calling super() is required when you have a constructor because **this** is uninitialized
    if super() is not called.
  - **Q2**: So what is the difference between super() and super(props)?
  - **A2.** Calling super(props) is only necessary when you want to access this.props inside 
  the constructor.  In this app, I want to initialize the likesCount: 0 which may be why I need
  super(props).
    - so what is the difference between this.state and this.props?  Here are the console.log()
    statements from each that are fired whenever I click 'like':
      - this.state: Object {likesCount: 2}
      - this.props: Object {}
      - They clearly aren't the same thing...so how is state different than props?
  - The big take-away for me is that if I have a constructor, which seems more likely than not
  that I will, my constructor function must requires argument props and must call super(props)













