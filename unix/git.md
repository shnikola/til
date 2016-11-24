# Git

TODO:
Git blame
git reset head

## Basics
`git pull --rebase` - neće raditi merge (dodaje commit u kojem spaja nove i lokalne promjene)
nego rebase (sve lokalne promjene će iznova primjeniti na zadnji commit)
`git rebase --abort` - ako se unutar rebasea dogodi konflikt, a želiš odustati
`git reflog` lista svega što se napravilo u gitu, s indexima. Restore na taj trenutak s `git reset HEAD@{index}`
`git clean -f -d` briše sve netrackane fileove, kao `hg purge`. Korisni kad odustaneš od merga, a ostane ti nemerganih fielova.


## Korisni aliasi
* `alias.commend 'commit --amend --no-edit'` ako zaboraviš dodati file u commitao. Napravi `add` pa `commend`.
* `alias.staash 'stash --include-untracked'` stash koji dodaje untracked fileove
* `alias.st 'status --short --branch'` kratki ispis statusa
* `alias.merc 'merge --no-ff'` merge uvijek stvara novi commit


## Commitao sam u krivi branch
* `git checkout correct-branch`
* `git cherry-pick master`
* `git checkout master`
* `git reset HEAD~ --hard` briše commit iz mastera


## Workflows
Za jednostavan proivod, koristi *Feature Branch workflow*:
* Svaki feature se razvija u svom branchu.
* Kad je spreman, merga se u `master`.

Za ozbiljniji proizvod s releasovima i verzijama, koristi *Gitflow*:
* Feature branchevi se mergaju u `develop`. Kad se skupi dovoljno featurea, `develop` se forka u `release-0.1`.
* U `release-0.1` se ne dodaju novi featuri, već samo ispravljaju bugovi i piše dokumentacija.
* Kad je spremno za shipping, `release-0.1` se mergea u `master` i tagira se verzijom.
* `master` se onda mergea nazad u `develop`. Na ovaj način jedan team može dovršavati release dok drugi radi
na idućoj verziji.
