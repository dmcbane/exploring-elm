# An Exploration of the Elm Programming Language

This is a recounting of my journey to determine if the [Elm programming language](https://elm-lang.org/) would live up to it promise and potentially become my tool of choice for web browser-based front end development. My journey started long before this recounting. Suffice it to say, that while I have more than 30 years of experience with software development and some limited experience with functional programming, I have never used Elm in any capacity.  Call it an analysis of alternatives if you will.  At least that is how it started.

I started keeping this journal after playing around with [An Introduction to Elm](https://guide.elm-lang.org/) a bit.  Having recently fought the HTM5/CSS/JavaScript yet battle again, I was looking for a better way and Elm looked promising.

## Preparing Your Development Environment

Any exploration into a new language begins with preparing the development environment.

### Install the Compiler and Tools

For [Elm](https://elm-lang.org/), there are installers available for [Windows](https://github.com/elm/compiler/releases/download/0.19.1/installer-for-windows.exe) and [Mac](https://github.com/elm/compiler/releases/download/0.19.1/installer-for-mac.pkg) and a binary for [Linux](https://github.com/elm/compiler/blob/master/installers/linux/README.md), but since [elm-format](https://www.npmjs.com/package/elm-format) and [elm-test](https://www.npmjs.com/package/elm-test) are installed via [Node Package Manager](https://www.npmjs.com/), I installed [Elm](https://elm-lang.org/) with [npm](https://www.npmjs.com/) also. ([If you don't already have Node.js and NPM, you will need install them before running the commands below.](https://nodejs.org/en/download/package-manager/) I recommend using [Chocolatey](https://chocolatey.org/) on Windows or your package manager on Linux to install Node.js and NPM.)

First, install the Elm platform.

```bash
npm install -g elm
```

Add support for automatically formatting your code.

```bash
npm install -g elm-format
```

Add code completion and documentation lookup.

##### Elm versions prior to 0.19

```bash
npm install -g elm-oracle
```

##### Elm versions 0.19 and greater

```bash
npm install -g @elm-tooling/elm-language-server
```

And finally, add the ability to run unit tests.

```bash
npm install -g elm-test
```

#### Resolving Permissions Errors
For Fedora Linux, you may need to follow the instructions below [(based on the instructions here)](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally) to resolve the permissions issues for installing globally.  You can perform this procedure on any Linux distribution and MacOS, but it does not apply to Windows outside of WSL.  (During testing, I successfully completed the following procedure on Fedora 32, Manjaro 20.0.3, NixOS 2.3.7 and Ubuntu 20.04).  

To start, find the previously installed global packages.

```bash
sudo npm list -g --depth 0
```
Automate removal with shell...

```bash
npm remove -g $(npm list -g --depth 0 | rg [├└]── | awk '{ print $2 }' | sed -E 's/^(@?.*?)@.*/\1/' -)
```
Or remove each manually...

```bash
sudo npm remove -g elm-oracle
sudo npm remove -g elm-test
sudo npm remove -g elm
```

 Create and initialize a directory for global installations.

```bash
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
```

Add the following to ~/.bash_profile, ~/.bashrc, ~/.profile, ~/.zshrc, or ~/.zshenv as appropriate for your distribution.

```bash
export NPM_CONFIG_PREFIX=~/.npm-global
export PATH=$PATH:~/.npm-global/bin
```

Sourced the file to get the updated environment variables.

```bash
source ~/.bash_profile
```
```bash
source ~/.bashrc
```
```bash
source ~/.profile
```
```bash
source ~/.zshrc
```
```bash
source ~/.zshenv
```


This should resolve the permissions errors for the npm commands above.

### Selecting an Editor

Your editor is one of the most important components in the tool chain since it is where you spend most of your time.  I looked at each of the editors listed in [An Introduction to Elm](https://guide.elm-lang.org/) to determine how well they support capabilities such as syntax highlighting, formatting, diagnostics, completions, etc.  In general, the editors use **[elm-oracle](https://www.npmjs.com/package/elm-oracle)** to support Elm versions prior to 0.19 and **[@elm-tooling/elm-language-server](https://www.npmjs.com/package/@elm-tooling/elm-language-server)** for versions 0.19 and later.  Some support both.  Elm 0.19 introduced [major changes to the terminal commands](https://github.com/elm/compiler/blob/master/docs/upgrade-instructions/0.19.0.md), a new standard library, a redesigned package system, and [more](https://elm-lang.org/news/small-assets-without-the-headache).  Apparently, these changes were so extensive that many projects didn't transition to 0.19.  Since I am new to Elm, I will only be looking at 0.19 support and the following are just my impressions of how well an editor currently supports development in Elm version 0.19.  

#### [Atom](https://atom.io/) with [elmjutsu](https://atom.io/packages/elmjutsu)/[language-elm](https://atom.io/packages/language-elm)/[atom-ide-ui](https://atom.io/packages/atom-ide-ui)/[autocomplete-plus](https://atom.io/packages/autocomplete-plus)

For [Atom](https://atom.io/),  you can't go wrong with [elmjutsu](https://atom.io/packages/elmjutsu) for Elm 0.19 and later.  If you follow the directions provided by the [elmjutsu](https://atom.io/packages/elmjutsu) package with the [atom-ide-ui](https://atom.io/packages/atom-ide-ui) optional recommendations, you'll have an excellent Elm development environment.
#### [Brackets](http://brackets.io/) with [elm-brackets](https://github.com/lepinay/elm-brackets)

I can't recommend Brackets for Elm development because if supports only [versions of Elm prior to 0.19](https://github.com/elm/compiler/blob/master/docs/upgrade-instructions/0.19.0.md).  If you're using Brackets and developing for an older version of Elm, it might be an option for you.

#### [Emacs](https://www.gnu.org/software/emacs/) with [elm-mode](https://github.com/jcollard/elm-mode)

[Joseph Collard's excellent elm-mode](https://github.com/jcollard/elm-mode) package supports all Elm versions and provides a top flight Elm development experience.  I am a long time Vim user and I love the Vim/Emacs hybrid configurations like [Doom Emacs](https://github.com/hlissner/doom-emacs) or [Spacemacs](https://www.spacemacs.org/) because I get the best of both worlds.  If you're a roll your own configuration fan, you might want to look at [Steve Purcell's Emacs configuration bundle](https://github.com/purcell/emacs.d) for ideas on how to configure Emacs for Elm.

##### Side note

Since I use Doom Emacs for development in Elm, I wanted to share a few notes.

* In your `~/.doom.d/init.el` (Space f P from Doom Emacs), definitely enable `company`, `lsp` and `elm` and use `(elm +lsp)` to tell `elm-mode` to use the language server protocol.  I also enable `spell`, `javascript`, `web`, and `yaml` since we're doing front end development.
* In your `~/.doom.d/config.el`, add `(add-to-list 'company-backends 'elm-company)` to the end of the file.

Also, if you are running Windows and have trouble getting spell checking to work correctly, I found the following to be helpful: [ispell/hunspell/Windows: a fully-worked example](https://lists.gnu.org/archive/html/help-gnu-emacs/2014-04/msg00030.html).

#### [JetBrains IntelliJ IDEA](https://www.jetbrains.com/idea/) or [JetBrains WebStorm](https://www.jetbrains.com/webstorm/) with the [Elm plugin](https://plugins.jetbrains.com/plugin/10268-elm/)

As you would expect from a JetBrains product, the JetBrains Elm plugin provides an exceptional development experience for the Elm developer.  If you're already using IntelliJ IDEA or WebStorm, you won't be disappointed with the Elm support.

#### [LightTable](http://lighttable.com/) with [elm-light](https://github.com/rundis/elm-light)

I was not able to use the Linux binary provided on LightTable.com nor build a Linux binary that would run successfully on any recent version of Linux (Fedora 32, Manjaro 20.0.3, NixOS 2.3.7 or Ubuntu 20.04).

I was able to install LightTable and the elm-light plugin on Windows 10, but even after after following the [Elm Light Guide](https://rundis.gitbooks.io/elm-light-guide/content/) to configure the elm-light plugin on Windows 10, I was not able to use it because the elm-light plugin supports only [versions of Elm prior to 0.19](https://github.com/elm/compiler/blob/master/docs/upgrade-instructions/0.19.0.md).

At this time, I cannot recommend LightTable for Elm 0.19 development.

#### [SublimeText](https://www.sublimetext.com/) with [elm-syntax-highlighting](https://github.com/evancz/elm-syntax-highlighting/)

LSP, LSP-elm, Elm Syntax Highlighting, Elm Format on Save

* LSP settings
  * // Settings in here override those in "LSP/LSP.sublime-settings"
    {
    "elm": {
      "command": ["elm-language-server", "--stdio"],
      "enabled": true,
      "initializationOptions": {
        "elmAnalyseTrigger": "change"
      },
      "languageId": "elm",
      "scopes": ["source.elm"],
      "syntaxes": ["Packages/Elm Syntax Highlighting/src/elm.sublime-syntax"]
    }	
    }
* LSP-elm settings
  * {
    	"elmLS.elmPath": "C:\\Users\\dalem\\AppData\\Roaming\\npm\\elm.cmd",
      	"elmLS.elmFormatPath": "C:\\Users\\dalem\\AppData\\Roaming\\npm\\elm-format.cmd",
      	"elmLS.elmTestPath": "C:\\Users\\dalem\\AppData\\Roaming\\npm\\elm-test.cmd"	
    }
* 

####  [Vim](https://www.vim.org/) or [NeoVim](https://neovim.io/) with [elm-vim](https://github.com/ElmCast/elm-vim)



#### [Visual Studio Code](https://code.visualstudio.com/) with [Elm](https://github.com/elm-tooling/elm-language-client-vscode)

 Although 

## First Elm Program

One of the biggest factors that led to my giving Elm a try was the quality of the compiler's error messages.  After reading what I assumed to be a lot of hype related to error messages.  I decided to put it to the test.

* elm init
* empty src/Main.elm 
* compile and show results
* use elm-format and show results
* compile and show results
* walk through getting some output

## When you encounter examples from previous versions

[elm-upgrade](https://github.com/avh4/elm-upgrade#readme)

```bash
npm install -g elm-upgrade
```

