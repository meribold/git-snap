#!/usr/bin/env bash

cd "$(git rev-parse --show-cdup)" || exit

snapshot_branch=snapshots-$USER-$HOSTNAME
head=$(git symbolic-ref HEAD) || exit
index=$(mktemp) || exit

# shellcheck disable=SC2015
git diff --staged > "$index" && git symbolic-ref HEAD "refs/heads/$snapshot_branch" || {
   rm -f "$index"
   exit 1
}

# Use a trap to make sure we restore HEAD and the index no matter what.
cleanup() {
   git symbolic-ref HEAD "$head" &&
   # Restore the index.
   git reset -q &&
   if [[ -s $index ]]; then
      # The patch exists and is not empty.  Apply it to the index.
      git apply --cached "$index" && rm "$index"
   else
      rm "$index"
   fi
}
trap cleanup EXIT

# Unstage tracked files (untracked files that have been staged remain staged).  Using `--`
# after `git reset` prevents ambiguity (e.g., when a file doesn't exist in the working
# tree).
git ls-tree -r --full-tree --name-only @ 2>/dev/null | xargs -r git reset -q --

# This will also commit new files that are already staged.  I think that's good.
git -c advice.statusHints=false commit -uno --no-verify -am 'Snapshot

This is a snapshot commit created using git-snap.

https://github.com/meribold/git-snap'
