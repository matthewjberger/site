---
title: Getting Started with Rust
header:
  image: "/images/mistymountains.jpg"
categories:
  - Rust
tags:
  - Rust
  - vscode
published: true
---

1.) Install Rust from [rustup.rs](https://rustup.rs)

* If on Windows, you'll need to install the [Visual C++ 2017 Build Tools](https://www.visualstudio.com/downloads/#build-tools-for-visual-studio-2017)

If on windows, you can install rustup via the [scoop package manager](https://scoop.sh/) by running these commands in powershell:

```powershell
Set-ExecutionPolicy RemoteSigned -scope CurrentUser
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
scoop install rustup
```

2.) Install the Language Server (for IDE-like features in editors):

```
rustup self update
rustup update
cargo +nightly install racer
rustup component add rls-preview
rustup component add rust-analysis
rustup component add rust-src
```

3.) Install [clippy](https://github.com/rust-lang/rust-clippy):

```
rustup component add clippy-preview
```

At this point you can edit rust with whichever editor you like. The following steps are for Visual Studio Code.

4.) Install `Visual Studio Code` from:

[https://code.visualstudio.com/](https://code.visualstudio.com/)

Note: If using scoop on windows, run:

```
scoop bucket add extras
scoop install vscode
```

5.) Install the `Rust (rls)` vscode extension from the vscode marketplace, or press `ctrl+shift+p` to open the command pallette and type:

```
ext install rust
```

How to install extensions in vscode:
[https://code.visualstudio.com/docs/editor/extension-gallery](https://code.visualstudio.com/docs/editor/extension-gallery)

6.) Add the following line to your vscode user preferences (`File->Preferences->Settings` or use the shortcut `Ctrl+Comma`):

```
"rust-client.channel": "stable"
```

7.) Create a new rust project with cargo and open it with vscode:

```
cargo new my-project-name
code my-project-name
```

8.) Begin learning Rust!

All the offline documentation for your current Rust release can be found using:

```
rustup doc
```

An up to date repository of various Rust learning resources can be found here:

* [https://github.com/ctjhoa/rust-learning](https://github.com/ctjhoa/rust-learning)

Awesome-Rust:

* [https://github.com/rust-unofficial/awesome-rust](https://github.com/rust-unofficial/awesome-rust)
