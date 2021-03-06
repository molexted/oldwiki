The development version is a newer version of Red Eclipse that is still being developed. If you want to help test the game, or experience the newer features, use this guide to obtain the latest development version of the game. Red Eclipse development repositories and issue trackers are hosted on [GitHub](https://github.com/red-eclipse).

Notes:

-   The client-server protocol normally changes between releases, so you might not be able to play on servers that are running the stable version.
-   If you experience a bug or other unwanted behaviour, and want to submit a bug report, see [Debug](Debug "wikilink") for instructions.
-   The Red Eclipse home directory (the one containing screenshots, custom maps, configs, etc.) is now inside the Git directory itself, under the directory called "home".

## Abstract

The development version of Red Eclipse is on the *master* branch of our *base* repository on GitHub, <https://github.com/red-eclipse/base>. If you are familiar with git, you can clone it from this repository. Alternately, you can use the game's updater to fetch the latest development version. For this, simply make a copy of your Red Eclipse installation and add the file "branch.txt" containing the keyword "master". In the following sections, we explain for each operating system how to use the launcher or various git tools to obtain the development version.

## Windows

### Using the updater

-   Make a copy of your Red Eclipse install, for example from *C\\Program Files (x86)\\Red Eclipse* to *Red-Eclipse-git* in *My Games*.
-   In the new directory, create a file "branch.txt", unless it is all already present: right click, New, Text Document.
-   Open the File with your preferred editor or notepad (double-click), enter the keyword "master" and save the file.
-   Run the game launcher, which is the file "redeclipse.bat" - it will automatically download and replace all needed files.
-   Note, if file extensions (like .bat) are not shown on your system: The launcher is the *redeclipse* file with the gears icon.
-   If you still have a shortcut in this directory, you can remove it, as it points to the release version client (stable).
-   Create a new shortcut for the launcher, so you can conveniently start the development version.
-   Once you launched the game, you will find a new "home" folder in the development version directory.
-   You can copy configuration files for game options etc from *My Documents\\My Games\\Red Eclipse* to this new home folder.
-   Whenever you launch the development version, the latest updates will be fetched automatically.

### Using git-bash

-   Download and install Git from <http://msysgit.github.io/>

#### Initial Clone

-   Start git-bash
-   Change to the directory where the clone should be stored

For example

`cd /c/Users/me/Documents`

-   Clone recursively (to also initialize and clone the submodules)

`git clone --recursive `[`https://github.com/red-eclipse/base.git`](https://github.com/red-eclipse/base.git)` redeclipse-git`

This will download Red Eclipse to the "redeclipse-git" directory within the current working directory.

#### Update Existing Clone

-   Start git-bash and change to the "redeclipse-git" clone directory
-   Fetch and merge the new changes in the base repository

`git pull`

-   Fetch and checkout the submodule changes

`git submodule update`

### Using TortoiseGit

-   Install according to <https://code.google.com/p/tortoisegit/wiki/SetupHowTo>

#### Initial Clone

-   Make a "redeclipse-git" directory (folder)
-   Right click on the directory
-   Click on "Git Clone..."
-   In the "Url:" blank, enter <https://github.com/red-eclipse/base.git>
-   Checkmark "Recursive" if you want the data submodules
-   Click "OK" and wait for the clone to complete

#### Update Existing Clone

-   Right click on the clone directory and choose "TortoiseGit"-&gt;"Pull" and then click "OK"
-   Right click on the clone directory and choose "TortoiseGit"-&gt;"Submodule Update" and then click "OK"

### Using Github for Windows

<span style="color:red">WARNING: Using GitHub for Windows requires a solid understanding of Git and UNIX, and can otherwise be confusing.</span>

#### Clone

-   Download and install Github for Windows from <https://windows.github.com/>
-   Follow install directions
-   In your browser, navigate to the RE Github repository page, <https://www.github.com/red-eclipse/base>
-   In the right sidebar, above download zip, you will see a link that says "Clone in Desktop", click on it
-   Select Github
-   Let Github do its thing
-   Done!

#### Updating

-   Go the the repository in Github for Windows
-   Press the Sync button in the top right corner
-   Done!

## GNU/Linux

### Using the updater

-   Create a copy of your Red Eclipse install, which will serve as the development version
-   Use your preferred text editor to create a file "branch.txt" that contains the keyword "master"
-   Run the game launcher: redeclipse.sh - it will upgrade your copied installation to the development version.
-   Once the game is launched, it will save your configuration data in the directory *./home*
-   You can copy all needed configuration files from *~/.redeclipse* to this *home* directory.
-   Updates to the development version will be fetched automatically.

### Using AppImages

-   [Simple instructions for the client](https://redeclipse.net/wiki/How_to_install#Development_version).
-   You can download other AppImages built from the master branch at [<https://redeclipse.net/appimage/master>](https://redeclipse.net/appimage/master).

### Using git

#### Clone via Git

-   Open the terminal
-   Install Git and the build & run dependencies of Red Eclipse. On a Debian-like system (e.g. Ubuntu, Linux Mint), this can be done with the command

`sudo apt-get install git build-essential libsdl2-mixer-dev libsdl2-image-dev libsdl2-dev`

-   Clone the Git repository with the command

`git clone --recursive `[`https://github.com/red-eclipse/base.git`](https://github.com/red-eclipse/base.git)` redeclipse-git`

This will download Red Eclipse to the "redeclipse-git" directory within the current working directory.

#### Compile

-   Use standard GNU make to compile

`cd redeclipse-git/`
`make -C src/ install`

-   Start the game

`./redeclipse.sh`

#### Update via Git

-   Update the local copy

`cd /path/to/base`
`git pull`
`git submodule update`

-   Recompile via

`make -C src/ clean install`

## OS X

### Using the updater

The procedure should be exactly the same as on linux systems.

### Using Xcode

#### Prerequisites

-   Install xcode such that xcodebuild is in your shell path

`xcode-select --install`
`Get `[`http://brew.sh/`](http://brew.sh/)
`brew install git`
`brew install Caskroom/cask/xquartz`

-   Make sure the directory ~/Library/Frameworks exists
-   Download, mount, and copy the .framework from each of the following to (~)/Library/Frameworks/

[`http://www.libsdl.org/release/SDL-1.2.15.dmg`](http://www.libsdl.org/release/SDL-1.2.15.dmg)
[`http://www.libsdl.org/projects/SDL_image/release/SDL_image-1.2.12.dmg`](http://www.libsdl.org/projects/SDL_image/release/SDL_image-1.2.12.dmg)` `
[`http://www.libsdl.org/projects/SDL_mixer/release/SDL_mixer-1.2.12.dmg`](http://www.libsdl.org/projects/SDL_mixer/release/SDL_mixer-1.2.12.dmg)

#### Compile

-   Open the terminal
-   Navigate to the location that you downloaded Red Eclipse

`cd /path/to/redeclipse-git`

-   (Clean previous builds)

`xcodebuild -project src/xcode/redeclipse.xcodeproj clean -configuration Release`

-   Build using xcode

`xcodebuild -project src/xcode/redeclipse.xcodeproj -configuration Release -alltargets`

-   Copy the new build to the bin directory

`cp src/xcode/build/UninstalledProducts/redeclipse.app/Contents/MacOS/redeclipse bin/redeclipse.app/Contents/MacOS/redeclipse_universal`
`chmod +x bin/redeclipse.app/Contents/MacOS/redeclipse_universal`

-   To start Red Eclipse, simply double-click the "Red Eclipse" application in the bin/ directory, or execute

`./bin/redeclipse.app/Contents/MacOS/redeclipse`

## Using Development Version Settings and Regular Settings in Tandem

The home directory is where your custom sounds, music, maps, graphics, scripts, etc., as well as "init.cfg" and "config.cfg" reside. Per default, the development and release version will use seperate user directories, and **no further adjustments are needed** to use both versions in tandem. Hopever, if you modify the ***-h*** argument inside the game launcher script, be sure to use different directories, so your settings will not be mixed. This is important, because some variables or options may get replaced or have a different meaning, and can therefore cause a clash of settings.

The following instructions will setup a directory named "home" inside the Red Eclipse development version directory for your custom data:

`[Red Eclipse development version directory]`
`    [bin]`
`    [data]`
`    [home] <--`
`    [src]`
`    ...`

### Windows

-   In the Red Eclipse development version directory, open the **redeclipse.bat** file in a text editor (like notepad).
-   On Line 5 you will see:

`set APP_OPTIONS=`

-   Simply add "***-h&lt;path/dir name&gt;***" to the end of that line

Where **&lt;path/dir name&gt;** is either the path to a directory outside of the Red Eclipse SVN folder, or just a name, which will create a folder inside that directory, for example

`set APP_OPTIONS=-hhome`

### GNU/Linux and OSX

-   In the Red Eclipse development version directory, open the **redeclipse.sh** file in a text editor (like gedit)

On line 12 you will see

`APP_OPTIONS=""`

-   Simply add ***-h&lt;path/dir name&gt;*** to the end of that line

Where **&lt;path/dir name&gt;** is either the path to a directory outside of the Red Eclipse SVN folder, or just a name, which will create a folder inside that directory, for example

`APP_OPTIONS="-hhome"`

## SVN History

The history of the project prior to the move to Git is available via the (read-only) repository

[`svn://svn.icculus.org/redeclipse`](svn://svn.icculus.org/redeclipse)

and also as a git-svn mirror at

[`git://redeclipse.git.sourceforge.net/gitroot/redeclipse/redeclipse`](git://redeclipse.git.sourceforge.net/gitroot/redeclipse/redeclipse)

for which there is also a webviewer at

<http://redeclipse.git.sourceforge.net/git/gitweb.cgi?p=redeclipse/redeclipse;a=summary>

-   The Git repository size is **more than 3.6G** since it includes the full history since revision 1

