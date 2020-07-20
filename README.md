# Electron, React, Typescript Kickoff

This kickoff guide should enable you to start implementing an application that runs on Mac, Windows and Linux the same way.

I am new to Javascript, Typescript, React, webpack and all this stuff. Years ago I was used to implement things in Java. Meanwhile I transformed to a project manager and scrum master.

But now I want to implement something in an exciting new technology.

Here is my starting point after endless evenings of searching, trying and failing.

It is not totally perfect, but it works! (unlike some other guides...)

(... on my machine, hehe)

## My Requirements

I wanted to build an application using

- [Electron](https://electronjs.org)
- [React](https://reactjs.org/)
- [Typescript](https://www.typescriptlang.org)

(does it matter why? ... aaeerm... no! :-) )

## Thanks To

After a lot of different aproaches and fails I found this guide:

- https://www.codementor.io/randyfindley/how-to-build-an-electron-app-using-create-react-app-and-electron-builder-ss1k0sfer

This helped me a lot. The main part of this kickoff is from that guide.
Thanks Randy for this guide!

Read it and you will understand why the things are made this way.
I won't give an explanation.

## Prerequisites

You should have installed

- npm
  - Use the installer given at https://nodejs.org/en/download/
- yarn
  - Use an installation method given at https://classic.yarnpkg.com/en/docs/install
  - On my mac I've done a `curl -o- -L https://yarnpkg.com/install.sh | bash`

## Steps

### Create React/Typescript project

```
$ yarn create react-app name-of-my-project --template typescript
$ cd name-of-my-project
```

### Add needed modules

Add electron

```
$ yarn add electron electron-builder --dev
```

Add some dev tools we'll need.

```
$ yarn add wait-on concurrently cross-env --dev
$ yarn add electron-is-dev
```

Install Rescripts:

```
$ yarn add @rescripts/cli @rescripts/rescript-env --dev
```

Add Electron Builder:

```
$ yarn add electron-builder --dev
```

### Create new files

Create new file called `public/electron.js` with the following content:

```
const electron = require('electron');
const app = electron.app;
const BrowserWindow = electron.BrowserWindow;

const path = require('path');
const isDev = require('electron-is-dev');

let mainWindow;

function createWindow() {
  mainWindow = new BrowserWindow({width: 900, height: 680});
  mainWindow.loadURL(isDev ? 'http://localhost:3000' : `file://${path.join(__dirname, '../build/index.html')}`);
  if (isDev) {
    // Open the DevTools.
    //BrowserWindow.addDevToolsExtension('<location to your react chrome extension>');
    mainWindow.webContents.openDevTools();
  }
  mainWindow.on('closed', () => mainWindow = null);
}

app.on('ready', createWindow);

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit();
  }
});

app.on('activate', () => {
  if (mainWindow === null) {
    createWindow();
  }
});
```

Create new file called `.rescriptsrc.js` with the following content:

```
module.exports = [require.resolve('./.webpack.config.js')]
```

Create new file called `.webpack.config.js` with the following content:

```
// define child rescript
module.exports = config => {
  config.target = 'electron-renderer';
  return config;
}
```

### Adjust the file `package.json`

Add the following tags.

```
"main": "public/electron.js",
"homepage": "./",
"author": {
  "name": "Your Name",
  "email": "your.email@domain.com",
  "url": "https://your-website.com"
},
"build": {
  "appId": "com.my-website.my-app",
  "productName": "MyApp",
  "copyright": "Copyright © 2019 ${author}",
  "mac": {
    "category": "public.app-category.utilities"
  },
  "files": [
    "build/**/*",
    "node_modules/**/*"
  ],
  "directories": {
    "buildResources": "assets"
  }
},
"rescripts": [
  "env"
],
```

Change the `"scripts"` tags from this...

```
"start": "react-scripts start",
"build": "react-scripts build",
"test": "react-scripts test",
```

to this:

```
"start": "rescripts start",
"build": "rescripts build",
"test": "rescripts test",
```

Add the following to the `"scripts"` tag:

```
"electron-dev": "concurrently \"cross-env BROWSER=none yarn start\" \"wait-on http://localhost:3000 && electron .\"",
"postinstall": "electron-builder install-app-deps",
"preelectron-pack": "yarn build",
"electron-pack": "electron-builder build -mw",
```

Check the `package.json` in this repository. Yours should be nearly the same.

### Module versions

I learned that there is a lot of change going on in module versions.

If you encounter problems after going through this kickoff, then check the installed versions of the dependencies.

I commited a `package.json` with fixed version that worked on the time of the upload.

What happens in future, ... only god knows!

## Usage

Start electron in development mode (hot reload on every saved file included):

```
$ yarn electron-dev
```

Build apps for Windows and Mac:

```
$ yarn electron-pack
```
