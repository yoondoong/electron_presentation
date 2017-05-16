# Electron Workshop

**Clone and run for a quick way to see Electron in action.**

This is a minimal Electron application based on the [Quick Start Guide](http://electron.atom.io/docs/tutorial/quick-start) within the Electron documentation.

**Use this app along with the [Electron API Demos](http://electron.atom.io/#get-started) app for API code examples to help you get started.**

You can learn more about each of these components within the [Quick Start Guide](http://electron.atom.io/docs/tutorial/quick-start).

Let's get down to business.

## To Use

Normally, to get started, we will install electron client tools using `npm install`:

```bash
npm install electron-cli electron -g
```

Find an empty directory where you want to put your electron folder, and

```bash    
electron-cli init your-app-name
```

electron-cli is a command line tool that helps you  set up an Electron dev environment quickly. You will see multiple options given by the init script, say whatever you want, but be sure to check `Y` for the boilerplate option, this will let Electron provide a basic template for you app.

Upon completion you should see a file structure like this

    |-Your Folder
       |---index.html
       |---main.js
       |---package.json
       |---node_modules
       |------blah blah blah

A basic Electron application needs just these files:

- `package.json` - Points to the app's main file and lists its details and dependencies.
- `main.js` - Starts the app and creates a browser window to render HTML. This is the app's **main process**.
- `index.html` - A web page to render. This is the app's **renderer process**.

Now you can run `electron .` to run the app, and it works!! Yay! Now we're going to make it look nicer.

<br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>

Aaaaannnd Psych!
----

We actually have a customized start repository, which is this one! Now fork it and delete the old repo!

So from your command line:

```bash
# Clone this repository
git clone https://github.com/Pspanky/electron_presentation
# Go into the repository
cd electron_presentation
# Install dependencies
npm install
# Run the app
npm start
```

Step 1: Basics
----
First, in `main.js` **comment out** the line that reads

```javascript
win.webContents.openDevTools()
```

because we don't want to see the dev tools when we start the app.
And in the callback function for `app.on('window-all-closed')`, change what's in there to `app.quit()` only, this will terminate the app once all windows are closed, instead of have the icon lingering in the dock, like Atom.

What we're trying to make is a Giphy search app that has a search bar and a display block. To accomplish that, we will first replace the content of `<body>` of `index.html` with

```html
<div id="main-container">
  <textarea id="search" placeholder="Search Keywork"></textarea>
  <div>
    <img id="img-show" src="http://www.collegedegreesearch.net/wp-content/uploads/2014/02/search.png" />
  </div>
</div>
```

Oh and don't forget to include the supplied styling file in `index.html`, took me a while to notice. Add this line to `<head>` in `index.html`

```html
<link rel="stylesheet" type="text/css" href="style.css">
```

And if there are other decorations you'd like to add, charge ahead.

Step 2: Functionalities
----
But if you have noticed, this app doesn't really do much does it? So we'll have to add the business logic in `renderer.js`, you can call it otherwise in your own project. Now add the following lines to your `renderer.js`
```javascript
// This file is required by the index.html file and will
// be executed in the renderer process for that window.
// All of the Node.js APIs are available in this process.

let axios = require('axios');

const apiUrl = (term) => {
    return `http://api.giphy.com/v1/gifs/search?q=${term}&api_key=dc6zaTOxFJmzC`;
};

let searchBar = document.getElementById('search');
searchBar.onkeypress = (e) => {
    if (e.keyCode === 13) {
        e.preventDefault();

        if (searchBar.value === "")
            return;

        // loading
        document.getElementById('img-show')
                .setAttribute('src', 'imgs/loading.gif');

        axios.get(apiUrl(searchBar.value), {})
            .then((res) => {
                document.getElementById('img-show')
                        .setAttribute('src', res.data.data[0].images.original.url);
            }).catch((err) => {
                document.getElementById('img-show')
                        .setAttribute('src', "imgs/error.png");
                console.log(err);
            });
    }
};
```
Again, include it in `index.html`, it's just like a web app! Except this time you would add
```html
<script>
    require('./renderer.js')
<script/>
```
towards the end of `index.html` file, inside `<html>` and after `<body>`. 

Finally
```bash
npm install --save axios
```
And run `electron .` again, gifs now will be displayed when you press enter in the search bar, it's simple as that.

Step 3: Keyboard Shortcut
----
Set up global keyboard shortcut in `main.js`. First we do this by importing ipc and global keyboard shortcut.

```javascript
const globalShortcut = electron.globalShortcut
const ipc = require('electron').ipcMain
```

Now go to `app.on('ready')`. Inside here, we set up a const `ret` to which we register a keyboard shortcut.
This means that every time our designated keyboard shortcut is pressed, it sends a string. 

```javascript
  const ret = globalShortcut.register('CommandOrControl+P', () => {
    mainWindow.webContents.send('showSurprise')
  });

  if (!ret) {
    console.log('registration failed');
  }
```

Now we want to make sure that our `renderer.js` which is actually run in our `index.html` can receive the prompts from our keyboard shortcut. At the top of `renderer.js`, declare the `ipc` variable. 

```javascript
const ipc = require('electron').ipcRenderer
```

We can now write a function that runs every time our `renderer.js` receives the prompt from
the keyboard shortcut. 

```javascript
ipc.on("showSurprise", (event, data) => {
	if (document.getElementById('secretSection').style.display == "none"){
			document.getElementById('secretSection').style.display = "flex";
	} else {
		document.getElementById('secretSection').style.display = "none";
	}
})
```

Got that working? Great! Here are a couple more shortcuts that you can throw in in the same place as the first shortcut in main.js which we you don't need the ipc for as they interact directly with the main Electron window. Play around with them and see what they do!

```javascript
const ret2 = globalShortcut.register('CommandOrControl+Q', () => {
    app.quit()
})
```

```javascript
const ret3 = globalShortcut.register('CommandOrControl+H', () => {
    app.hide()
})
```

```javascript
const ret4 = globalShortcut.register('CommandOrControl+S', () => {
    console.log('CommandOrControl+S is pressed')
    app.show()
})
 ```

```javascript
const ret5 = globalShortcut.register('CommandOrControl+T', () => {
    console.log('CommandOrControl+T is pressed')
    createWindow(true, true)
})
```

```javascript
const ret6 = globalShortcut.register('CommandOrControl+F', () => {
    console.log('CommandOrControl+F is pressed')
    createWindow(false, false)
})
```

Last step: Packaging
----

You've created a wonderful app, and you want to distribute it to other people, how do you do that? Simple

```bash
electron-cli pack
```

Look around, there's a new folder with your app in it!

Done! If you want to distribute for other platforms, you will need to add support for multiple architectures but those things are widely available on the net. e.g. [here](http://electron.rocks/electron-angular-packing-and-distribution/).
