# git-snap

`git snap` creates a commit on a user- and machine-specific snapshots branch that includes
most local changes.  It doesn't ask for a commit message.  It also doesn't touch the
working tree (working directory), saves and restores the index, and switches back to the
branch that was checked out before running `git snap`.  In other words, everything looks
exactly the same as before after running `git snap`, but your changes are safely stored by
Git and can be pushed to a remote for redundancy or sharing.  This is in contrast to
stashes.

## Usage

    git snap

This will create a commit with most local changes on a branch named
`snapshots-<username>-<hostname>`.<sup>[1](#user-content-footnote-1)</sup>  Take a look at
the [`snapshots-meribold-smial`][3] branch of this repository to see some commits created
using git-snap.

git-snap is save.  It doesn't touch the working tree (working directory) and saves and
restores the index.

## What exactly is included in a snapshot commit

The version of a file that gets committed is always the one in your working tree.  Which
files are included depends on whether the file exists in the working tree, the index,
`HEAD`, and the tip of the snapshots branch.

| working tree | index  | `HEAD` | tip of snapshots branch | included in snapshot commit                                      |
|--------------|--------|--------|-------------------------|------------------------------------------------------------------|
|              |        |        |                         | :x:                                                              |
|              |        |        | exists                  | :x: (removed from snapshots branch)                              |
|              |        | exists |                         | :x:                                                              |
|              |        | exists | exists                  | :x: (removed from snapshots branch)                              |
|              | exists |        |                         | :x:                                                              |
|              | exists |        | exists                  | :x: (removed from snapshots branch)                              |
|              | exists | exists |                         | :x:                                                              |
|              | exists | exists | exists                  | :x: (removed from snapshots branch)                              |
| exists       |        |        |                         | :x:                                                              |
| exists       |        |        | exists                  | :x: (removed from snapshots branch)                              |
| exists       |        | exists |                         | :x:                                                              |
| exists       |        | exists | exists                  | :x: (removed from snapshots branch)                              |
| exists       | exists |        |                         | :heavy_check_mark: (new file; staged; added to snapshots branch) |
| exists       | exists |        | exists                  | :heavy_check_mark: (already there)                               |
| exists       | exists | exists |                         | :heavy_check_mark: (added to snapshots branch)                   |
| exists       | exists | exists | exists                  | :heavy_check_mark: (already there)                               |

Most of these shouldn't be surprising.  To summarize:

*   If the file doesn't exist in the working tree then it doesn't go into the snapshot
    commit.
*   If the file exists but isn't staged then it doesn't go into the snapshot commit.
*   If the file exists in both the working tree and the index then it goes into the
    snapshot commit.

That covers new files.  But what about modified files?

## Installation

Save git-snap to some directory in your `$PATH`.

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
[3]: https://github.com/meribold/git-snap/commits/snapshots-meribold-smial
