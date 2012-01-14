# On undoing, fixing, or removing commits in git

This document is an attempt to be a fairly comprehensive guide to
recovering from what you did not mean to do when using git.  It isn't
that git is so complicated that you need a large document to take care
or your particular problem, it is more that the set of things that you
might have done is so large that different techniques are needed
depending on exactly what you have done and what you want to have
happen.


## First step

Strongly consider taking a
[backup](https://gist.github.com/1540906#backups) of your current
working directory and .git to avoid any possibility of losing data as
a result of the use or misuse of these instructions. We promise to
laugh at you if you fail to take a backup and regret it later.

Proceed to the next section.  Afterwards, answer the questions posed
by clicking the link for that section.  A section with no links (other
than this one) is a terminal node and you should have solved your
problem by completing the suggestions posed by that node.  This is not
a document to read linearly.


## Are you trying to find that which is lost or fix a change that was made?

Due to previous activities (thrashing about), you may have lost some
work which you would like to find and restore.  Alternately, you may
have made some changes which you would like to fix.

* [Fix a change](#committedp)
* [Find what is lost](#lostnfound)


<a name="committedp" />
## Have you committed?

If you have not yet committed that which you do not want, git does not
know anything about what you have done, yet, so it is pretty easy to
undo what you have done.

* [Yes, I committed](#committed)
* [No, I have not yet committed](#uncommitted)


<a name="uncommitted" />
## Everything or just some things?

So you have not yet committed, the question is now whether you want to
undo everything which you have done since the last commit or just some
things?

* [Discard everything](#uncommitted_everything)
* [Discard some things](#uncommitted_somethings)


<a name="uncommitted_everything" />
## How to undo all uncommitted changes

So you have not yet committed and you want to undo everything.  Well,
[best practice](https://gist.github.com/1540906) is for you to stash
the changes in case you were mistaken and later decide that you really
wanted them after all. `git stash save "description of changes"`. You
can revisit those stashes later `git stash list` and decide whether to
`git stash drop` them after some time has past.  Please note that
untracked and ignored files are not stashed by default.  See
"--include-untracked" and "--all" for stash options to handle those
two cases.

However, perhaps you hare confident (or arrogant) enough to know for
sure that you will never ever want the uncommitted changes.  If so,
you can run `git reset --hard`, however please be quite aware that
this is almost certainly a completely unrecoverable operation.  Any
changes which are removed here cannot be restored later.  This will
not delete untracked or ignored files.  Those can be deleted with `git
clean -nd` `git clean -ndX` respectively, or `git clean -ndx` for both
at once.  Well, actually those command do not delete the files.  They
show what files will be deleted.  Replace the "n" in "-nd…" with "f"
to actually delete the files.  [Best
practice](https://gist.github.com/1540906) is to ensure you are not
deleting what you should not by looking at the moribund filenames
first.


<a name="uncommitted_somethings">
## How to undo some uncommitted changes

So you have not yet committed and you want to undo some things, well
`git status` will tell you exactly what you need to do.  For example:

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
```

However, the `git checkout` in file mode is a command that cannot be
recovered from—the changes which are discarded most probably cannot be
recovered.  Perhaps you should run `git stash save -p "description"`
instead, and select the changes you no longer want to be stashed
instead of zapping them.


<a name="committed" />
## Do you have a clean working directory?
<!-- This text is duplicated in #foo -->

So you have committed.  However, before we go about fixing or removing
whatever is wrong, you should first ensure that any uncommitted
changes are safe, by either committing them (`git commit`) or by
stashing them (`git stash save "message"`) or getting rid of them.
`git status` will help you understand whether your working directory
is clean or not.

* [My working directory is quite clean](#committed_really)
* [My working directory is filthy and I want to discard it](#uncommitted)


<a name="committed_really" />
## Have you pushed?

So you have committed, the question is now whether you have made your
changes publicly available or not.  Publishing history is seminal
event.

Please note in any and all events, the recipes provided here will
typically (only one exception which will self-notify) only modify the
current branch you are on.  Specifically any tags or branches
involving the commit you are changing or a child or that commit will
not be modified.  You must deal with those separately.  Look at `gitk
--all --date-order` to help visualize everything what other git
references might need to be updated.

* [Yes, I pushed](#pushed)
* [No, I did not push](#unpushed)


<a name="unpushed" />
## Do you want to discard all unpushed changes on this branch?

There is a shortcut in case you want to discard all changes made on
this branch since you have last pushed or in any event, to make your
local branch identical to "upstream".

* [I want to discard all unpushed changes](#discard_all_unpushed)
* [I want to fix some unpushed changes](#fix_unpushed)


<a name="discard_all_unpushed" />
## Discarding all local commits on this branch

In order to discard all local commits on this branch, to make the
local branch identical to the "upstream" of this branch, simply run
`git reset --hard @{u}`


<a name="fix_unpushed" />
## Is the commit you want to fix the most recent?

While the techniques mentioned to deal with deeper commits will work
on the most recent, there are some convenient shortcuts you can take
with the most recent commit.

* [I want to change the most recent commit](#change_last)
* [I want to change an older commit](#change_deep)


<a name="change_last" />
## Do you want to remove or change the commit message/contents of the last commit?

* [I want to remove the last commit](#remove_last)
* [I want to update the message/contents of the last commit](#update_last)


<a name="remove_last" />
## Removing the last commit

To remove the last commit from git, you can simply run `git reset
--hard HEAD^` If you are removing multiple commits from the top, you
can run `git reset HEAD~2` to remove the last two commits.  You can
increase the number to remove even more commits.


<a name="update_last" />
## Updating the last commit's contents or commit message

To update the last commit's contents or commit message for a commit
which you have not pushed or otherwise published, first you need to
get the index into the correct state you wish the commit to reflect.
If you are changing the commit message only, you need do nothing.  If
you are changing the file contents, typically you would modify the
working directory and use `git add` as normal.

Once the index is in the correct state, then you can run `git commit
--amend` to update the last commit.  Yes, you can use "-a" if you want
to avoid the `git add` suggested in the previous paragraph.


<a name="change_deep" />
## Do you want to remove an entire commit?

* [I want to remove an entire commit](#remove_deep)
* [I want to change an older commit](#change_deep)


<a name="remove_deep" />
## Removing an entire commit

I call this operation "cherry-pit" since it is the inverse of a
"cherry-pick".  You must first identify the SHA of the command you
wish to remove.  You can do this using `gitk --date-order` or using
`git log --graph --decorate --oneline` You are looking for the 40
character SHA-1 hash ID (or the 7 character abbreviation).  Yes, if
you know the "^" or "~" shortcuts you can use those.

```shell
git rebase -p --onto SHA^ SHA
```

Obviously replace "SHA" with the reference you want to get rid of.
The "^" in that command is literal.

However, please be warned.  If some of the commits between SHA and the
tip of your branch are merge commits, it is possible that `git rebase`
will be unable to properly recreate them.  Please inspect the
resulting merge topology `gitk --date-order HEAD ORIG_HEAD` to ensure
that git did want you wanted.  If it did not, there is not really any
automated recourse.  You can reset back to the commit before the SHA
you want to get rid of, and then cherry-pick the normal commits and
manually re-merge the "bad" merges.  Or you can just suffer with the
inappropriate topology (perhaps creating fake merges `git merge --ours
otherbranch` so that subsequent development work on those branches
will be properly merged in with the correct merge-base).

<a name="change_deep" />
## Do you want to remove/change/rename a particular file/directory from all commits during all of git's history

* [Yes please, I want to make a change involving all git commits](#filterbranch)
* [No, I only want to change a single commit](#change_single_deep)


<a name="filterbranch" />
## Changing all commits during all of git's history

You have not pushed but still somehow want to change all commits in
all of git's history?  Strange.

You want to use the `git filter-branch` command to perform this
action.  This command is quite involved and complex, so I will simply
point you at the [manual page](http://jk.gs/git-filter-branch.html)
and remind you that [best practice](https://gist.github.com/1540906)
is to always use ` --tag-name-filter cat -- --all` unless you are
really sure you know what you are doing.

BTW, this is the one command I referred to earlier which will update
all tags and branches, at least if you use the best practice
arguments.

<a name="change_single_deep" />
## Is a merge commit involved?

If the commit you are trying to change is a merge commit, or if there
is a merge commit between the commit you are trying to change and the
tip of the branch you are on, then you need to do some special
handling of the situation.

* [Yes, a merge commit is involved](#change_single_deep_merge)
* [No, only simple commits](#change_single_deep_simple)


<a name="change_single_deep_merge" />
<a name="change_single_deep_simple" />
<a name="pushed" />
<a name="lostnfound" />

<a name="copyright" />
## Copyright

Copyright ⓒ 2012 Seth Robertson

Creative Commons Attribution-ShareAlike 2.5 Generic (CC BY-SA 2.5)

http://creativecommons.org/licenses/by-sa/2.5/

I would appreciate changes being sent back to me, being notified if
this is used or highlighted in some special way, and links being
maintained back to the authoritative source.  Thanks.


<a name="thanks" />
## Thanks

Thanks to the experts on #git for review, feedback, and ideas.


<a name="comments" />
## Comments

Comments and improvements welcome.

Add them below, or discuss with SethRobertson (and others) on #git


## Line eater fodder

Because of my heavy use of anchors for navigation, and the utter lack
of flexibility in the markup language this document is written in, it
is important that the document be long enough at the bottom to
complete fill your screen so that the item your link directed you to
is at the top of the page.

<pre>












































</pre>

Hopefully that should do it.
