# Git

## Savjeti

Kada pišeš commit message, fokusiraj se na "zašto", a ne "što" (taj dio ti pokriva kod)

Za pisanje commit messaga, editor je puno bolje mjesto od terminala. Postavi `git config --global core.editor "atom --wait"`

Korisni aliasi:
* `alias.unstage 'reset HEAD --'`
* `alias.commend 'commit --amend --no-edit'`
* `alias.staash 'stash --include-untracked'` stash koji dodaje untracked fileove
* `alias.st 'status --short --branch'` kratki ispis statusa
* `alias.merc 'merge --no-ff'` merge uvijek stvara novi commit
* `alias.prune 'fetch --prune'` briše lokalne brancheve koji su obrisani na remoteu.

## Tools

`git reflog` lista svega što se napravilo u gitu, s indexima. Restore na taj trenutak s `git reset HEAD@{index}`

`git checkout --patch` interaktivno prolazi kroz sve fileove i pita te želiš li discardati promjene u njemu.

`git blame -C -L 10,20 file.txt` ispisuje commitove koji su dodali linije 10-20 u zadanom fileu. `-C` prepoznaje ukoliko je linija stigla iz nekog drugog filea.

`git log -S "some-code"` daje punu povijest dijela koda.

## Workflows

Za jednostavan proivod, koristi *Feature Branch workflow*.
* Svaki feature se razvija u svom branchu.
* Kad je spreman, merga se u `master`.

Za ozbiljniji proizvod s releasovima i verzijama, koristi *Gitflow*.
* Feature branchevi se mergaju u `develop`. Kad se skupi dovoljno featurea, `develop` se forka u `release-0.1`.
* U `release-0.1` se ne dodaju novi featuri, već samo ispravljaju bugovi i piše dokumentacija.
* Kad je spremno za shipping, `release-0.1` se mergea u `master` i tagira se verzijom.
* `master` se onda mergea nazad u `develop`. Na ovaj način jedan team može dovršavati release dok drugi radi
na idućoj verziji.

## Basics

Git ne sprema verzije fileova kao diffove, nego kao snapshotove. Zbog toga git funckionira više kao mini file system nego kao version control system.

Git projekt se sastoji od 3 dijela:
* `.git` directory u kojem se nalaze objekti i metadata projekta.
* staging area (index) u kojem se drži što ide u sljedeći commit.
* working directory koji je checkout jednog snapshota projekta zapisan na disk i može se modificirati.

Ako je file u working directoriju postojao u posljednjem snapshotu kažemo da je *tracked*, ako nije postojao kažemo da je *untracked*.

`git init` stvara novi `.git` directory unutar postojećeg projekta.
`git clone <url>` dohvaća cijelokupnu povijest svih fileova i stavlja je u lokalni directory. Dodaje remote repository kao `origin`. Postavlja lokalni `master` branch da tracka remote `master`.

`git commit` sprema snapshot iz staging area u `.git` directory.

`git rm <file>` briše file iz staging area i iz working directorija (s diska).
`git rm --cached <file>` briše file iz staging area, ali ga ostavlja na disku.

`git reset HEAD <file>` unstagea file, ali ostavlja promjene.
`git checkout -- <file>` briše promjene iz filea.

## Remote repositories

`git remote` ispisuje sve remote repozitorije.
`git remote show origin` ispisuje detaljne informacije o remoteu.
`git remote add pb https://github.com/paulboone/ticgit` dodaje novi remote repository.
`git remote rename pb paul` mijenja ime remoteu.
`git remote rm paul` briše remote iz liste.

`git fetch pb` dohvaća sve podatke iz remotea koje još nemaš, ali ih ne merga s tvojim podatcima.
`git pull` ako je trenutni branch postavljen da tracka remote branch, automatski radi `fetch` i `merge`.
`git push <remote> <branch>` šalješ svoj branch na zadani remote server. Defaultno, šalje se na `origin` remote i trenutni branch.

## Tags

Tagovi se koriste za označavanje važnih točaka u povijesti repozitorija, npr. release verzija. Lightweight tag je samo pointer na određeni commit, a annotated tag je zaseban objekt koji sadrži autora i komentar.

`git tag v1.3` stvara novi lightweight tag na poslijednji commit.
`git tag -a v1.4 -m "..."` stvara annotated tag.

`git tag` ispisuje sve postojeće tagove.
`git show v1.4` prikazuje commit kojem tag pripada

Tagovi se defaultno ne pushaju na remote repozitorije.
`git push origin <tag>` šalje tag u remote repozitorij.
`git push origin --tags` šalje sve tagove.

## Branches

Branchevi su samo pointeri na određeni commit. Kada se stvori novi commit u branchu, pointer se automatski prebaci na njega.

`git branch` ispisuje lokalne brancheve. `-v` prikazuje posljednji commit svakog brancha.

`git branch staging` stvara novi branch na trenutnom commitu.
`git checkout staging` prebacuje se na branch tako da pointa HEAD na njega.

### Merging

`git merge hotfix` merga `hotfix` branch u trenutni.

Ako branch kojeg mergamo (npr. `hotfix`) direktno slijedi iz trenutnog brancha (npr. `master`), napravit će se *fast forward* - pointer `master` će se samo prebaciti na commit na kojeg pointa `hotfix`. Oba brancha sada pokazuju na isti commit, pa su efektivno mergani.

Ako branhcevi koji mergamo divergiraju, tj. oba imaju commitove koje drugi nema, potrebno je napraviti *three-way merge*. Stvara se merge commit koji ima dva parenta. `master` sada pointa na taj commit.

`git branch --merged` ispisuje sve brancheve koji su mergani s trenutnim.
`git branch --no-merged` ispisuje sve brancheve koji još nisu mergani.

Lokalno se čuvaju i remote branchevi (npr. `origin/master`) koji drže stanje remote servera. To su i dalje samo pointeri unutar postojećeg commit stabla.

### Rebase and Cherry Pick

`git cherry-pick <commit>` kopira cijeli commit i dodaje ga na trenutni branch.

`git rebase <branch>` kopira niz commitova iz trenutnog brancha koji su divergirali od zadanog. Na taj način se može mergati a da se očuvaju pojedinačni commitovi. Primjer rebasea:
* `git checkout feature` nas pozicionira na vrh brancha.
* `git rebase master` dodaje u `master` sve commitove iz `feature`.
* `git checkout master` i `git merge feature` radi fast forward merge da prebaci `master` na posljednji commit.

Ako se unutar rebasea dogodi konflikt, a želiš odustati, koristi `--abort`.

Rebase je dobar jer ostavlja cijelovitu povijest commitova prilikom merga brancheva, ali ne smiješ rebasati commitove koji su već pushani. Prilikom rebasea stvaraš nove commitove i napuštaš stare, što će učiniti kaos u tuđim repozitorijima.

### Remote Branches

Ako netko napravi push na `origin` remote repozitorij i `origin/master` pointa na novi commit, možeš dohvatiti novi commit i promjene na remote branchevima pomoću `git fetch origin`. Ova naredba će dohvatiti nove commitove, ali neće ih mergati u tvoje lokalne brancheve. To moraš napraviti ručno s `git merge origin/master`.

Da bi pojednostavnio dohvaćanje remote promjena, moguće je podesiti da lokalni branch tracka remote branch:
`git checkout --track origin/serverfix` stvara novi lokalni branch `serverfix` koji tracka remote branch `origin/serverfix`.
`git checkout serverfix` isto to samo skraćeno, radi ako postoji samo jedan takav branch u remotima.
`git push -u origin` pusha lokalni branch na `origin` i tracka ga.

Jednom kad se branch tracka, `git pull` će automatski obaviti `fetch` i `merge`. `git branch -vv` ispisuje koji lokalni branch tracka koji remote.

## Stash and Clean

`git stash` sprema sve necommitane promjene u stash.
`git stash pop` dohvaća promjene iz stasha.
`git stash drop` odbacuje promjene iz stasha.
`git stash list` ispisuje sve spremljene stasheve.
`git stash show -p` ispisuje promjene koje će se primjeniti.

`git clean -f -d` briše sve untracked fileove i directorije. Korisni kad odustaneš od merga, a ostane ti nemerganih fielova. `-n` ispisuje što će se ukloniti bez da stvarno obriše.

## Diff

`git diff` ispisuje razliku između working directorija i staging area.
`git diff --staged` ispisuje razliku između staging area i heada.
`git diff master...contrib` razliku između brancha i njegovog trenutka kad se odvojio od mastera.

## Reset

`git reset <commit>` pomjera na što `HEAD` i trenutni branch pokazuju, npr. `git reset HEAD~` postavlja `HEAD` i trenutni branch na parent commit. Dolazi s nekoliko opcija:
* `git reset --soft HEAD~` stare verzije fileova ostaju u stagingu i working directoriju.
* `git reset --mixed HEAD~` (default) stare verzije fileova ostaju u working directoriju.
* `git reset --hard HEAD~` sve promjene se nepovratno gube.

`git reset <file>` oblik neće mijenjati `HEAD`, nego će samo primjeniti promjene na zadanim fileovima.
* `git reset HEAD file.txt` (`HEAD` ne treba pisati) briše staged promjene iz filea (tj. kopira verziju filea iz `HEAD`a i stavlja ga u staging)
* `git reset HEAD~1 file.txt` stavlja verziju iz parent commita u staging.

## Checkout

`git checkout <branch>` pomjera na što `HEAD` pokazuje, ali za razliku od `reset`, treuntni branch ostaje gdje je. Pritom i promjene u working directoriju ostaju sačuvane.

`git checkout <file>` ne pomjera `HEAD`, ali stavlja verziju filea iz `HEAD`a u staging i working directory, efektivno brišući necommitane promjene (kao `git reset --hard`).

## Rewriting history

Ako si zaboravio dodati file u commit (a nisi pushao):
Dodaj ga s `git add file.txt`, pa pozovi `git commit --ammend` koji će dodati sve promjene iz staginga u prethodni commit.

Ako si napravio krivi commit, a nisi pushao:
Napravi `git reset HEAD~` i commitane promjene će biti u stagingu.

Ako si napravio 3 commita koja želi squashati:
Napravi `git reset --soft HEAD~2` da vratiš `HEAD` za dva commita unazad. U stagingu su ti najnovije verzije fileova koje možeš sve ubaciti u jedan `git commit`.

Ako si commitao u krivi branch, npr `master`:
Pozicioniraj se u ispravni branch s `git checkout correct-branch`. Dodaj mu taj krivi commit s `git cherry-pick master`. Prebaci se u master branch i izbriši commit s `git reset HEAD~ --hard`.

## Config

Konfiguracija gita nalazi se na 3 mjesta:
* `/etc/gitconfig` za cijeli system
* `~/.gitconfig` za usera
* `.git/config` unutar projekta

## .gitignore

Patterni u `.gitignore` fileu vrijede za sve fileove, u kojem god direktoriju se nalazili. Možeš koristi globbing (`*`, `**`, `[]`) kao i u bashu.
* `/tmp/readme.txt` za specificirati točan file.
* `logs/` za ignorirati sve foldere tog imena.
* `!lib.a` neće ignorirati `lib.a`, čak i ako bi trebao biti ignoriran.

## Internals

Git drži sve verzije fileova, directorija i commitova u `.git/objects`. Svaki objekt je identificiran hashem od 40 znakova, a može biti: blob (sadržaj filea), tree (directory s imenima objekata i njihovim hashevima), i commit (sadrži commitan tree i svoje parente commitove).

Kada dodaješ file u staging area, git radi checksum (SHA-1) svakog filea i sprema njihove blobove u `.git` repozitorij. Kada napraviš commit, git radi checksum svakog poddirektorija, sprema ih kao tree objekte u `.git` repozitorij, te stvara novi commit objekt s metadatom i pointerom na root directory projekta. Na taj način može rekreirati cijeli working directory iz commita.

Svaki sljedeći commit će imati pointer na svoj parent commit, tj. commit koji je došao prije njega.

`HEAD` označava posljednji napravljen commit u trenutnom branchu. Prijašnje commitove moguće je referencirati pomoću `HEAD~3`. Ime trenutnog brancha drži se u `.git/HEAD`.

Branchevi se drže u `.git/refs/heads`. Svaki branch ima svoj file koji sadrži samo pokazivač na posljednji commit u tom branchu.


# Literatura

* https://git-scm.com/book/en/v2
