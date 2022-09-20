`git snap` creates a commit that includes most local changes on a user- and
machine-specific snapshots branch.  It doesn't ask for a commit message.  It also doesn't
touch the working tree, saves and restores the index, and switches back to the branch that
was checked out before running `git snap`.  In other words, everything looks the same as
before after running `git snap`, but your changes are safely stored by Git and can be
pushed to a remote for redundancy or sharing.  This is in contrast to stashes.

## Usage

    git snap

This creates a commit with most local changes on a branch named
`snapshots-<username>-<hostname>`.  There are no options or parameters.  Take a look at
the [`snapshots-meribold-smial`][1] branch of this repository to see some commits created
using `git snap`.

## Installation

Save `git-snap` to some directory in your `$PATH` and make it executable.  E.g.:

```bash
curl -f https://raw.githubusercontent.com/meribold/git-snap/v1.1.3/git-snap > ~/bin/git-snap
chmod +x ~/bin/git-snap
```

## What gets included in a snapshot commit?

The version of a file that gets committed is always the one in your working tree.  Which
files are included depends on the working tree, the index, and the tip of the snapshots
branch.  Only files that exist in the working tree and in the index or tip of the
snapshots branch go into the snapshot commit.

*   If the file doesn't exist in the working tree, it doesn't go into the snapshot commit.
    Otherwise:
    *   If the file is staged, it goes into the snapshot commit.
    *   If the file exists in the tip of the snapshots branch, it goes into the snapshots
        commit.
    *   Otherwise the file doesn't go into the snapshot commit.

Each row in the following table specifies whether a file exists in any of four trees: the
working tree, the index, `HEAD`, and the tip of the snapshots branch.  The last column
indicates whether `git snap` would include this file in the commit it creates.  The
version that is included is always the one from the working tree.

| working tree | index  | `HEAD` | tip of snapshots branch | description                             |                    | effect                          |
|--------------|--------|--------|-------------------------|-----------------------------------------|--------------------|---------------------------------|
| exists       | exists | exists | exists                  | tracked file                            | :wavy_dash:        | updated to working tree version |
| exists       | exists | exists |                         | ''                                      | :heavy_plus_sign:  | added to snapshots branch       |
| exists       | exists |        | exists                  | untracked but staged file               | :wavy_dash:        | updated to working tree version |
| exists       | exists |        |                         | ''                                      | :heavy_plus_sign:  | added to snapshots branch       |
| exists       |        | exists | exists                  | after `git rm --cached`                 | :wavy_dash:        | updated to working tree version |
| exists       |        | exists |                         | ''                                      |                    |                                 |
| exists       |        |        | exists                  | untracked and unstaged file             | :wavy_dash:        | updated to working tree version |
| exists       |        |        |                         | ''                                      |                    |                                 |
|              | exists | exists | exists                  | tracked file, after `rm`                | :heavy_minus_sign: | removed from snapshots branch   |
|              | exists | exists |                         | ''                                      |                    |                                 |
|              | exists |        | exists                  | untracked file, staged and then removed | :heavy_minus_sign: | removed from snapshots branch   |
|              | exists |        |                         | ''                                      |                    |                                 |
|              |        | exists | exists                  | after `git rm`                          | :heavy_minus_sign: | removed from snapshots branch   |
|              |        | exists |                         | ''                                      |                    |                                 |
|              |        |        | exists                  | after `git rm` and committing           | :heavy_minus_sign: | removed from snapshots branch   |

## Similar projects

*   [Dura][2] is a background process that automatically creates commits similar to those
    created by `git snap`.

[1]: https://github.com/meribold/git-snap/commits/snapshots-meribold-smial
[2]: https://github.com/tkellogg/dura
