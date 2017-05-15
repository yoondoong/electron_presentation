# electron-quick-start

**Clone and run for a quick way to see Electron in action.**

This is a minimal Electron application based on the [Quick Start Guide](http://electron.atom.io/docs/tutorial/quick-start) within the Electron documentation.

**Use this app along with the [Electron API Demos](http://electron.atom.io/#get-started) app for API code examples to help you get started.**

A basic Electron application needs just these files:

- `package.json` - Points to the app's main file and lists its details and dependencies.
- `main.js` - Starts the app and creates a browser window to render HTML. This is the app's **main process**.
- `index.html` - A web page to render. This is the app's **renderer process**.

You can learn more about each of these components within the [Quick Start Guide](http://electron.atom.io/docs/tutorial/quick-start).

## To Use

To clone and run this repository you'll need [Git](https://git-scm.com) and [Node.js](https://nodejs.org/en/download/) (which comes with [npm](http://npmjs.com)) installed on your computer. From your command line:

```bash
# Clone this repository
git clone https://github.com/electron/electron-quick-start
# Go into the repository
cd electron-quick-start
# Install dependencies
npm install
# Run the app
npm start
```
STEPS FOR THE WORKSHOP

##Step 1:
Import our renderer.js into our index.html. This way, the search bar will display gifs when things are typed into it.

CODE
```bash
require('./renderer.js')
```

##Step 2:
Set up global keyboard shortcut in main.js. First we do this by importing ipc and global keyboard shortcut.

CODE
```bash
const globalShortcut = electron.globalShortcut
const ipc = require('electron').ipcMain
```

##Step 3: 
Now go to app.on 'ready'. Inside here, we set up a const 'ret' to which we register a keyboard shortcut.
This means that every time our designated keyboard shortcut is pressed, it sends a string. 

CODE
```bash
  const ret = globalShortcut.register('CommandOrControl+P', () => {
    mainWindow.webContents.send('showSurprise')
  });

  if (!ret) {
    console.log('registration failed');
  }
```

##Step 4:
Now we want to make sure that our renderer js which is actually run in our index.html can receive 
the prompts from our keyboard shortcut. At the top of renderer.js, establish the ipc variable. 

CODE
```bash
const ipc = require('electron').ipcRenderer
```

##Step 5:
Finally, we can now write a function that runs every time our renderer.js receives the prompt from
the keyboard shortcut. 

CODE
```bash
ipc.on("showSurprise", (event, data) => {
	if (document.getElementById('secretSection').style.display == "none"){
			document.getElementById('secretSection').style.display = "flex";
	} else {
		document.getElementById('secretSection').style.display = "none";
	}
})
```
