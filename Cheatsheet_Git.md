# Cheatsheet Git

## Configuration
| Commande | Effet |
|---|---|
| `git config --global user.name "Nom"` | nom global |
| `git config --global user.email "email"` | e-mail global |
| `git config --global core.editor code` | éditeur par défaut |
| `git config --list` | lister la config |
| `git config --global init.defaultBranch main` | branche par défaut |
| `git config --global alias.st status` | créer un alias |
| `git config --global credential.helper store` | mémoriser les identifiants |
| `git config --global core.autocrlf input` | gestion des fins de ligne (Linux/macOS) |

## Démarrer un projet
| Commande | Effet |
|---|---|
| `git init` | initialiser un dépôt |
| `git clone URL` | cloner un dépôt distant |
| `git clone --branch dev URL` | cloner une branche précise |
| `git clone --depth 1 URL` | clone superficiel (rapide) |

## Cycle de vie des fichiers
| Commande | Effet |
|---|---|
| `git status` | état du dépôt |
| `git status -sb` | version compacte |
| `git add fichier` | indexer un fichier |
| `git add .` | indexer tout |
| `git add -A` | indexer tout (suppressions incluses) |
| `git add -p` | indexation interactive (morceau par morceau) |
| `git commit -m "message"` | valider |
| `git commit -am "message"` | add + commit (fichiers déjà suivis) |
| `git commit --amend -m "nouveau"` | modifier le dernier commit |
| `git rm fichier` | supprimer et indexer |
| `git mv fichier dest` | déplacer/renommer et indexer |
| `git restore fichier` | annuler les modifs non indexées |
| `git restore --staged fichier` | désindexer (unstage) |
| `git reset HEAD fichier` | désindexer (ancien style) |
| `git checkout -- fichier` | restaurer depuis l'index (ancien style) |

## Comparer
| Commande | Effet |
|---|---|
| `git diff` | différences non indexées |
| `git diff --staged` | différences indexées |
| `git diff HEAD` | différences par rapport au dernier commit |
| `git diff branche1..branche2` | comparer deux branches |
| `git show commit` | contenu d'un commit |
| `git show --stat commit` | fichiers modifiés par un commit |
| `git diff --stat` | résumé chiffré |

## Historique
| Commande | Effet |
|---|---|
| `git log` | historique |
| `git log --oneline` | version courte |
| `git log --graph --all --oneline` | graphe des branches |
| `git log -p` | avec les diffs |
| `git log --author="nom"` | filtrer par auteur |
| `git log --grep="motif"` | filtrer par message |
| `git log --follow -- fichier` | historique d'un fichier |
| `git blame fichier` | qui a modifié chaque ligne |
| `git reflog` | journal de toutes les actions (sauveur !) |

## Branches
| Commande | Effet |
|---|---|
| `git branch` | lister les branches locales |
| `git branch -r` | branches distantes |
| `git branch -a` | toutes |
| `git branch nom` | créer une branche |
| `git checkout nom` / `git switch nom` | changer de branche |
| `git switch -c nom` | créer + basculer |
| `git checkout -b nom` | créer + basculer (ancien style) |
| `git branch -d nom` / `git branch -D nom` | supprimer (dangereux avec -D) |
| `git branch -m ancien nouveau` | renommer |
| `git merge nom` | fusionner une branche dans la branche courante |
| `git merge --abort` | annuler une fusion conflictuelle |
| `git rebase nom` | rejouer les commits de la branche courante sur nom |
| `git rebase -i HEAD~3` | rebase interactif (squash, reword...) |
| `git cherry-pick commit` | appliquer un commit sur la branche courante |

### Stratégies de merge
- **merge** : crée un commit de fusion, conserve l'historique.
- **rebase** : historique linéaire, réécrit les commits.
- **squash** (`git merge --squash`) : un seul commit.

## Gestion de conflits
1. `git status` : fichiers en conflit listés.
2. Éditer les fichiers : marqueurs `<<<<<<<`, `=======`, `>>>>>>>`.
3. `git add fichier` puis `git commit`.
Alternative visuelle : `git mergetool`.

## Distant (remote)
| Commande | Effet |
|---|---|
| `git remote -v` | lister les remotes |
| `git remote add origin URL` | ajouter un remote |
| `git remote set-url origin URL` | changer l'URL |
| `git remote remove origin` | supprimer |
| `git fetch origin` | récupérer les infos du distant |
| `git pull` | fetch + merge |
| `git pull --rebase` | fetch + rebase (historique propre) |
| `git push` | pousser la branche courante |
| `git push -u origin main` | pousser + associer la branche |
| `git push origin branche` | pousser une branche |
| `git push --all` | toutes les branches |
| `git push --tags` | pousser les tags |
| `git push origin --delete branche` | supprimer une branche distante |
| `git push --force` / `--force-with-lease` | forcer (prudence, préférer `--force-with-lease`) |
| `git push -f` | alias de `--force` |

## Tags
| Commande | Effet |
|---|---|
| `git tag` | lister |
| `git tag v1.0` | tag léger |
| `git tag -a v1.0 -m "message"` | tag annoté |
| `git tag -d v1.0` | supprimer localement |
| `git push origin v1.0` | pousser un tag |
| `git push origin --tags` | pousser tous les tags |
| `git checkout v1.0` | se positionner sur un tag |

## Annuler des modifications
| Situation | Commande |
|---|---|
| Modifs non indexées | `git restore fichier` |
| Fichiers indexés | `git restore --staged fichier` |
| Dernier commit (en gardant les modifs) | `git reset --soft HEAD~1` |
| Dernier commit (annuler + désindexer) | `git reset HEAD~1` |
| Tout annuler (destructif) | `git reset --hard HEAD` |
| Annuler un commit en gardant l'historique | `git revert commit` |
| Commiter par erreur sur la mauvaise branche | `git reset --soft HEAD~1` puis `git switch -c bonne_branche` |
| Récupérer un commit "perdu" | `git reflog` puis `git cherry-pick` |

## Stash
| Commande | Effet |
|---|---|
| `git stash` | mettre de côté les modifs |
| `git stash list` | lister |
| `git stash pop` | réappliquer + supprimer |
| `git stash apply` | réappliquer sans supprimer |
| `git stash push -m "message"` | stash avec message |
| `git stash push -u` | inclure les fichiers non suivis |
| `git stash drop stash@{0}` | supprimer |
| `git stash show -p stash@{0}` | voir le contenu |

## Recherche dans le dépôt
| Commande | Effet |
|---|---|
| `git grep motif` | chercher dans le contenu suivi |
| `git grep -n motif` | avec numéros de ligne |
| `git log -S "motif"` | commits qui modifient une quantité de motif |
| `git log -G "motif"` | commits où le motif apparaît |
| `git bisect start` | recherche binaire de la régression |
| `git bisect bad` / `git bisect good` | marquer |
| `git bisect reset` | quitter |

## Fichiers ignorés (.gitignore)
```gitignore
node_modules/
dist/
*.log
.env
!important.env
```
| Commande | Effet |
|---|---|
| `git check-ignore fichier` | vérifier si un fichier est ignoré |
| `git status --ignored` | montrer les fichiers ignorés |
| `git ls-files` | fichiers suivis |
| `git rm -r --cached dossier` | désindexer sans supprimer (après modif du .gitignore) |

## Exemples d'outils
| Outil | Usage |
|---|---|
| `gitk` | interface graphique historique |
| `git gui` | interface de commit |
| `git cola` / `gitg` | GUI |
| `gh` | CLI GitHub (PR, issues) |

## Bonnes pratiques
- Commits **petits et atomiques** : un commit = une idée.
- Messages de commit **explicites** (impératif : « Ajoute la fonction X »).
- Ne pas committer de **secrets** (.env, clés).
- **`git pull --rebase`** pour garder un historique propre.
- **Toujours tester avant de pousser.**
- Vérifier `git status` avant tout commit.
- Utiliser `.gitignore` dès la création du projet.
