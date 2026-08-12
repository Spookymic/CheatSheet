# Cheatsheet Shell

## Qu'est-ce qu'un shell ?
Interpréteur de commandes entre l'utilisateur et le système d'exploitation.
- **Bash** : le plus répandu sur Linux (par défaut sur la plupart des distributions).
- **Zsh** : plus moderne, thèmes, plugins (défaut macOS depuis Catalina).
- **sh** : le shell historique (POSIX).
- **Fish** : ergonomique, autocomplétion avancée.
- **PowerShell** : shell Windows.

## Structure d'une commande
```
commande [options] [arguments]
ls      [-la]      /etc
```

## Variables
```sh
NOM="valeur"        # affectation (sans espaces autour du =)
echo $NOM           # lecture
echo "${NOM}"       # lecture sûre (contexte)
export NOM          # exporter vers les sous-processus
unset NOM           # supprimer
```
Variables spéciales :
| Variable | Contenu |
|---|---|
| `$0` | nom du script / shell |
| `$1`, `$2`... | arguments positionnels |
| `$#` | nombre d'arguments |
| `$@` | tous les arguments |
| `$?` | code de sortie de la dernière commande |
| `$$` | PID du shell courant |
| `$HOME` | dossier personnel |
| `$PATH` | chemins de recherche des commandes |
| `$USER` | utilisateur courant |

## Chaînage et contrôle
| Syntaxe | Effet |
|---|---|
| `cmd1; cmd2` | exécuter l'un après l'autre |
| `cmd1 && cmd2` | cmd2 seulement si cmd1 réussit |
| `cmd1 \|\| cmd2` | cmd2 seulement si cmd1 échoue |
| `cmd1 \| cmd2` | pipe : sortie de cmd1 vers entrée de cmd2 |
| `( cmd )` | sous-shell |
| `{ cmd; }` | groupe (même shell) |
| `cmd &` | arrière-plan |
| `cmd1 $(cmd2)` | substitution de commande |
| `` cmd1 `cmd2` `` | substitution (ancien style) |

## Redirections
| Syntaxe | Effet |
|---|---|
| `cmd > fichier` | stdout vers fichier (écrase) |
| `cmd >> fichier` | stdout vers fichier (ajoute) |
| `cmd 2> fichier` | stderr vers fichier |
| `cmd 2>&1` | stderr vers stdout |
| `cmd > fichier 2>&1` | tout vers fichier |
| `cmd < fichier` | entrée depuis fichier |
| `cmd <<< "texte"` | here-string |
| `cmd << EOF ... EOF` | here-document |
| `cmd > /dev/null` | supprimer la sortie |
| `cmd \| tee fichier` | afficher ET écrire dans fichier |

## Scripts
```sh
#!/bin/bash          # shebang : interpréteur
# commentaire
set -e               # s'arrêter en cas d'erreur
set -u               # erreur si variable non définie
echo "Hello $1"
exit 0               # code de sortie (0 = succès)
```
| Commandes | Effet |
|---|---|
| `chmod +x script.sh` | rendre exécutable |
| `./script.sh` | lancer |
| `bash script.sh` | lancer via bash |
| `sh -n script.sh` | vérifier la syntaxe |
| `bash -x script.sh` | tracer l'exécution |

## Conditions et tests
```sh
if [ "$x" -eq 5 ]; then
    echo "égal"
elif [ -f "fichier.txt" ]; then
    echo "fichier existe"
else
    echo "sinon"
fi

[ condition ] && echo "vrai" || echo "faux"
```
Tests de fichiers : `-f` fichier, `-d` dossier, `-r` lisible, `-w` écriture, `-x` exécutable, `-e` existe, `-s` non vide.
Comparaisons numériques : `-eq -ne -gt -lt -ge -le`. Chaînes : `=`, `!=`, `-z` (vide), `-n` (non vide).

## Boucles
```sh
for i in 1 2 3; do echo $i; done
for f in *.txt; do echo "-> $f"; done
for ((i=0; i<10; i++)); do echo $i; done

while [ "$i" -lt 5 ]; do echo $i; ((i++)); done
until [ "$i" -ge 5 ]; do echo $i; ((i++)); done
```
| Mot-clé | Effet |
|---|---|
| `break` | sortir de la boucle |
| `continue` | passer à l'itération suivante |

## Fonctions
```sh
ma_fonction() {
    echo "Argument: $1"
    return 0
}
ma_fonction "hello"
```

## Expansion et globbing
| Syntaxe | Effet |
|---|---|
| `*` | n'importe quelle suite de caractères |
| `?` | un caractère |
| `[abc]` | un caractère parmi a, b, c |
| `[a-z]` | un caractère de a à z |
| `{a,b,c}` | expansion de liste |
| `{1..10}` | expansion numérique |
| `~` | dossier personnel |
| `~/dossier` | dossier sous home |
| `$(( 2 + 3 ))` | arithmétique |

## Quoting (guillemets)
| Syntaxe | Comportement |
|---|---|
| `"texte"` | interpolation des variables, pas de globbing |
| `'texte'` | littéral, rien n'est interprété |
| `\` | échappe un caractère |
| `` ` `` | substitution de commande |

## Fichiers de configuration

Chaque shell lit des fichiers différents selon qu'il est **interactif** ou **de connexion (login)**.

| Fichier | Rôle |
|---|---|
| `/etc/profile` | config globale, exécutée au login |
| `~/.profile` | config login POSIX (sh ; aussi bash si `.bash_profile` absent) |
| `~/.bash_profile` | config login Bash (contient souvent `source ~/.bashrc`) |
| `~/.bashrc` | config interactive Bash |
| `~/.zshrc` | config interactive Zsh |
| `~/.zshenv` | Zsh : chargé à chaque invocation (variables d'env) |
| `~/.bash_history` | historique Bash |

### Où mettre quoi ?
| Contenu | Fichier conseillé |
|---|---|
| Variables d'environnement exportées (`export`) | `.profile` / `.bash_profile` |
| Alias et fonctions | `.bashrc` / `.zshrc` |
| Invite de commande (PS1) | `.bashrc` / `.zshrc` |
| Configuration d'applications | `.bashrc` / `.zshrc` |

### Appliquer sans relancer
```sh
source ~/.bashrc     # Bash
source ~/.zshrc      # Zsh
```

### Bonnes pratiques de personnalisation
- Alias courts et mnémoniques : `alias ll='ls -la'`.
- Vérifier la syntaxe sans exécuter : `bash -n ~/.bashrc`.
- Tester sans config : `bash --noprofile --norc`.
- Toujours quoter les variables dans les fonctions : `"$1"`.
- Après une modification de `PATH`, vérifier avec `echo $PATH`.

## Gestion des travaux
| Commande | Effet |
|---|---|
| `jobs` | lister les taches |
| `Ctrl+Z` | suspendre la tache |
| `bg` | reprendre en arrière-plan |
| `fg` | reprendre au premier plan |
| `kill %1` | tuer la tache 1 |
| `nohup cmd &` | lancer insensible au logout |
| `disown` | détacher de la session |

## Historique
| Commande | Effet |
|---|---|
| `history` | lister l'historique |
| `!42` | relancer la commande 42 |
| `!!` | relancer la dernière commande |
| `!$` | dernier argument de la commande précédente |
| `Ctrl+R` | recherche interactive |
| `fc` | éditer la dernière commande |

## Bonnes pratiques
- Toujours entre guillemets les variables : `"$VAR"`.
- Préférer `[[ ]]` en Bash (plus robuste que `[ ]`).
- Utiliser `set -euo pipefail` au début des scripts.
- Vérifier `$?` après les commandes critiques.
- Ne pas analyser les sorties avec des espaces dans les noms de fichiers (utiliser `while read`).
- Commenter ses scripts, mais sans sur-commenter.
