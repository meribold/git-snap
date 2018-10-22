Sometimes I want to have some work that isn't yet ready for a proper commit available on
another computer.  Other times I want to push unfinished work to a remote, so data loss is
less likely.  In either case, I usually [switch branches without touching the working
tree][1], create a throwaway commit, and switch back to the original branch.

This works, but it's a bit manual and cumbersome.  It becomes more difficult when already
having added a subset of the local changes to the index in preparation of the next commit:
how is the index saved and restored?

That's why I wrote `git-snap`.

## Usage

After saving `git-snap` to some directory in your `$PATH`:

    $ git snap

This will create a commit with all changes to tracked files on a branch called
`snapshots-<username>-<hostname>`.  It doesn't touch the working directory and will
restore everything else (`HEAD` and the index) after making the commit.  Take a look at
the [`snapshots-casi-elanor`][3] branch of this repository to see some commits that
`git-snap` created.

[1]: https://stackoverflow.com/q/6070179
     "Switching branches without touching the working tree?"
[2]: https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified#_the_index
     "Git Tools - Reset Demystified - Pro Git"
[3]: https://github.com/meribold/git-snap/commits/snapshots-casi-elanor
