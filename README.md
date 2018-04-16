# How to Write a Mutation for Aurora
This is a simple tutorial for writing a mutation for the Aurora application. These are the steps you'll need to follow:
1. setting up npm
2. writing your mutation
3. setting up Webpack
4. publishing your mutation to npm

Mutations allow our users to have ultimate flexibility with their note taking styles. A mutation can change most aspects of Aurora. For instance, if you like taking notes in a cornell style format, you can write a mutation that will allow you to do that. Don't like the way the previews look? You can write a mutation to change that.

## Sample Mutation Project
If you don't want to go through the details of this tutorial, you can also just fork [this project](https://github.com/rainmaker022/Aurora-Mutation-Framework) and change a few strings and get on your way.

## npm
First you’ll need to install node if you don’t already have it. That can be done with this link: [node download](https://nodejs.org/en/download/)


Node comes with a version of npm, but you will need to check if it is the latest version. That can be done with entering this command in your terminal

`npm install npm@latest -g`

To test if it updated enter the command

`npm -v `

The version should be higher than 2.1.8

Once you have npm downloaded and updated you can then you can start creating your mutation.

Navigate in your file system to where you would like your npm package to be stored. Then create a folder called `aurora-mutate-nameOfMutation`. It is important to have the prefix `aurora-mutate` because that is how it will end up in our mutation store.

Enter the following command into your command line

`npm init`

![npm init](https://i.imgur.com/rEk9owK.png)

This command creates a package.json file and it will prompt you to enter in values for the fields of the `package.json`. Make sure to input `build/index.js` for `main`.

![package json](https://i.imgur.com/4kMpiWm.png)

If you need more help with npm check out their tutorial: [npm tutorial](https://docs.npmjs.com/getting-started/what-is-npm)
## Mutation
After you create the package.json file, then you can create your mutation. Create another file in the same folder that your `package.json` is in. It needs to be titled the same as what you put in the `entry point` field when creating your `package.json`. In this running example that file is `index.js`.

![mutation setup](https://i.imgur.com/Sy2hIXm.png)

Your mutation will need to be written in React, which is a framework for javascript. If you do not already have an editor that works well with React, you can get [Atom](https://atom.io/) or [VScode](https://code.visualstudio.com/).

Here is an example on how to mutate the editor of Aurora to turn it purple
```import React from "react";
import styled from "styled-components";

const purple = `
  color: #8B008D;
`;

function purpleEditor(BaseEditor) {
  return class extends React.Component {
    render() {
      return (
        <BaseEditor style={purple}>{this.props.children}</BaseEditor>;
      );
    }
  };
}

module.exports.mutations = {
  Editor: purpleEditor
};
```
Here is an example on how to mutate the frame of Aurora to replace it with a flying cat gif
```import React from "react";
import styled from "styled-components";

const EVERYWHERE = styled.img`
  position: fixed;
  top: 0;
  left: 0;
  min-width: 100%;
  min-height: 100%;
`;

function AddKitty(Frame) {
  return class extends React.Component {
    render() {
      return (
        <Frame>
          <EVERYWHERE src="https://media.giphy.com/media/VxbvpfaTTo3le/giphy.gif" />
          {this.props.children}
        </Frame>
      );
    }
  };
}

module.exports.mutations = {
  Frame: AddKitty
};
```
Here is the github repository for this example: [flying cat](https://github.com/Flaque/aurora-mutate-kitty-cat)

There are other examples in this repository that you can look at.

The import statements used are to import react and styled components. React is the language used to write the mutation and styled components allow us to change how the components(Editor and Frame) look. The constants (`const`) are used to style the components using css. If you want a more in depth guide for writing React check this out:

https://reactjs.org/docs/hello-world.html

For more information about the React technique used to write mutations check this out:

https://reactjs.org/docs/higher-order-components.html

### Example Mutations
- [aurora-mutate-rich-styling](https://github.com/rainmaker022/Aurora-Mutation-Tutorial/tree/master/aurora-mutate-rich-styling): simple mutation that adds rich styling (bold, italics, underline) to the editor.
- [aurora-mutate-image-support](https://github.com/rainmaker022/Aurora-Mutation-Tutorial/tree/master/aurora-mutate-image-support): relatively simple mutation that gives user ability to insert image via URL.
- [aurora-mutate-two-column-editor](https://github.com/rainmaker022/Aurora-Mutation-Tutorial/tree/master/aurora-mutate-two-column-editor): pretty simple mutation that gives users two fixed columns in their notes.
- [aurora-mutate-many-column-editor](https://github.com/kmccrohan/aurora-mutate-many-column-editor):
a more complex mutation that allows user to add/remove columns to their notes.


## Webpack
Webpack is a bundler, and we’re going to use it to bundle your mutation once you have written it. You can install Webpack by entering this onto your command line

`npm install webpack -g`

Next you’ll have to create a `webpack.config.js` file. This file will tell webpack what it should do. Here is an example of what it should look like.
```
const path = require("path");

module.exports = {
  entry: "./index.js", //tells webpack where to start
  output: {
    filename: "./build/index.js", //tells webpack the output
    libraryTarget: "commonjs2"
  },
  module: {
    loaders: [
      { test: /\.js$/, loader: "babel-loader", exclude: /node_modules/ },
      { test: /\.jsx$/, loader: "babel-loader", exclude: /node_modules/ },
      { test: /\.css$/, loader: "style-loader!css-loader" }
    ]
  }
};
```
Next, you’ll have to install some babel dependencies by running the following command on your command line:

`npm install --save-dev babel-core babel-preset-env babel-preset-react  babel-loader`

Then, you’ll have to bundle in react and styled components by running the following command:

`npm install --save react styled-components`

Your `package.json` file should now look something like this
```
{
  "name": "aurora-mutate-purple-editor",
  "version": "1.0.0",
  "description": "Turns the aurora editor purple",
  "main": "build/index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack",
    "prepublish": "webpack"
  },
  "keywords": [
    "aurora",
    "mutation"
  ],
  "author": "rainmaker",
  "license": "ISC",
  "dependencies": {
    "react": "^16.1.0",
    "styled-components": "^2.2.3",
    "webpack": "^3.8.1"
  },
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-preset-env": "^1.6.1",
    "babel-preset-react": "^6.24.1"
  }
}
```

### A note on editor mutations
If you are writing a mutation that adds a new type of editor to the `window.editors` registry,
then make sure that the name of the editor you are adding is listed in the `keywords` array of
the `package.json`.

### babelrc
After you write your config file and run the previous npm commands, you'll have to create a `.babelrc` file in your main mutation folder. The contents of that file should be exactly as follows.
```
{
  "presets": ["env", "react", "stage-0"]
}
```

Then run the command `webpack` on your command line. This will create a build folder and a bundle.js file.

![webpack](https://i.imgur.com/zszzTEC.png)

## Publishing your npm package
To publish a npm package you have to register yourself as a user. If you have already created your account on the npm website use the command `npm login` to login to your account. If you have not created an account yet use the command `npm adduser` to create your npm account. The next step is to publish your npm package by using the command `npm publish`. This command will publish everything in the current directory, so make sure you are in the right spot before running this command.
```
npm adduser //creates a new account for npm
npm login   //logs onto npm
npm publish //builds and publishes your npm package (if prepublish script is configured to "webpack")
```

![npm publish](https://i.imgur.com/m4lkqRA.png)
### Updating your npm package
After publishing your package you can also update it by using the `npm version <update-type>` command, where `<update-type>` is either `patch`, `major`, or `minor`. Then run `npm publish` to publish your changes.
```
npm version <update-type>   //updates version of your npm package
npm publish                 //builds publishes your updated package
```

## Check out the Aurora Mutations Store!
Once you publish your npm package your mutation will be available for all of the Aurora users to add to their version of the application!

## Mutation Development
Currently, we admit that there is no easy way to develop mutations. The simplest workflow is to make changes to your mutation, change the version and publish it, then reinstall the mutation in your Aurora application. However, we have found some faster ways.

One problem with installing your newest version through the Aurora GUI is that it often doesn't install the latest version that you just published because of delays in npm's system. So the faster and more reliable way of updating the version of your mutation Aurora is using is:
```
cd ~/.aurora/<env>/mutations/  # where <env> is development or production
vi package.json                # vi or whichever code editor you like
# update the version of your mutation to the version you just published
npm install                    # installs the updated mutation
# Reload Aurora (command+R)
```

Additionally, if you just want to test something small (like add a `console.log` to see if a bit of code is even getting called), you can directly edit the mutation source code.
```
vi ~/.aurora/<env>/mutations/node_modules/<my_mutation>/build/index.js
# Search for the section of code you want to change, make the change
# Reload Aurora
```
Changing this source file can be difficult because it is compiled code. Additionally, these changes do not persist so this is purely for testing little things out. If you want to make any permanent change to your mutation, you have to make the changes in your npm package and then publish.

## Unpublishing your mutation from the mutation store
If you would like to remove your mutation from the mutation store, we allow a way to do that.
Simply add the keyword `unpublish` to your `package.json` and publish the update version:
```
keywords: ["unpublish"]
```
This is nice if you started work on a mutation but never finished it. There is no way to remove your `npm` package from the npm repo, but at least this will prevent your mutation from appearing in our Mutation Store.
