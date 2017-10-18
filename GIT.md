## Git push without a branch specified

Basically it explains how to set the default behavior to push your current branch just executing git push. Probably what you need is:

```
git config --global push.default current
```

Other options:

**nothing** : Do not push anything
**matching** : Push all matching branches
**upstream/tracking** : Push the current branch to whatever it is tracking
**current** : Push the current branch

[Reference](https://stackoverflow.com/questions/14031970/git-push-current-branch-shortcut)
