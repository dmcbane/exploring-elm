# An Exploration of the Elm Programming Language

This is a recounting of my journey to determine if the [Elm programming language](https://elm-lang.org/) would live up to its promise and potentially become my tool of choice for web browser-based front end development. My journey started long before this recounting. I have more than 30 years of experience with software development, some limited experience with functional programming, but I have never used Elm in any capacity.  After fighting the HTML5/CSS/JavaScript battle yet again I began this analysis of alternatives.  I was looking for a better way and Elm looked promising.

## 1. Preparing Your Development Environment

Any exploration into a new language begins with preparing the development environment.

### Install the Compiler and Tools

For [Elm](https://elm-lang.org/), there are installers available for [Windows](https://github.com/elm/compiler/releases/download/0.19.1/installer-for-windows.exe) and [Mac](https://github.com/elm/compiler/releases/download/0.19.1/installer-for-mac.pkg) and a binary for [Linux](https://github.com/elm/compiler/blob/master/installers/linux/README.md), but since [elm-format](https://www.npmjs.com/package/elm-format) and [elm-test](https://www.npmjs.com/package/elm-test) are installed via [Node Package Manager(NPM)](https://www.npmjs.com/), I installed [Elm](https://elm-lang.org/) with NPM also. [If you don't already have Node.js and NPM, you will need install them before installing Elm and some support tools.](https://nodejs.org/en/download/package-manager/) I recommend using [Chocolatey](https://chocolatey.org/) on Windows or your package manager on Linux to install Node.js and NPM.

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
npm install -g @elm-tooling/elm-language-server elm-analyse
```

And finally, add the ability to run unit tests.

```bash
npm install -g elm-test
```

#### Resolving Linux Permissions Errors
For Fedora Linux, if you're not using [Node Version Manager](https://github.com/nvm-sh/nvm), you may need to follow the instructions below [(based on these instructions)](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally) to resolve the permissions issues for installing globally.  You can perform this procedure on any Linux distribution and MacOS, but it does not apply to Windows outside of the Windows Subsystem for Linux (WSL).  During testing, I successfully completed the following procedure on Fedora 32, Manjaro 20.0.3, NixOS 2.3.7 and Ubuntu 20.04 (both stand alone and under WSL).

##### Optionally Remove Global Packages

If you are performing this procedure on a machine where you already have global packages installed and you do not need your global packages to be available to other users or processes, you may want remove the existing packages before relocating your global local repository.  You can remove the global packages automatically from within your shell with the following.

```bash
sudo npm remove -g $(npm list -g --depth 0 | rg [├└]── | awk '{ print $2 }' | sed -E 's/^(@?.*?)@.*/\1/' -)
```
If you need to pick and choose which global packages to remove, you can list the currently installed global packages with the following.

```bash
sudo npm list -g --depth 0
```
And then remove each manually with the following.

```bash
sudo npm remove -g <package name>
```

##### Establishing the Location of your Global Packages

Create and initialize a directory for global installations.

```bash
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
```

Add the following to `~/.bash_profile`, `~/.bashrc`, `~/.profile`, `~/.zshrc`, or `~/.zshenv` as appropriate for your shell of choice.

```bash
export NPM_CONFIG_PREFIX=~/.npm-global
export PATH=$PATH:~/.npm-global/bin
```

Source the file, as appropriate to your distribution, to get the updated environment variables.

```bash
source ~/.bash_profile
# or
source ~/.bashrc
# or
source ~/.profile
# or
source ~/.zshrc
# or
source ~/.zshenv
```
This should resolve the permissions errors so that you can successfully install the packages as shown [above](#Install the Compiler and Tools).

### Selecting an Editor

Your editor is one of the most important components in the tool chain since it is where you spend most of your time.  I looked at each of the editors listed in [An Introduction to Elm](https://guide.elm-lang.org/) to determine how well they support capabilities such as syntax highlighting, formatting, diagnostics, completions, etc.  In general, the editors use **[elm-oracle](https://www.npmjs.com/package/elm-oracle)** to support Elm versions prior to 0.19 and **[@elm-tooling/elm-language-server](https://www.npmjs.com/package/@elm-tooling/elm-language-server)** for versions 0.19 and later.  Some support both.  Elm 0.19 introduced [major changes to the terminal commands](https://github.com/elm/compiler/blob/master/docs/upgrade-instructions/0.19.0.md), a new standard library, a redesigned package system, and [more](https://elm-lang.org/news/small-assets-without-the-headache).  Apparently, these changes were so extensive that many projects didn't transition to 0.19.  Since I am new to Elm, I will only be looking at 0.19 support and the following are just my impressions of how well an editor currently supports development in Elm version 0.19.  

#### [Atom](https://atom.io/) with [elmjutsu](https://atom.io/packages/elmjutsu)/[language-elm](https://atom.io/packages/language-elm)/[atom-ide-ui](https://atom.io/packages/atom-ide-ui)/[autocomplete-plus](https://atom.io/packages/autocomplete-plus)

For [Atom](https://atom.io/),  you can't go wrong with [elmjutsu](https://atom.io/packages/elmjutsu) for Elm 0.19 and later.  If you follow the directions provided by the [elmjutsu](https://atom.io/packages/elmjutsu) package with the [atom-ide-ui](https://atom.io/packages/atom-ide-ui) optional recommendations, you'll have an excellent Elm development environment.
#### [Brackets](http://brackets.io/) with [elm-brackets](https://github.com/lepinay/elm-brackets)

I can't recommend Brackets for Elm development because if supports only [versions of Elm prior to 0.19](https://github.com/elm/compiler/blob/master/docs/upgrade-instructions/0.19.0.md) and there doesn't seem to have been any recent updates on the plugin.  If you're using Brackets and developing for an older version of Elm, it might be an option for you.

#### [Emacs](https://www.gnu.org/software/emacs/) with [elm-mode](https://github.com/jcollard/elm-mode)

[Joseph Collard's excellent elm-mode](https://github.com/jcollard/elm-mode) package supports all Elm versions and provides an excellent Elm development experience.  If you like to tune every detail of your development environment, this is the editor for you.  It can be challenging to configure if you start from out-of-the-box Emacs.  I recommend a Vim/Emacs hybrid configuration like [Doom Emacs](https://github.com/hlissner/doom-emacs) or [Spacemacs](https://www.spacemacs.org/) as a good starting point, but if you're a roll your own configuration fan, you might want to look at [Steve Purcell's Emacs configuration bundle](https://github.com/purcell/emacs.d) for ideas on how to configure Emacs for Elm.  **Definitely one of the better solutions** in this list but also more challenging to setup.

###### Notes

Below are a few notes that might be useful to you in setting up your Elm environment in Doom Emacs.

* In your `~/.doom.d/init.el` (Space f P from Doom Emacs), definitely enable `company`, `lsp` and `elm` and use `(elm +lsp)` to tell `elm-mode` to use the language server protocol.  Also enable `spell`, `javascript`, `web`, and `yaml` for front end development.
* In your `~/.doom.d/config.el`, add `(add-to-list 'company-backends 'elm-company)` to the end of the file.

Also, if you are running Windows and have trouble getting spell checking to work correctly, the following may be helpful: [ispell/hunspell/Windows: a fully-worked example](https://lists.gnu.org/archive/html/help-gnu-emacs/2014-04/msg00030.html).

#### [JetBrains IntelliJ IDEA](https://www.jetbrains.com/idea/) or [JetBrains WebStorm](https://www.jetbrains.com/webstorm/) with the [Elm plugin](https://plugins.jetbrains.com/plugin/10268-elm/)

Easy to setup; just install the plugin.  The JetBrains Elm plugin provides an exceptional development experience for the Elm developer.  If you're already using IntelliJ IDEA or WebStorm, you won't be disappointed with the Elm support.  **Definitely one of the better solutions in this list.**

#### [LightTable](http://lighttable.com/) with [elm-light](https://github.com/rundis/elm-light)

I was not able to use the Linux binary provided on LightTable.com nor build a Linux binary that would run successfully on any recent version of Linux (Fedora 32, Manjaro 20.0.3, NixOS 2.3.7 or Ubuntu 20.04).

I was able to install LightTable and the elm-light plugin on Windows 10, but even after after following the [Elm Light Guide](https://rundis.gitbooks.io/elm-light-guide/content/) to configure the elm-light plugin on Windows 10, I was not able to use it because the elm-light plugin supports only [versions of Elm prior to 0.19](https://github.com/elm/compiler/blob/master/docs/upgrade-instructions/0.19.0.md).

At this time, I cannot recommend LightTable for Elm 0.19 development.

#### [SublimeText](https://www.sublimetext.com/) with [LSP](https://packagecontrol.io/packages/LSP)/[LSP-elm](https://packagecontrol.io/packages/LSP-elm)/[elm-syntax-highlighting](https://packagecontrol.io/packages/Elm%20Syntax%20Highlighting)/[elm-format-on-save](https://packagecontrol.io/packages/Elm%20Format%20on%20Save)

[LSP](https://lsp.readthedocs.io/en/latest/), [LSP-elm](https://lsp.readthedocs.io/en/latest/#elm), [Elm Syntax Highlighting](https://github.com/evancz/elm-syntax-highlighting), and [Elm Format on Save](https://github.com/evancz/elm-format-on-save) combine to form a very effective development environment for Elm.  The resulting environment feels lightweight and responsive and it provides the user with just the right amount of feedback without being distracting.  If you're already using SublimeText, you won't be disappointed with the Elm support.

####  [Vim](https://www.vim.org/) or [NeoVim](https://neovim.io/) with [Conquer of Completion](https://github.com/neoclide/coc.nvim)/[theJian/elm.vim](https://github.com/theJian/elm.vim)

[Vim](https://www.vim.org/)/[NeoVim](https://neovim.io/) provides the development experience using the [elm-language-server](https://github.com/elm-tooling/elm-language-server).  Follow the instructions provided on the [elm-language-server page](https://github.com/elm-tooling/elm-language-server#vim) to configure [Conquer of Completion](https://github.com/neoclide/coc.nvim) and use the [example .vimrc](https://github.com/elm-tooling/elm-vim/tree/master/vim-config-example) as a good starting point for your configuration.  (You can see my configuration [here](https://github.com/dmcbane/vimrc) if you're interested.) 

Like Emacs, setting up Vim/NeoVim for Elm development is more challenging than most of the other editors in this list, but the end result is very responsive and very satisfying to work in.  Then again, I could be biased since I have used Vim for many years.

#### [Visual Studio Code](https://code.visualstudio.com/) with [Elm](https://github.com/elm-tooling/elm-language-client-vscode)

 Beginning users will love [VSCode](https://code.visualstudio.com/) for Elm development.  It is exceptionally easy to setup (simply install the [Elm plugin](https://marketplace.visualstudio.com/items?itemName=Elmtooling.elm-ls-vscode)) and provides all of the features you would expect from a full-featured editor.  **Definitely one of the better solutions in this list** and my recommendation for beginners or for anyone who want's to get started with minimal fuss.

## 1. First Elm Program

One of the biggest factors that led to my giving Elm a try was the alleged quality of the compiler's error messages.  After reading what I expected to be a lot of hype related to the error messages, I decided to put it to the test.

Using [An Introduction to Elm](https://guide.elm-lang.org/) as a guide, I created a folder for my Elm project and then ran the  `elm init` and `elm reactor` commands from within the project folder.

```bash
mkdir elm-one
cd elm-one
elm init
elm reactor
```

Opening the URL recommended by `elm reactor` (http://localhost:8000), I was presented with the contents of my project folder.

### Nothing In - Nothing Out

My first thought on how to test the Elm error messages was to create an empty source file and try to generate a valid source file from the resulting error messages so I used the touch command to create an empty source file.  Opening another tab in my terminal and returning to my project folder, I entered the following.

```bash
touch src/Main.elm
```

> #### Slight Sidetrack
>
> The touch command is included on Linux and MacOS, but not Windows.  Initially, I used `echo "" > src\Main.elm` to create the empty file, but that resulted in a file with UTF-16LE encoding.  Returning to the reactor in the browser (http://localhost:8000) and selecting the src folder, the browser reported "ERR_EMPTY_RESPONSE".  HMM. Definitely not what I expected and since I didn't get the same result under Linux, I suspected the method used to create the empty file.
>
> Looking at the terminal where the `elm reactor` command was running, there were no error messages or any indication that anything might be amiss.  Running `elm make .\src\Main.elm` finally yielded something useful.
>
> ```
> Detected problems in 1 module.
> -- WEIRD DECLARATION ---------------------------------------------- src\Main.elm
> 
> I am trying to parse a declaration, but I am getting stuck here:
> 
> 1| ∩┐╜∩┐╜
>    ^
> When a line has no spaces at the beginning, I expect it to be a declaration like
> one of these:
> 
>     greet : String -> String
>     greet name =
>       "Hello " ++ name ++ "!"
> 
>     type User = Anonymous | LoggedIn String
>   
> Try to make your declaration look like one of those? Or if this is not supposed
> to be a declaration, try adding some spaces before it?
> ```
>
> Line 1 of the supposedly empty file containing characters seemed to confirm that the empty file wasn't empty.  Opening the file in my editor led me to discover that the file was created with UTF-16LE character encoding.  I deleted the file and recreated it using my editor, with UTF-8 encoding this time, and the `elm make .\src\Main.elm` command returned the following.
>
> ```
> Compiling ...+-------------------------------------------------------------------------------
> |  Corrupt File: C:\Users\dalem\src\elm\elm-one\elm-stuff\0.19.1\i.dat
> |   Byte Offset: 69582
> |       Message: not enough bytes
> |
> | Please report this to https://github.com/elm/compiler/issues
> | Trying to continue anyway.
> +-------------------------------------------------------------------------------
> Detected a problem.
> -- CORRUPT CACHE ---------------------------------------------------------------             
> 
> It looks like some of the information cached in elm-stuff/ has been corrupted.               
> 
> Try deleting your elm-stuff/ directory to get unstuck.
> 
> Note: This almost certainly means that a 3rd party tool (or editor plugin) is
> causing problems your the elm-stuff/ directory. Try disabling 3rd party tools
> one by one until you figure out which it is!
> ```
> Following the recommendation, I removed the elm-stuff directory with `rm -Recurse -Force .\elm-stuff\`. I stopped the reactor process with CTRL-C via the terminal and restarted it.  Now my results from the reactor matched those on Linux.  Running the `elm make src\Main.elm` command again returned matching results also.

Returning to the reactor dashboard in my browser (http://localhost:8000), I see the following.

```
-- WEIRD DECLARATION --------------- C:\Users\dalem\src\elm\elm-one\src\Main.elm

I am trying to parse a declaration, but I am getting stuck here:

1| 
   ^
When a line has no spaces at the beginning, I expect it to be a declaration like
one of these:

    greet : String -> String
    greet name =
      "Hello " ++ name ++ "!"

    type User = Anonymous | LoggedIn String

Try to make your declaration look like one of those? Or if this is not supposed
to be a declaration, try adding some spaces before it?
```

Wow.  That is definitely the best error message I've seen from a compiler.  Most would have offered the line number and a cryptic error message and maybe a stack trace indicating how we arrived at the location of the problem.  The elm compiler provided an error name, the line number, a description of what the compiler expected, and recommendations as to how to resolve the problem.  Definitely above and beyond.

The recommendations seem to be guiding me to create a function and a time, so I'm going to play the obtuse card and copy the code directly from the recommendation just to see what happens.

```elm
greet : String -> String
greet name =
    "Hello " ++ name ++ "!"

type User = Anonymous | LoggedIn String
```

The reactor dashboard now displays...

```
-- MODULE NAME MISSING ------------- C:\Users\dalem\src\elm\elm-one\src\Main.elm

I need the module name to be declared at the top of this file, like this:

    module Main exposing (..)

Try adding that as the first line of your file!

Note: It is best to replace (..) with an explicit list of types and functions
you want to expose. When you know a value is only used within this module, you
can refactor without worrying about uses elsewhere. Limiting exposed values can
also speed up compilation because I can skip a bunch of work if I see that the
exposed API has not changed.
```

I'm starting to think that the hype surrounding Elm error messages is not hype at all.  Following the recommendation again, my `src/Main.elm` file now looks like the following:

```elm
module Main exposing (..)

greet : String -> String
greet name =
    "Hello " ++ name ++ "!"

type User = Anonymous | LoggedIn String
```

We had a good run, but the next error message in the reactor dashboard doesn't tell me how to proceed.  Oh no!  I might actually have to think.

```bash
Initialization Error
ReferenceError: _Platform_export is not defined
```

Running `elm make src/Main.elm` yields "Success!" so our file apparently contains syntactically valid Elm code.  Based on past experience and every Elm example that I've seen, I expect the problem to be caused by the lack of a main function in the Main module.  To test this supposition, change `src/Main.elm` as follows.

```elm
module Main exposing (..)

greet : String -> String
greet name =
    "Hello " ++ name ++ "!"

type User = Anonymous | LoggedIn String

main =
	greet "World"
```

 Our supposition was apparently correct, because the reactor dashboard now reports meaningful error messages again.

```
-- BAD MAIN TYPE ------------------- C:\Users\dalem\src\elm\elm-one\src\Main.elm

I cannot handle this type of `main` value:

9| main =
   ^^^^
The type of `main` value I am seeing is:

    String

I only know how to handle Html, Svg, and Programs though. Modify `main` to be
one of those types of values!
```

Unfortunately, there is no suggestion this time, but I know I want to output Html so lets change the type of the main function to return Html instead of a String.

```elm
module Main exposing (..)

greet : String -> String
greet name =
    "Hello " ++ name ++ "!"

type User = Anonymous | LoggedIn String

main: Html
main =
	greet "World"
```

The resulting error...

```
-- NAMING ERROR -------------------- C:\Users\dalem\src\elm\elm-one\src\Main.elm

I cannot find a `Html` type:

9| main: Html
         ^^^^
These names seem close though:

    Bool
    Cmd
    Char
    Int

Hint: Read <https://elm-lang.org/0.19.1/imports> to see how `import`
declarations work in Elm.
```

...seems to indicate that we should import Html, so lets try that.

```elm
module Main exposing (..)

import Html

greet : String -> String
greet name =
    "Hello " ++ name ++ "!"

type User = Anonymous | LoggedIn String

main: Html
main =
	greet "World"
```

Resulting in

```
-- NAMING ERROR -------------------- C:\Users\dalem\src\elm\elm-one\src\Main.elm

I cannot find a `Html` type:

11| main: Html
          ^^^^
These names seem close though:

    Bool
    Cmd
    Char
    Int

Hint: Read <https://elm-lang.org/0.19.1/imports> to see how `import`
declarations work in Elm.
```

Looking more closely at the page offered by the Hint ([https://elm-lang.org/0.19.1/imports](https://elm-lang.org/0.19.1/imports)), the import should have been...

```elm
import Html exposing (Html)
```

...and the function signature should have been ...

```elm
main: Html msg
```

yielding.

```elm
module Main exposing (..)

import Html exposing (Html)

greet : String -> String
greet name =
    "Hello " ++ name ++ "!"

type User = Anonymous | LoggedIn String

main: Html msg
main =
	greet "World"
```

The error changes again; this time is shows

```
-- TYPE MISMATCH ------------------- C:\Users\dalem\src\elm\elm-one\src\Main.elm

Something is off with the body of the `main` definition:

13|     greet "World"
        ^^^^^^^^^^^^^
This `greet` call produces:

    String

But the type annotation on `main` says it should be:

    Html msg
```



# Backup information

## When you encounter examples from previous versions

[elm-upgrade](https://github.com/avh4/elm-upgrade#readme)

```bash
npm install -g elm-upgrade
```

