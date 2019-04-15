---
title: Getting Started with Electron on Windows
header:
  image: "/images/mountains.jpg"
categories:
  - Electron
tags:
  - Electron
  - vscode
published: true
---

# Installing nodejs and electron

Install the [scoop](http://scoop.sh/) package manager for windows.

Scoop is a command line package manager for windows that will let you easily download the tools we need. Open a powershell terminal and run:

```powershell
set-executionpolicy remotesigned -s cu

iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
```

Then download [nodejs](https://nodejs.org/en/) using scoop. nodejs comes with the node package manager (npm) for managing libraries.

```bash
scoop install nodejs
```

Now install [electron](https://electron.atom.io/). Electron lets you build cross-platform desktop apps using web technologies.

```bash
npm install -g electron
```

This will install the `electron` command globally in your path.

# Running the electron example application

To see an example electron application:

```bash
# Install git for windows
scoop install git

# Clone the Quick Start repository
git clone https://github.com/electron/electron-quick-start

# Go into the repository
cd electron-quick-start

# Install the dependencies and run
npm install && npm start
```

[Click here for more information about the electron quick-start project.](https://electron.atom.io/docs/tutorial/quick-start/)

# Installing Visual Studio Code

I recommend using [Visual Studio Code](https://code.visualstudio.com/) to develop your electron application. You can download this using scoop.

```bash
scoop install vscode
```

This will install the `code` command globally in your path. There should also be an icon for vscode on the start menu.

# Debugging with Visual Studio Code

Open the `electron-quick-start` project in vscode. Click on the second lowest icon on the sidebar on the left side of vscode to open the debug panel (or press `ctrl-shift-D`). At the top will be a small gear with an orange circle in its upper right corner. Click this and type `node.js` where it says `select environment`. Click `Node.js`. It will open a file called `launch.json` with some existing values. Replace the code in this file with the following json:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch Electron",
            "type": "node",
            "request": "launch",
            "program": "${workspaceRoot}\\main.js",
            "stopOnEntry": false,
            "args": [
                "."
            ],
            "cwd": "${workspaceRoot}",
            "runtimeExecutable": "${workspaceRoot}\\node_modules\\.bin\\electron.cmd",
            "env": {},
            "sourceMaps": false
        }
    ]
}
```

There should be a new target in the dropdown next to the gear in the debug panel called `Launch Electron`.

Pressing `F5` will now launch and debug your application and hit breakpoints you set. Try setting a breakpoint in `main.js` and running with `F5`.

There is also built-in intellisense, code completion, syntax highlighting and more available by default.

# Additional Resources

It is recommended that you install the [latest release of the electron api demos](https://github.com/electron/electron-api-demos/releases) and follow along with them using the sample quick-start application.

For more sample applications, checkout the [electron-sample-apps](https://github.com/hokein/electron-sample-apps) repository on github.

For concise descriptions of various electron-related terms, see [essential electron](http://jlord.us/essential-electron/).

Finally, there is a [podcast from Scott Hanselman with Github Electron developer Jessica Lord](https://hanselminutes.com/534/creating-cross-platform-electron-apps-with-jessica-lord) that is worth listening to.

Good luck!
