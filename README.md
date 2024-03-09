# git rebase playground

## how to use

as when playing around with rebasing - you sometimes with a rewind button, here you got it.
the initial state of the repo is backed up inside the bak_1.bundle, which was created using `git bundle create ../repo.bundle --all` inside the `./repo` folder.
you can always regenerate the initial state by `git clone repo.bundle repo`

this is the initial state (`git log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all`)

```log

* be11332 - (3 minutes ago) 10 - Your Name Here (HEAD -> main, origin/main, origin/HEAD)
* 440ac22 - (3 minutes ago) 9 - Your Name Here
* f09f322 - (4 minutes ago) 8 - Your Name Here
* 1a381ae - (4 minutes ago) 7 - Your Name Here
| * c12547f - (4 minutes ago) 6 - Your Name Here (origin/feat)
| * c67e571 - (4 minutes ago) 5 - Your Name Here
| * 94f5e58 - (4 minutes ago) 4 - Your Name Here
|/  
* f8437ca - (5 minutes ago) 3 - Your Name Here
* 52cb0c0 - (5 minutes ago) 2 - Your Name Here
* 9e81122 - (5 minutes ago) 1 - Your Name Here
* 5e60189 - (5 minutes ago) initial commit - Your Name Here

```

## rebase fun

### going through rebase hell

being on feat, with a `git rebase main` you will have to resolve 3 merge conflicts, and the final state will look like this

```log

* a0b9cdc - (8 minutes ago) 6 - Your Name Here (HEAD -> feat)
* 7b0bd2e - (9 minutes ago) 5 - Your Name Here
* 50908e0 - (9 minutes ago) 4 - Your Name Here
* be11332 - (8 minutes ago) 10 - Your Name Here (origin/main, origin/HEAD, main)
* 440ac22 - (8 minutes ago) 9 - Your Name Here
* f09f322 - (8 minutes ago) 8 - Your Name Here
* 1a381ae - (8 minutes ago) 7 - Your Name Here
| * c12547f - (8 minutes ago) 6 - Your Name Here (origin/feat)
| * c67e571 - (9 minutes ago) 5 - Your Name Here
| * 94f5e58 - (9 minutes ago) 4 - Your Name Here
|/  
* f8437ca - (9 minutes ago) 3 - Your Name Here
* 52cb0c0 - (9 minutes ago) 2 - Your Name Here
* 9e81122 - (9 minutes ago) 1 - Your Name Here
* 5e60189 - (10 minutes ago) initial commit - Your Name Here

```

### avoid rebase hell by squashing first

create a clean slate first by

```bash
rm -rf repo/
git clone repo.bundle repo
```

then we follow whats described [here](https://gist.github.com/n00neimp0rtant/9515611):

* switch to main `git checkout origin/main`
* create a branch called feat_tmp `git checkout -b feat_tmp`
* squash merge all changes from feat `git merge --squash feat`
* resolve one (not three) merge conflicts
* rename the old feat branch `git branch -m origin/feat origin/feat_unsquashed`
* move the original feat branch to our tmp branch `git branch -m feat`
* (optional) remove the old unsquashed feat branch `git branch -D feat_unsquashed`
* (optional, because local setup) force push to remote `git push --force-with-lease`

result

```log

* 8b578f4 - (9 minutes ago) Squashed commit of the following: - Max (HEAD -> feat)
* be11332 - (21 minutes ago) 10 - Your Name Here (origin/main, origin/HEAD, main)
* 440ac22 - (21 minutes ago) 9 - Your Name Here
* f09f322 - (21 minutes ago) 8 - Your Name Here
* 1a381ae - (21 minutes ago) 7 - Your Name Here
| * c12547f - (21 minutes ago) 6 - Your Name Here (origin/feat, feat_unsq)
| * c67e571 - (22 minutes ago) 5 - Your Name Here
| * 94f5e58 - (22 minutes ago) 4 - Your Name Here
|/  
* f8437ca - (22 minutes ago) 3 - Your Name Here
* 52cb0c0 - (22 minutes ago) 2 - Your Name Here
* 9e81122 - (22 minutes ago) 1 - Your Name Here
* 5e60189 - (23 minutes ago) initial commit - Your Name Here

```

with our squased commit having details on the original ones:

```log

Commit: 8b578f4a4dbe26aac31b1d931fd86a84fc1191ff
Parents: be11332d7686e0634f287b7d129fee0036b75a78
Author: Your Name Here <your@email.example>
Committer: Your Name Here <your@email.example>
Date: Sat Mar 09 2024 21:20:17 GMT+0100 (Central European Standard Time)
 

Squashed commit of the following:

commit c12547fe9d7b6dd58d2a8cfff21083468d0aff3f
Author: Your Name Here <your@email.example>
Date: Sat Mar 9 21:07:37 2024 +0100

    6

commit c67e571b836263630174f23dc7c725bf03090181
Author: Your Name Here <your@email.example>
Date: Sat Mar 9 21:07:25 2024 +0100

    5

commit 94f5e582305ea44950850cd456784f8ee125fa14
Author: Your Name Here <your@email.example>
Date: Sat Mar 9 21:07:16 2024 +0100

    4


```

[more info](https://blog.oddbit.com/post/2019-06-17-avoid-rebase-hell-squashing-wi/)
