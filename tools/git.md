# Git

```shell
# This fetches and prunes remote-tracking branches
git fetch --prune

## Delete local branches with deleted upstreams
git fetch -p && git branch -vv | awk '/: gone]/{print $1}' | xargs git branch -d
```
