# On undoing, fixing, or removing commits in git

A flowchart to navigate you through the many options.

## First step

Strongly consider taking a backup of your current working directory and .git to avoid any possibility of losing data as a result of the use or misuse of these instructions. We promise to laugh at you if you fail to take a backup and regret it later.

Proceed to the next section.  Afterwards, answer the questions posed by clicking the link for that section.  A section with no links (other than this one) is a terminal node and you should have solved your problem by completing the suggestions posed by that node.  This is not a document to read linearly.


## Have you committed?

If you have not yet committed that which you do not want, git does not know anything about what you have done, yet, so it is pretty easy to undo what you have done.

* [Yes, I committed](#committed)
* [No, I have not yet committed](#uncommitted)


<a name="uncommitted" />
## Everything or just some things?

So you have not yet committed, the question is now whether you want to undo everything which you have done since the last commit or just some things?

* [Discard dverything](#uncommitted_everything)
* [Discard some things](#uncommitted_somethings)


<a name="uncommitted_everything" />
## How to undo all uncommitted changes

So you have not yet committed and you want to undo everything.  Well, best practice is for you to stash the changes in case you were mistaken and later decide that you really wanted them after all. `git stash save "description of changes"`  You can revisit those stashes later `git stash list` and decide whether to `git stash drop` them after some time has past.  Please note that untracked and ignored files are not stashed by default.  See "--include-untracked" and "--all" for stash options to handle those two cases.

However, perhaps you hare confident (or arrogant) enough to know for sure that you will never ever want the uncommitted changes.  If so, you can run `git reset --hard`, however please be quite aware that this is almost certainly a completely unrecoverable operation.  Any changes which are removed here cannot be restored later.  This will not delete untracked or ignored files.  Those can be deleted with `git clean -nd` `git clean -ndX` respectively, or `git clean -dnx` for both at once.  Well, actually those command do not delete the files.  They show what files will be deleted.  Replace the "n" in "-nd…" with "f" to actually delete the files.  Best practice is to ensure you are not deleting what you should not.


<a name="uncommitted_somethigns">
## How to undo some uncommitted changes

So you have not yet committed and you want to undo some things, well `git status` will tell you exactly what you need to do.  For example:

```
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#       new file:   .gitignore
#
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   A
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#       C
'''

However, the `git checkout` in file mode is a command that cannot be recovered from—the changes which are discarded most probably cannot be recovered.  Perhaps you should run `git stash save -p "description"` instead, and select the changes you no longer want to be stashed instead of zapping them.


<a name="committed" />
## Have you pushed?

So you have committed, the question is now whether you have made your changes publicly available or not.  Once you `git push` or otherwise publish your changes, someone else might have retrieved the change and started basing their work on it.  Similarly, shared bare git repositories do not allow history to be overwritten by default.  In other words, having pushed is a seminal event.

* [Yes, I pushed](#pushed)
* [No, I did not push](#unpushed)