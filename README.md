## Motivation

Sometimes I want to have some work that isn't yet ready for a proper commit available on
another machine.  Other times I want to push unfinished work to a remote, so data loss is
less likely.  In either case, I usually

1.  switch branches [without touching the working tree][1],
2.  create a throwaway commit,
3.  switch back to the original branch.

This works, but it's a bit cumbersome, so I wrote `git-snap` to automate it.

## Usage

    $ git snap

This will create a commit with all changes to tracked files on a branch called
`snapshots-<username>-<hostname>`.<sup>[1](#user-content-footnote-1)</sup>  It doesn't
touch the working directory and will restore everything else (`HEAD` and the index) after
making the commit.  Take a look at the [`snapshots-casi-elanor`][3] branch of this
repository to see some commits created with `git-snap`.

## Installation

Save `git-snap` to some directory in your `$PATH`.

## Footnotes

<ol>
<li id="footnote-1">
Untracked but (partially) staged files are also committed.
</li>
</ol>

[1]: https://stackoverflow.com/q/6070179
     "Switching branches without touching the working tree?"
[2]: https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified#_the_index
     "Git Tools - Reset Demystified - Pro Git"
[3]: https://github.com/meribold/git-snap/commits/snapshots-casi-elanor
