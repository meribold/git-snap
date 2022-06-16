`git snap` creates a commit that includes most local changes on a user- and
machine-specific snapshots branch.  It doesn't ask for a commit message.  It also doesn't
touch the working tree (working directory), saves and restores the index, and switches
back to the branch that was checked out before running `git snap`.  In other words,
everything looks exactly the same as before after running `git snap`, but your changes are
safely stored by Git and can be pushed to a remote for redundancy or sharing.  This is in
contrast to stashes.

## Usage

    git snap

This creates a commit with most local changes on a branch named
`snapshots-<username>-<hostname>`.<sup>[1](#user-content-footnote-1)</sup>  Take a look at
the [`snapshots-meribold-smial`][3] branch of this repository to see some commits created
using `git snap`.

## What exactly is included in a snapshot commit?

The version of a file that gets committed is always the one in your working tree.  Which
files are included depends on whether the file exists in the working tree and the index.

*   If the file doesn't exist in the working tree then it doesn't go into the snapshot
    commit.
*   If the file exists but isn't staged then it also **doesn't** go into the snapshot
    commit.
*   If the file exists in both the working tree and the index then it **does** go into the
    snapshot commit.

| working tree | index  | `HEAD` | tip of snapshots branch | description                      | included in snapshot commit                        |
|--------------|--------|--------|-------------------------|----------------------------------|----------------------------------------------------|
|              |        |        | exists                  | after `git rm` and committing    | :x: removed from snapshots branch                  |
|              |        | exists |                         | after `git rm`                   |                                                    |
|              |        | exists | exists                  | after `git rm`                   | :x: removed from snapshots branch                  |
|              | exists |        |                         | new file, added and then removed |                                                    |
|              | exists |        | exists                  | new file, added and then removed | :x: removed from snapshots branch                  |
|              | exists | exists |                         | tracked file, after `rm`         |                                                    |
|              | exists | exists | exists                  | tracked file, after `rm`         | :x: removed from snapshots branch                  |
| exists       |        |        |                         | new file, not staged             |                                                    |
| exists       |        |        | exists                  | new file, not staged             | :x: removed from snapshots branch                  |
| exists       |        | exists |                         | after `git rm --cached`          |                                                    |
| exists       |        | exists | exists                  | after `git rm --cached`          | :x: removed from snapshots branch                  |
| exists       | exists |        |                         | new file, staged                 | :heavy_check_mark: added to snapshots branch       |
| exists       | exists |        | exists                  | new file, staged                 | :heavy_check_mark: updated to working tree version |
| exists       | exists | exists |                         | tracked file                     | :heavy_check_mark: added to snapshots branch       |
| exists       | exists | exists | exists                  | tracked file                     | :heavy_check_mark: updated to working tree version |

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
[3]: https://github.com/meribold/git-snap/commits/snapshots-meribold-smial
