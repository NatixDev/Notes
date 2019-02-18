1. Create a new branch:
git checkout -b type/name-of-the-branch
The different types you can use are: feature, fix, docs, refactor.

As an example, you could create a new branch whose name is feature/add-ratings-movie or fix/scrolling-bug.

2. Do your changes on your newly created branch. Once you're finished, track and commit them:
git add path/to/your/file.vue
git commit -m "Add a new ratings section to the detailed movie"

Please be descriptive on your commit. No "Updates", "Some work", "Changes", etc.

3. Push your changes to the fork
git push origin type/name-of-the-branch

4. Go on GitHub, and create a Pull Request (PR). The PR should be against the master branch of the main movies repository.

5. Submit the pull request from your branch to the movies repo master branch.

6. In the body of the PR, include more details if necessary. You may want to precise if you introduced breaking changes for example.


Tu peux créer des branches. La raison pour laquelle tu crées des branches est parce que tu ne veux pas mettre du code qui ne marche pas sur master. 
Pour créer une branche tu utilises git checkout -b. Pour changer de branche, tu utilises git checkout.
Par exemple: git checkout -b feature/add-activity
Ensuite tu travailles sur cette branche, tu fais tes commits tranquille.
git add index.js
git commit -m "Add middleware"
Etc, etc.
Une fois que tu as fini de travailler sur cette branche, tu vas pusher ta branche sur le repo d'origine.
Parce que tu pourras remarquer que pour travailler, tu as du faire un clone du repo sur GitLab. Du coup ce qu'il se passe derriére, quand tu clones ce repo, c'est que ton repo local a ce qu'on appelle un remote qui s'appelle origin.

Du coup pour pusher tu fais: git push origin feature/add-activity. éa va prendre ta branche locale feature/add-activity et la mettre sur le repo origin (celui sur GitLab).
Une fois que ce sera sur GitLab. Il te proposera automatiquement en haut de faire une Pull Request parce qu'il va voir que tu as des commits en plus que la branche master.
Cette PR c'est simplement, prendre le code sur ta branche et le fusionner avec la branche master. Du coup tu ouvres cette PR. N'importe qui ayant accés au repo peut donc voir tes changements, faire des commentaires, etc.
Si tout est ok, tu fais un merge, une fusion quoi.
Et là tu vas me dire.
Oui, mais ça se passe comment si on bosse à deux dessus?
Ben, en fait, il faut tout le temps que tu sois à jour de la branche dans laquelle tu veux merger ton code, sinon tu peux avoir des conflits. Mais pas de soucis sur ça, tu ne pourras jamais merger une branche sans avoir résolu tes conflits.

Exemple: j'ajoute la feature "reset password". Toi en méme temps tu travailles sur le Routing.

git checkout -b feature/reset-password
// je travaille, 1h passe
git add App.js RequestResetPassword.js
git commit -m "Add request reset password form"
// je re-travaille, 2h passent
git add App.js ResetPassword.js
git commit -m "Add reset password form"
La, j'ai fini de faire le resetPassword, je te dis: c'est bon c'est fini!
Du coup, je vais pusher ma branche: 

git push origin feature/reset-password
J'ouvre la Pull Request, et la, Merde. Je vois qu'il y a des conflits sur le fichiers App.js  (puisque c'est l'un des fichiers principaux, éa arrivera probablement)
Et oui parce qu'en attendant que je travaille, toi tu as fais éa:

git checkout -b feature/add-routing
// blablabla, travail
git add App.js routes.js PrivateRoute.js
git commit -m "Add routes"
// travail terminé, tu push et ouvres une PR
git push origin feature/add-routing
Tu décides de mettre (logiquement) le code sur master, du coups, tu fais ta PR et tu la merges dans master.
Pas de soucis, personne n'a pushé de code en attendant.
Revenons à mon cas.
Pendant que je travaillais, tu as mis du code sur master, j'ai modifié App.js et tu as modifé App.js.
Du coup, je vais avoir un conflit.
Alors localement, je vais mettre à jour ma branche master local:

// On se replace sur master
git checkout master
// On récupére le code
git pull origin master


Et je vais mettre tes changements dans ma branche:

git checkout feature/reset-password
git merge master
// La il va me sortir un truc du style Attention aux conflits, etc.


Je vais direct dans le fichier, je résoud les conflits et je m'assure que tout marche bien.

git commit -m "Resolve conflicts"
git push origin feature/reset-password


Et si je retourne sur GitLab, magie, il me dit que je peux merger sans problème (à moins que tu ais re-pushé du code en attendant sur master en conflit avec le mien ;))

Permet de dire à Git de se souvenir qu'un « git push » de notre branche « experimental » envoie les changements à la branche « experimental » du dépôt distant. Le prochain push pourra donc se faire simplement avec : git push :

```git push --set-upstream origin example ```

Permet de changer de repo :

```git remote set-url origin https://github.com/user/repo2.git```

