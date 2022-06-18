`git snap` creates a commit that includes most local changes on a user- and
machine-specific snapshots branch.  It doesn't ask for a commit message.  It also doesn't
touch the working tree, saves and restores the index, and switches back to the branch that
was checked out before running `git snap`.  In other words, everything looks the same as
before after running `git snap`, but your changes are safely stored by Git and can be
pushed to a remote for redundancy or sharing.  This is in contrast to stashes.

## Usage

    git snap

This creates a commit with most local changes on a branch named
`snapshots-<username>-<hostname>`.  Take a look at the [`snapshots-meribold-smial`][1]
branch of this repository to see some commits created using `git snap`.

## Installation

Save `git-snap` to some directory in your `$PATH`.

## What gets included in a snapshot commit?

The version of a file that gets committed is always the one in your working tree.  Which
files are included depends on whether the file exists in the working tree and the index.

*   If the file exists in both the working tree and the index, it goes into the snapshot
    commit.
*   If the file doesn't exist in the working tree, it doesn't go into the snapshot commit.
*   If the file exists but isn't staged, it **also doesn't** go into the snapshot commit.

Each row in the following table specifies whether a file exists in any of four trees: the
working tree, the index, `HEAD`, and the tip of the snapshots branch.  The last column
indicates whether `git snap` would include this file in the commit it creates.  The
version that is included is always the one from the working tree.

| working tree | index  | `HEAD` | tip of snapshots branch | description                       |                    | effect                          |
|--------------|--------|--------|-------------------------|-----------------------------------|--------------------|---------------------------------|
|              |        |        | exists                  | after `git rm` and committing     | :heavy_minus_sign: | removed from snapshots branch   |
|              |        | exists |                         | after `git rm`                    |                    |                                 |
|              |        | exists | exists                  | after `git rm`                    | :heavy_minus_sign: | removed from snapshots branch   |
|              | exists |        |                         | new file, staged and then removed |                    |                                 |
|              | exists |        | exists                  | new file, staged and then removed | :heavy_minus_sign: | removed from snapshots branch   |
|              | exists | exists |                         | tracked file, after `rm`          |                    |                                 |
|              | exists | exists | exists                  | tracked file, after `rm`          | :heavy_minus_sign: | removed from snapshots branch   |
| exists       |        |        |                         | new file, not staged              |                    |                                 |
| exists       |        |        | exists                  | new file, not staged              | :heavy_minus_sign: | removed from snapshots branch   |
| exists       |        | exists |                         | after `git rm --cached`           |                    |                                 |
| exists       |        | exists | exists                  | after `git rm --cached`           | :heavy_minus_sign: | removed from snapshots branch   |
| exists       | exists |        |                         | new file, staged                  | :heavy_plus_sign:  | added to snapshots branch       |
| exists       | exists |        | exists                  | new file, staged                  | :wavy_dash:        | updated to working tree version |
| exists       | exists | exists |                         | tracked file                      | :heavy_plus_sign:  | added to snapshots branch       |
| exists       | exists | exists | exists                  | tracked file                      | :wavy_dash:        | updated to working tree version |

[1]: https://github.com/meribold/git-snap/commits/snapshots-meribold-smial
