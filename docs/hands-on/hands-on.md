# Mise en situation

Ce petit exemple de mise en pratique permettra de lister et d'utiliser les commandes git les plus courantes.

## Prérequis

L'objectif étant de pratiquer l'utilisation de git, on supposera que les étapes suivantes sont des acquis : 

- Avoir installé git sur son ordinateur.

- Avoir un compte GitHub.

- Avoir les autorisations pour pusher vers un repository GitHub (autrement dit, le PC connaît vos identifiants).

## Situation

Vous venez de rejoindre une équipe de data science dans une entreprise qui travaille sur la conduite autonome. Ils ont développé une application tout-en-un intégrant des modèles de machine learning qui tourneront à bord de leurs véhicules.

A l'heure actuelle, leur base de code, contenue dans le dossier `mock-project` , comporte une seule feature fonctionnelle `movement detection`. Le package `core` contient du code qui peut servir à toutes les features.

L'objectif de votre session de travail est d'ajouter une toute nouvelle feature `sign detection`, et de la proposer à vos (fictifs) collègues pour relire votre code.

Le *git flow* en vigueur dans l'équipe est le suivant :

- La branche `project-main` reflète ce qui est en production. L'appplication étant encore en développement, rien n'y a encore été mergé.

- La branche `project-develop` est la branche de travail de l'équipe. C'est là que sont mergées les branches de feature lorsqu'elles ont été validées par l'équipe.

- Lorsqu'on travaille sur une nouvelle feature, il faut créer une branche `project-feature/$FEATURE_NAME$` à partir de `project-develop`.

- Avant de merger une branche de feature vers `project-develop`, il faut squasher ses commits en un seul commit respectant la convention : 
  
  ```git
  $FEATURE_NAME$:
      - $SUB_FEATURE1$
      - $SUB_FEATURE2$
      - ...
  ```
  
  où la liste de "sub-features" doit décrire ce qui a été fait dans le commit.

- Lorsque la branche de feature est "propre", une pull request doit être faite vers `project-develop`.

## Mise en place du projet

Afin de pouvoir travailler sur le projet, il faut le récupérer sur GitHub [à l'addresse du présent repository](https://github.com/SnowHawkeye/git-basics).

### Créer un fork du projet

En entreprise, vous auriez été ajouté en tant que collaborateur du projet. Ici, comme ce n'est pas le cas, vous ne pourrez pas pusher de code directement sur le repository.

Pour contourner ce problème, il suffit de créer un **fork** du projet, c'est-à-dire un miroir du repository qui vous sera propre. Pour cela, il suffit d'appuyer sur le bouton "Fork" en haut à droite de la page.

Naviguez maintenant vers votre version du repository : vous aurez maintenant tous les accès nécessaires.

### Cloner le repository en local

Appuyez sur "Code" et copiez l'URL du projet. 

<img src="figures/fig-clone.png" title="" alt="" data-align="center">

Sur votre ordinateur, naviguez vers le dossier où vous voulez cloner le repository et ouvrez un terminal. En ligne de commande, vous pouvez utiliser : 

```bash
cd $PATH_TO_FOLDER
```

Puis importez le projet avec : 

```git
git clone $REPOSITORY_URL
```

Vous avez maintenant le dossier du repository dans vos fichiers locaux. N'oubliez pas d'y naviguer en ligne de commande avec `cd`.

## Travail sur le projet

### Vérifier les branches

Maintenant que vous avez récupéré le projet, un bon réflexe avant de commencer à travailler est de vérifier que vous êtes bien à jour avec la remote.

> Ici, il y a peu de risque puisque vous venez de télécharger le projet, mais en d'autres circonstances il est prudent de vérifier qu'on n'a pas oublier de se mettre à jour avec le travail de ses collègues.

Pour lister les branches que vous avez en local, utilisez :

```git
git branch
```

Vous verrez alors listées les branches que vous avez sur votre machine, en particulier celles qui nous intéressent : `project-main` et `project-develop`. Si elles n'apparaissent pas, vous pouvez utiliser la commande suivante pour aller les chercher en remote :

```git
git fetch
```

### Manipuler les branches

Maintenant que vous êtes prêt et à jour, vous aimeriez commencer à travailler sur la nouvelle feature. Vous allez donc créer une nouvelle branche pour coder. Le problème étant : vous ne savez pas encore quel nom a été convenu pour cette nouvelle feature, et vous ne pouvez donc pas encore créer une branche qui respecte la convention `project-feature/$FEATURE_NAME$`.

Toutefois, cela ne vous bloquera pas : il vous suffit de créer une branche temporaire que vous renommerez plus tard. Pour créer la branche, commencez par vous placer sur `project-develop` :

```git
git checkout project-develop
```

> Si vous partez de la mauvaise branche pour coder votre feature, vous risquez de ne pas avoir tout le code dont vous avez besoin, ou de générer des conflits en ramenant le votre plus tard.

Vous pouvez également regarder l'historique de la branche avec : 

```git
git log
```

> Utilisez les flèches pour monter / descendre dans l'historique. Si vous êtes "bloqués" dans le log, appuyez sur `q` pour quitter, ou sur `ctrl+C` qui est un raccourci plus général pour interrompre une commande.

Vous pouvez alors créer une nouvelle branche :

```git
git checkout -b tmp/sign
```

Si le nom ne vous convient pas, vous pouvez le changer avec :

```git
git branch -m $NEW_BRANCH_NAME
```

Vous êtes maintenant sur une nouvelle branche, qui n'existe qu'en local (puisque vous n'avez rien pushé) et sur laquelle vous pouvez faire tous les tests qui vous semblent utiles. Créez quelques fichiers, modifiez-en d'autres, faites-vous plaisir !

Après quelques essais, vous pouvez lister les fichiers qui ont été changés avec :

```git
git status
```

*A priori*, tous vos changements apparaissent en rouge est rien n'est `staged for commit` : vous n'avez aucune intention de garder ces changements.

Après votre réunion quotidienne, vous avez une idée plus claire de ce qui est attendu de vous : la feature s'appelle `sign detection`, et vous avez des instructions claires quant à ce que vous devez faire.

Vos tests n'ayant rien donné d'intéressant, vous décidez simplement de repartir de zéro en supprimant la branche et en en recréeant une nouvelle. Pour faire bonne mesure, vous décidez aussi d'annuler tous vos changements : 

```git
git reset --hard
git checkout project-develop
git branch -D tmp/sign
git checkout -b project-feature/sign-detection
```

Quelques notes sur les commandes ci-dessus : 

- `git reset --hard` **détruit tous vos changements** et vous remet dans l'état du dernier commit. A utiliser uniquement quand vous ne voulez rien garder. Ici, puisque la branche sera supprimée, sa seule utilité est de vous éviter d'avoir à commiter des changements que vous allez de toute façon perdre. Il existe des reset plus "soft" qu'on ne détaille pas ici.

- Pour supprimer une branche avec `git branch -D`, il faut se trouver sur une autre branche. C'est pourquoi on commence par retourner sur `project-develop`. On note que la branche n'est supprimée que localement !

- Une alternative ici aurait été de simplement renommer la branche après avoir utilisé `git reset --hard`.

- Vous remarquerez que la nouvelle branche créée respecte les conventions de nommage de l'équipe !

### Création d'une nouvelle feature

#### Ajout de modifications

Fort de ce travail préliminaire, vous êtes maintenant prêt à créer une nouvelle feature. Pour cela, les étapes suivantes sont nécessaires : 

- Créez un nouveau dossier `sign-detection` dans lequel vous placerez un fichier `SignDetectionCode.txt` où vous écrirez quelques lignes (peu importe le contenu, avoir des changements permettra d'avoir un commit plus "visuel" lorsqu'on fera une pull request par la suite).

- Ouvrez le fichier `CoreCode.txt` du package `core` et ajoutez-y une ligne.

- Du travail préparatoire avait été effectué avant votre arrivée, mais il est à un endroit de la codebase qui n'est plus pertinent. Déplacez le fichier `SignPrework.txt` vers le package `sign-detection`.

- Imaginons que votre code génère des fichiers annexes à l'exécution. Ajoutez un fichier `GeneratedCode.txt` n'importe où dans le repository.

> Tous ces "faux changements" n'ont d'objectif que de montrer les différents types de changement qui peuvent apparaître dans un commit. Certains d'entre eux seront utilisés pour illustrer d'autres points par la suite.

Si vous utilisez `git status`, vous devriez voir tous les changements que vous avez faits : le nouveau dossier, le fichier modifié, le fichier déplacé, et le nouveau fichier.

#### Les fichiers à ignorer

Vous aimeriez committer vos changements, mais vous ne voulez pas committer le fichier `GeneratedCode.txt`. En effet, ce type de fichier généré "automatiquement" est de toute façon regénéré à l'utilisation - en d'autres termes, il est inutile et générateur potentiel de conflits.

Une première solution consisterait à le supprimer, ou à ne pas l'ajouter aux fichiers à committer. Cela représente cependant une perte de temps à chaque fois que vous voulez committer quelque chose.

La bonne solution ici est d'ajouter le fichier au fichier `.gitignore` : c'est un fichier utilitaire qui indique à git tous les fichiers dont les changements sont à ignorer. Il est à la racine du repository.

> C'est un fichier caché. Si vous ne le voyez pas dans l'explorateur de fichiers, il faut :
> 
> - Sous Windows, aller dans l'onglet "Affichage" et cocher l'option correspondante.
> 
> - Sous Mac, utiliser le raccourci ⇧ + ⌘ + •

Il vous suffit de l'ouvrir et d'y ajouter une ligne avec le nom du fichier. Les fichiers `.gitignore` peuvent aussi accepter :

- Des noms de dossier (e.g. `ignore-me/`), tous les fichiers du dossier sont alors ignorés.

- Des extensions de fichier (e.g. `.ignoreme`), tous les fichiers ayant cette extension sont alors ignorés.

Si vous enregistrez le `.gitignore` et que vous utilisez à nouveau `git status`, vous devriez voir que le fichier "généré" n'est plus considéré comme ayant reçu des changements. En revanche, le `.gitignore` a maintenant été modifié.

> Le `.gitignore` étant un fichier "commun", il est déconseillé de le modifier unilatéralement comme nous venons de le faire (pour éviter les conflits). Dans la réalité, on préfère utiliser des `.gitignore` pré-faits. Voir par exemple [ce lien](https://www.toptal.com/developers/gitignore)).

#### Committer ses changements

Maintenant que notre liste de changements est "propre", nous pouvons maintenant les  committer, c'est-à-dire les enregistrer dans l'historique de notre branche.

Une pratique courante lorsqu'on veut faire relire son code à ses collègues est de créer plusieurs "petits commits" contenant peu de changements, mais explicites et faciles à digérer en code review. C'est une fois le code validé que l'on squashera ces commits en un seul "commit de feature", comme le veut notre convention d'équipe. 

> Ce type de manoeuvre est pratique lorsqu'on déplace / supprime beaucoup de fichiers, ce qui donne des changelists énormes et rend illisible les pull requests.

Pour illustrer ce cas de figure, nous allons donc créer deux commits à partir des changements qu'on a faits.

Le changement que nous avons fait qui est "à part" des autres est celui du `.gitignore`. Nous allons donc le committer séparément : 

```git
git add .gitignore
git commit -m "Update .gitignore"
```

Si vous utilisez à nouveau `git log`, vous verrez le nouveau commit apparaître dans l'historique. Avec `git status` vous devriez toujours voir vos changements pour les autres fichiers, mais plus pour `.gitignore`.

Nous allons maintenant créer un deuxième commit avec le reste des changements : 

```git
git add .
git commit -m "Sign detection feature"
```

Dans le code ci-dessus :

- `git add .` permet d'ajouter tous les changements que vous voyez dans `git status` au commit.

- Le message de commit ne respecte pas encore les conventions de l'équipe, mais vous aurez l'occasion de le changer par la suite quand vous squasherez les commits !

#### Uploader ses changements

Vous avez committé vos changements **localement**, il est maintenant temps de les uploader vers le repository en remote. Il est important de noter qu'une fois que les changements sont en ligne, les modifications telles que renommer une branche ou réécrire son l'historique deviennent dangereuses.

Pour envoyer vos changements en ligne, utilisez : 

```git
git push
```

Vous devriez avoir un message d'erreur : vous essayez de pusher vers une branche qui n'existe pas en remote ! La solution est simple : copiez-collez la commande proposée par le message d'erreur, qui devrait ressembler à quelque chose du genre : 

```git
git push --set-upstream origin project-feature/sign-detection
```

> `origin` désigne ici la remote, c'est-à-dire le repository vers lequel on pushe. Nous ne détaillerons pas ici le travail avec plusieurs remotes, mais cela ne change pas vraiment les commandes : il faut juste faire attention lorsqu'on push ou pull à choisir la bonne remote.

Maintenant que les changements sont en ligne, vous pouvez aller sur la page web de votre projet GitHub, et créer une pull request.

- Si votre push est récent, un bouton se présentera directement.

- Sinon, vous pouvez cliquer sur "branches", trouver votre branche dans la liste et cliquer sur "New pull request".

![](figures/fig-pr-location.png)

Lorsque la PR est ouverte, portez attention à :

- La branche vers laquelle vous voulez merger (pour nous, ce serait `project-develop`).

- Le titre et commentaire de votre PR : c'est par eux que vous communiquez avec votre équipe ce que fait votre PR - soignez-les !

- La liste des commits de la PR (vous devriez en voir 2 pour l'instant) et de vos changements. C'est une bonne façon de vérifier que vous faites une PR vers la bonne branche, et que vous avez committé ce que vous vouliez committer.

Lorsque vous êtes certain que tout est au propre, vous pouvez appuyer sur "Create pull request".

![](figures/fig-pr.png)

Il est bon de noter que si vous faites de nouveaux commits sur votre branche, ils seront automatiquement ajoutés à la PR tant qu'elle n'aura pas été mergée.

### Manipulation de l'historique

Imaginons maintenant que votre PR a été relue par vos collègues et que vous avez eu leur accord pour la merger. Pour respecter les conventions de votre équipe, vous devez encore :

- Squasher vos commits en un seul commit.

- Changer le message de commit pour respecter les conventions de l'équipe.

Pour cela, nous allons utiliser un **rebase**. Fondamentalement, cela signifie que l'on change l'origine de notre branche. Mais en l'occurence, on s'en sert pour réorganiser les commits.

En particulier, on va utiliser un rebase "interactif" (c'est le `-i` dans la commande ci-dessous), c'est-à-dire que git va nous "accompagner".

La première chose à faire est de compter le nombre de commits à rebase, c'est-à-dire le nombre de commits que l'on va modifier dans notre historique. **Il faut faire très attention à bien compter** : si on va trop loin, on va changer les commits qui étaient déjà sur `project-develop` et donc créer des conflits d'historique.

Si vous avez suivi les instructions jusqu'ici, vous devriez avoir deux commits à squasher, et l'instruction à utiliser est donc :

```git
git rebase -i HEAD~2
```

- `HEAD` correspond au "pointeur" qui indique à git la version avec laquelle vous travaillez. Ici, on retourne "2 commits en arrière de `HEAD`" pour pouvoir les squasher.

- Evidemment, vous pouvez remplacer le `2` par le nombre souhaité.

Lorsque vous entrez la commande, vous verrez alors apparaître un éditeur appelé Vim dans votre terminal. Vous y verrez la liste des commits que vous voulez rebase (du plus ancien au plus récent), ainsi qu'une aide sur ce que vous pouvez faire avec. En particulier : 

- `pick` garde le commit tel quel

- `reword` permet de changer son message

- `fixup` "mélange" le commit au commit précédent et abandonne son message

> Vim est un peu déroutant à utiliser. Lorsqu'il s'ouvre, vous ne pouvez initialement pas éditer quoi que ce soit. Ci-après une petite liste de raccourcis utiles :
> 
> - Si vous appuyez sur `i`, vous passez en mode "insertion" et vous pouvez éditer le fichier. Appuyez sur `esc` pour sortir de ce mode.
> 
> - Lorsque vous êtes hors du mode insertion, vous pouvez appuyer sur `:` pour entrer une commande. Les deux commandes qui vous sont utiles sont alors : 
>   
>   - `wq` pour "write and quit", sauvegarde vos changement et quitte l'éditeur
>   
>   - `qa!` permet de quitter sans sauvegarder

On veut changer le fichier pour qu'il ressemble à : 

```
reword [commit 1 id] [commit 1 message]
fixup [commit 2 id] [commit 2 message]
```

Lorsque vous validez ces changements, vous aurez une nouvelle fenêtre où vous pourrez changer le message de commit. Remplacez le message actuel par :

```
signs-detection:
    - updated core
    - added signs detection feature
```

Validez le tout, et vous devriez avoir un message vous disant que votre rebase a été un succès. En utilisant `git log`, vous devriez voir le nouveau commit (et l'absence de ceux que vous avez remplacés !).

### Dernière mise à jour de la PR et merge

Il n'y a maintenant plus qu'à pusher à nouveau ces changements. Cependant, comme vous avez changé l'historique de la branche, un simple `push` ne suffit pas, il faut utiliser un "force push" :

```git
git push -f origin project-feature/sign-detection
```

> Comme auparavant, on peut se contenter d'écrire `git push -f` sans préciser la remote et la branche. Toutefois, en raison du "risque" induit par la réécriture de l'historique, il est bon d'être précautionneux lorsqu'on force push. Autrement dit, revérifiez bien deux fois que vous pushez vers la bonne branche ! 

Ceci fait, vous aurez bien mis à jour votre branche, et vous verrez que votre PR est à jour si vous rechargez la page. Après une dernière vérification, mergez-la dans `project-develop` depuis GitHub.

De retour dans votre terminal, vous pouvez retourner sur la branche `project-develop` et utiliser `git pull` pour la mettre à jour en local.

> Vous l'aurez sans doute remarqué, l'utilisation de rebase est un peu fastidieuse en ligne de commande. Certaines IDE (PyCharm, VSCode) peuvent vous simplifier la tâche en vous proposant une interface graphique.

### Résumé de la partie

Félicitations, votre nouvelle feature est maintenant ajoutée à la branche de développement ! Ci-après un petit résumé des étapes franchies pour y parvenir :

- Vous avez créé une nouvelle branche pour travailler sur la feature.

- Vous avez apporté les changements nécessaires.

- Vous avez scindé vos changements en deux commits et ouvert une PR pour votre équipe.

- Avec leur approbation (symbolique), vous avez regroupé vos commits en un seul, et mergé cet unique commit dans la branche de développement.

Ce que vous avez vu jusqu'ici est suffisant pour la grande majorité de vos journées de développement. Dans la suite, nous verrons un dernier scénario auquel il faut savoir faire face quand on utilise git : la gestion des conflits.

## Gestion des conflits

Les conflits ont lieu en cas de rebase ou de merge, lorsque git n'arrive pas à concilier les changements tout seul. Autrement dit, ils se produisent lorsqu'on essaie de mettre ensemble des changements sur deux branches affectant le même fichier.

Plaçons-nous dans le scénario suivant : vous travaillez avec votre collègue sur une feature qui nécessite des changements dans le fichier `CoreCode.txt`. Vous vous répartissez le travail : votre collègue mettra à jour `CoreCode.txt`, et vous avancerez sur la feature elle-même jusqu'à être bloqué. L'heure venue, pour récupérer ses changements, vous rebaserez votre branche sur la sienne.

Le problème est le suivant : par erreur ou par nécessité, vous avez aussi changé le fichier `CoreCode.txt`. Il y aura donc un conflit à gérer. 

> Ne vous en faites pas si la mise en situation est un peu obscure, c'est surtout l'application qui sera importante. En pratique, ce genre de scénario peut avoir lieu si vous travaillez à deux sur la même feature, et que l'un essaie de prendre de l'avance pour aider l'autre.

Pour simplifier les choses, deux branches sont déjà créées : `conflict-source` et `conflict-target`. L'objectif va être de rebase `conflict-source` sur `conflict-target`.

Commencez par vous placer sur `conflict-source` :

```git
git checkout conflict-source
```

> N'hésitez pas à utiliser `git log` pour voir à quoi ressemble l'historique de cette branche !

Et rebasez vous sur `conflict-target` :

```git
git rebase conflict-target
```

Un message d'erreur s'affichera alors dans le terminal, vous expliquant qu'il y a un conflit dans le fichier `CoreCode.txt` et que le rebase ne peut pas s'effectuer tant que vous n'aurez pas résolu le conflit.

Pour résoudre le conflit, ouvrez `CoreCode.txt`. Vous devriez voir qu'une partie du texte ressemble maintenant à ceci :

```
<<<<<<< HEAD
Le code conflictuel existant sur la branche cible.
=======
Le code conflictuel existant dans la branche à rebase
>>>>>>> [Code du commit] (Nom du commit de la branche à rebase)
```

C'est la façon dont git vous indique où sont les conflits. C'est donc à vous de comparer les deux versions conflictuelles et de décider comment les allier. 

> Parfois, la résolution du conflit est très simple (il suffit de mettre les codes l'un à la suite de l'autre), mais parfois vous devrez vous concerter avec votre collègue pour vous entendre sur la façon de garder vos deux travaux sans perdre de fonctionnalité.

Résolvez le conflit de la façon qui vous semble appropriée (n'oubliez pas d'enlever les "décorations" que git avait ajoutées !) et enregistrez le fichier.

Pour pouvoir continuer le rebase, vous devez alors committer vos nouveaux changements (la résolution du conflit) et utiliser la commande :

```git
git rebase --continue
```

Si tous les conflits sont réglés, vous aurez un message vous indiquant que le rebase s'est effectué avec succès. En utilisant `git log`, vous verrez que l'origine de la branche source a bien été modifiée, et vous verrez le commit que vous avez ajouté pendant le rebase.

> "Mais le commit de rebase fait tâche dans l'historique !"
> 
> Une remarque pertinente ! On peut simplement squasher le "commit de rebase" dans le commit qu'on a apporté sur la branche pour clarifier l'historique. Encore une fois, attention à ne pas créer de nouveaux problèmes en réécrivant l'historique.

## Un mot sur les stashs

On peut voir une *stash* comme un ensemble de changements qu'on décide de mettre dans sa poche pour les utiliser plus tard. Si vous voulez tester leur utilisation, faites des changements dans un fichier, et exécutez la séquence suivante : 

```git
git status
git stash
git status
git stash pop
git status
```

Vous verrez que `git status` ne montre plus vos changements après que vous les ayez stashés, mais qu'ils réapparaissent après avoir utilisé `git stash pop`. 

Quelques notes supplémentaires : 

- Vous pouvez mettre de côté vos changements, changer de branche et les `pop` sur cette nouvelle branche.

- `pop` élimine la stash. Autrement dit, `pop` élimine la liste de vos changements de la liste de vos stashs.

- Il est possible de manipuler plusieurs stashs, de les lister, de les renommer, etc. Je vous redirige vers la documentation si vous voulez tester ces cas d'usage en profondeur.

## Un mot sur le cherry-picking

Le *cherry pick* correspond à prendre un commit en particulier et à le ramener sur la branche courante. Il se fait simplement avec :

```git
git cherry-pick $COMMIT_ID
```

Sachez toutefois que, comme avec toute opération de ce genre, il est possible que vous ayiez des conflits à gérer.

## Conclusion

Cela conclut cette mise en pratique de l'utilisation de git.

Git est un outil très puissant, et il existe beaucoup plus de commandes que celles qui sont présentées ici. Ces dernières permettent toutefois de travailler confortablement avec git au quotidien. Si vous maîtrisez tout ce qui est au-dessus, vous ne devriez pas avoir de mal à comprendre et utiliser d'autres commandes.

Si vous avez besoin de quelque chose qui n'a pas été exposé dans cette mise en pratique, n'hésitez pas à consulter la [documentation](https://git-scm.com/doc), ou à poser une question à votre moteur de recherche favori !
