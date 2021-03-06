*Red Eclipse* is on github, <http://github.com/red-eclipse/>. This means the development of the game is open and community-driven, and the github site is not only a host for the repositories, but also a place to ease collaboration. Anyone can make their *fork* (working copy) of the game, modify a branch of the fork, and suggest their changes to be merged back with the official game. This process is what they call a **pull request** on github.

## Github help

Github offers plenty of help text pages. We're not going to cover much of this here. Rather, we want to give our players a first glance how to quickly submit a small change via a pull request. For further reading, see <https://help.github.com/categories/collaborating-on-projects-using-issues-and-pull-requests/>.

## Git documentation

Git is the versioning system used on github, and therefore also currently used by Red Eclipse to keep track of all the changes and development history. When submitting a small modification, there is usually no need to deal with git commands. But there's extensive documentation available online. In case you'll need this, see <https://git-scm.com/documentation>.

## Single text files

Let us assume you want to contribute a text description for one of the many in-game commands or variables. Or maybe you have spotted a typo in one of those descriptions. If you want to do that with a pull request on github, first navigate to the corresponding file. In this case, it is here:

` `[`https://github.com/red-eclipse/base/blob/master/config/usage.cfg`](https://github.com/red-eclipse/base/blob/master/config/usage.cfg)

At the top right of the code box, you'll see some buttons. Click the pen-icon to edit the file. This will automatically create a new branch on your own fork for this. If you have not forked yet, that will be done automatically after your confirmation. Apply your additions or corrections using the online file editor. Once you are done, you can review your changes with the button at the bottom. You'll be asked to create a pull request and describe your changes. Make sure the summary (commit text) is short enough and makes sense also for someone who does not see which file was changed. For example, "fix typo on usage of guitext". That's it. Your pull request will be listed here:

` `[`https://github.com/red-eclipse/base/pulls`](https://github.com/red-eclipse/base/pulls)

Once it was accepted and merged you can delete your branch. You'll be informed when it's save to do so. It's up to you what to do with your branch if your request has been rejected...

## Multiple files

Once you have branches on your fork, you can edit the text files there at will. And of course, you can also create a pull request after modifying several files. When you do so, you can and should review all changes and differences. Once the pull request is open, further changes (commits) to the same branch are possible, and will then be part of the pull request, unless it has already been merged.

## Non-text files

On github, it's easy to edit text files, such as source code or config files. But things get much more complicated when you want to submit non-text content like maps, models or icons. Maybe the easiest way to share these things is to put them on the forums, <http://redeclipse.net/forum/viewforum.php?f=5>. There the contribution can be discussed, and a developer can push the new content for you later. And please note that new content should always be first discussed on the forums anyway.

To make a pull request for non-text files, you have two options: Use git console commands to commit and push your modifications. Or use some git client to do the same thing. Either way, you'll need to clone the corresponding repository first, as described in the article about obtaining the [development version](obtain_development_version "wikilink"). There you also find some clues about git and git clients.

What makes things a bit tricky: You'll need to push your changes to a branch of your own fork, and then use that branch to create the pull request. For example,let us assume you want to make a pull request for a new map. For this, you will first need to fork the maps repository, which is used as a submodule in the base repo:

` `[`https://github.com/red-eclipse/maps/`](https://github.com/red-eclipse/maps/)

Press the fork button on the top right. E.g. if your github account name is *guy*, you will get your fork on

` `[`https://github.com/guy/maps/`](https://github.com/guy/maps/)

Now you can use the following git commands:

1) Clone from your own fork to a local directory, e.g. *./re-maps*

` git clone `[`https://github.com/guy/maps`](https://github.com/guy/maps)` re-maps`

2) apply your changes, i.e. copy your map files over to *re-maps*

3) register the new files

` git add mymapname.*`

4) commit (and describe) your changes

` git commit -a`

5) push your stuff to the branch on your own fork

` git push`

Finally, got to your maps fork on the github site and create a pull request as described above.

## Community repo

Community members can get access to a special github repository for extra content, such as utility scripts, art sources or custom maps:

` `[`https://github.com/red-eclipse/community`](https://github.com/red-eclipse/community)

This means they can commit and push new content directly to this repo, i.e. without the need to create pull requests.

So, once you have cloned the repo

` git clone `[`https://github.com/red-eclipse/community`](https://github.com/red-eclipse/community)` re-community`

new content can be pushed as described above. Before doing so, it makes sense to update your local copy first.

` git pull`
` *add your updates*`
` git commit`
` git push`

One last thing. It can happen that you do a commit and then realize that you made some mistake, which you want to undo. If you have not pushed it yet to the repository, you can kill the previous commit and reset your local copy as follows:

` git reset --hard HEAD~1`

This way, all changes are lost. Use *soft* instead of *hard* to only revert the commit, but keep all modifications you made staged, e.g. when you just want to rewrite the commit text (description).
