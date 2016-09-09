# Git

TODO:
Git blame


## Git workflows
https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow

`git pull --rebase` - neće raditi merge (dodaje commit u kojem spaja nove i lokalne promjene)
nego rebase (sve lokalne promjene će iznova primjeniti na zadnji commit)

`git rebase --abort` - ako se unutar rebasea dogodi konflikt, a želiš odustati

**Feature branch workflow:**
U principu ovo što trenutno radimo - jedan feature, jedan branch; kad je spreman, mergaš ga u master.

**Gitflow workflow:**
Za ozbiljniji proizvod s releasovima i verzijama. Feature branchevi se mergaju u `develop`.
Kad se skupi dovoljno featurea, develop branch se forka u `release-0.1` - tu se više featuri ne dodaju, već samo
ispravljaju bugovi i piše dokumentacija. Kad je spremno za shipping, `release-0.1` se mergea u `master` i tagira
se verzijom. `master` se onda mergea nazad u `develop`. Na ovaj način jedan team može dovršavati release dok drugi radi
na idućoj verziji.

**Forking workflow:**
Svatko radi na vlastitom repozitoriju, i šalju pull-request adminu koji jedini ima pristup nad centralnim repom.


## Commitao sam, a zaboravio dodati file.
* `git add -A` da dodaš file.
* `git commit --ammend` dodaje promjenu u zadnji commit.

## Želim popraviti commit message
* `git commit --ammend`

## Commitao sam u krivi branch
* `git reset HEAD~ --soft` undo commit, ostavlja promjene
* `git checkout correct-branch`
* `git commit -m`
