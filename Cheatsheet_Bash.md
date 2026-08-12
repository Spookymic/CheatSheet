# Cheatsheet Bash

## Raccourcis de ligne de commande
| Raccourci | Effet |
|---|---|
| `Ctrl+A` / `Ctrl+E` | début / fin de ligne |
| `Ctrl+U` | effacer avant le curseur |
| `Ctrl+K` | effacer après le curseur |
| `Ctrl+W` | effacer le mot avant |
| `Ctrl+L` | effacer l'écran |
| `Ctrl+C` | interrompre |
| `Ctrl+Z` | suspendre |
| `Ctrl+R` | recherche dans l'historique |
| `Ctrl+←` / `Ctrl+→` | sauter d'un mot |
| `Alt+.` | dernier argument de la commande précédente |
| `Alt+B` / `Alt+F` | mot précédent / suivant |
| `Ctrl+T` | permuter deux caractères |
| `Ctrl+X Ctrl+E` | éditer la ligne dans un éditeur |

## Historique
| Commande | Effet |
|---|---|
| `history` | afficher l'historique |
| `history 20` | les 20 dernières |
| `!!` | rejouer la dernière |
| `!42` | rejouer la commande 42 |
| `!mot` | rejouer la dernière commençant par mot |
| `!$` | dernier argument de la dernière commande |
| `!*` | tous les arguments de la dernière |
| `fc -l` | lister avec numéros |
| `HISTSIZE` | nombre max de commandes mémorisées |
| `echo $HISTFILE` | fichier d'historique |

## Variables
```bash
VAR="valeur"
export VAR
readonly VAR
echo "$VAR"
unset VAR
```
Variables utiles : `$HOME`, `$PATH`, `$USER`, `$HOSTNAME`, `$SHELL`, `$PWD`, `$OLDPWD`, `$BASH_VERSION`, `$LINENO`, `$RANDOM`.

## Substitution
| Syntaxe | Effet |
|---|---|
| `$(commande)` | substitution de commande (préférée) |
| `` `commande` `` | substitution (ancien) |
| `${var}` | référence sûre |
| `${var:-défaut}` | valeur par défaut si non définie |
| `${var:=défaut}` | valeur par défaut + affectation |
| `${var:+alt}` | alternative si définie |
| `${var:?message}` | erreur si non définie |
| `${var:2}` | depuis la position 2 |
| `${var:2:4}` | 4 caractères depuis la position 2 |
| `${#var}` | longueur |
| `${var%motif}` | retirer la plus petite fin correspondante |
| `${var%%motif}` | retirer la plus grande fin |
| `${var#motif}` | retirer le plus petit début |
| `${var##motif}` | retirer le plus grand début |
| `${var/motif/remp}` | remplacer la première occurrence |
| `${var//motif/remp}` | remplacer toutes les occurrences |
| `${var^^}` | tout en majuscules |
| `${var,,}` | tout en minuscules |

## Conditions `[[ ]]` (spécifique Bash)
```bash
[[ "$a" == "x" ]]        # égalité chaîne
[[ "$a" != "x" ]]
[[ "$a" > "b" ]]         # comparaison alphabétique
[[ -n "$a" ]]            # non vide
[[ -z "$a" ]]            # vide
[[ "$a" =~ ^[0-9]+$ ]]   # regex
[[ -f file ]] && [[ -d dir ]]   # ET explicite
[[ -f file && -d dir ]]         # ET dans [[ ]]
[[ -f file || -d dir ]]         # OU
[[ ! -f file ]]                 # négation
```

## Tableaux (Bash 4+)
```bash
arr=(un deux trois)
echo "${arr[0]}"        # un
echo "${arr[@]}"        # tous les éléments
echo "${#arr[@]}"       # taille
arr+=("quatre")         # ajouter
unset 'arr[1]'          # supprimer un élément
for el in "${arr[@]}"; do echo "$el"; done
```
Tableaux associatifs (Bash 4) :
```bash
declare -A dico=([nom]="Alice" [age]=30)
echo "${dico[nom]}"
```

## Arithmétique
```bash
$(( 5 + 3 ))
(( x = 5 + 3 ))
let "y = 2 * 5"
```
Opérateurs : `+ - * / % **` et raccourcis `+= -= *= /= %= ++ --`.

## Expansion (globbing)
| Motif | Effet |
|---|---|
| `*` | toute séquence |
| `?` | un caractère |
| `[abc]` | un parmi |
| `[!abc]` | sauf |
| `{a,b,c}` | liste |
| `{1..5}` | séquence |
| `*.{jpg,png}` | jpg ou png |

## Sécurité de scripts
```bash
#!/bin/bash
set -e          # arrêt à la 1re erreur
set -u          # erreur si variable non définie
set -o pipefail # erreur si un pipe échoue
set -x          # tracer les commandes exécutées
```
| Option | Effet |
|---|---|
| `-e` | exit on error |
| `-u` | erreur sur variable non définie |
| `-x` | mode trace |
| `-v` | afficher les lignes lues |
| `-n` | vérifier la syntaxe sans exécuter |
| `-f` | désactiver le globbing |

## Trap
```bash
trap 'echo "Nettoyage..." ; rm -f /tmp/tmpfile' EXIT
trap 'echo "Interruption"' INT TERM
```
Signaux courants : `EXIT`, `INT` (Ctrl+C), `TERM`, `KILL`, `HUP`, `USR1`, `USR2`.

## Options de debug
```bash
bash -n script.sh      # syntaxe seule
bash -x script.sh      # trace
bash -v script.sh      # verbose
set -o xtrace          # trace dans le script
```

## Personnalisation (.bashrc / .bash_profile)

- `~/.bashrc` : lu à chaque shell **interactif** → aliases, fonctions, prompt.
- `~/.bash_profile` (ou `~/.profile`) : lu à la **connexion** → variables exportées, souvent `source ~/.bashrc`.

### Exemple de .bashrc complet
```bash
# === Alias ===
alias ll='ls -la'
alias la='ls -A'
alias ..='cd ..'
alias grep='grep --color=auto'
alias rm='rm -i'                 # confirmation avant suppression
alias cp='cp -i'
alias gs='git status'
alias update='sudo apt update && sudo apt upgrade'

# === Variables ===
export EDITOR=nano
export LANG=fr_FR.UTF-8
export PATH="$HOME/bin:$PATH"
export HISTSIZE=10000
export HISTFILESIZE=20000

# === Invite de commande (PS1) ===
PS1='\u@\h:\w\$ '
# Version colorée (codes ANSI) :
# PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '

# === Historique ===
shopt -s histappend              # ajouter plutôt qu'écraser
HISTCONTROL=ignoreboth           # ignorer doublons et lignes à espace initial

# === Fonctions ===
mkcd() { mkdir -p "$1" && cd "$1"; }

extract() {
    case "$1" in
        *.tar.gz) tar -xzvf "$1" ;;
        *.zip) unzip "$1" ;;
        *.tar) tar -xf "$1" ;;
        *) echo "Extension non gérée" ;;
    esac
}

# === Complétion ===
if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
fi

# === Couleurs ls ===
export LS_COLORS='di=1;34:ln=1;36:*.tar=1;31'
```

### PS1 : séquences d'invite
| Séquences | Effet |
|---|---|
| `\u` | utilisateur |
| `\h` | hôte (court) |
| `\H` | hôte complet |
| `\w` | dossier courant (chemin) |
| `\W` | nom du dossier seul |
| `\d` | date |
| `\t` / `\T` | heure (24 h / 12 h) |
| `\#` | numéro de commande |
| `\$` | `$` (ou `#` si root) |
| `\n` | nouvelle ligne |
| `\[\033[...m\]` | code ANSI couleur |

### .inputrc (Readline — partagé avec d'autres shells)
| Ligne | Effet |
|---|---|
| `set completion-ignore-case on` | complétion insensible à la casse |
| `set show-all-if-ambiguous on` | afficher les choix dès le 1er Tab |
| `set bell-style none` | désactiver le bip |
| `"\C-w": backward-kill-word` | personnaliser un raccourci |

### Appliquer / vérifier
```bash
source ~/.bashrc       # recharger dans le shell courant
bash --noprofile --norc   # shell temporaire sans configuration
alias                  # lister les alias
echo $PS1              # prompt actuel
declare -F             # lister les fonctions définies
```

## `shopt` (options Bash)
| Commande | Effet |
|---|---|
| `shopt -s nullglob` | expansion vide au lieu de laisser le motif |
| `shopt -s globstar` | `**` récursif |
| `shopt -s histappend` | historique append |
| `shopt -s autocd` | `cd` implicite |
| `shopt -s cdspell` | corriger les fautes de `cd` |
| `shopt -s checkwinsize` | recalcule la taille de fenêtre |
| `shopt -u nom` | désactiver |

## Pièges fréquents
| Erreur | Correction |
|---|---|
| Espaces dans `VAR = x` | `VAR=x` (sans espaces) |
| `if [ $var == x ]` | `if [[ "$var" == "x" ]]` |
| Boucle sur `ls` | `for f in *.txt` |
| `cat fichier | grep motif` | `grep motif fichier` |
| Sans `-r` sur `read` | `while IFS= read -r ligne` |
| `$(cmd)` sans guillemets | `"$(cmd)"` |

## Commandes Bash vs POSIX sh
| Fonctionnalité | sh | Bash |
|---|---|---|
| `[[ ]]` | non | oui |
| Tableaux | non | oui |
| `${var^^}` | non | oui |
| `<(...)` (process substitution) | non (souvent) | oui |
| `$(( ))` arithmétique | oui | oui |
| `local` dans les fonctions | non | oui |
