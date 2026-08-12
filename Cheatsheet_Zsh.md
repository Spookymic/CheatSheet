# Cheatsheet Zsh

## Spécificités par rapport à Bash
- Shell par défaut sur macOS depuis Catalina.
- Complétion puissante (menu, groupes, descriptions).
- Correction automatique des commandes (`setopt correct`).
- Thèmes et plugins via **oh-my-zsh** ou frameworks.
- Compatible avec la syntaxe Bash pour l'essentiel.
- Expansion **glob avancée** (recursive, qualifiers).
- **Process substitution** et `RPROMPT` (invite à droite) natifs.

## Installation
| Commande | Effet |
|---|---|
| `sudo apt install zsh` | Debian/Ubuntu |
| `sudo dnf install zsh` | Fedora |
| `sudo pacman -S zsh` | Arch |
| `chsh -s $(which zsh)` | définir Zsh comme shell par défaut |
| `echo $SHELL` | vérifier le shell courant |
| `omz` (oh-my-zsh) | `sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"` |

## Fichiers de configuration
| Fichier | Rôle |
|---|---|
| `~/.zshrc` | config interactive (le plus important) |
| `~/.zprofile` | au login (connexion) |
| `~/.zsh_history` | historique |
| `~/.zlogin` | exécuté à l'ouverture de session |
| `~/.zlogout` | à la fermeture |
| `/etc/zshrc` | config globale |
| `~/.zshenv` | chargé à chaque invocation (env variables) |

Après modification : `source ~/.zshrc`.

### Exemple de .zshrc personnalisé
```zsh
# === Alias ===
alias ll='ls -lah'
alias la='ls -A'
alias ..='cd ..'
alias -s txt=nano               # taper "note.txt" ouvre nano
alias -g G='| grep'             # alias global : "ps aux G apache"

# === Options ===
setopt auto_cd                  # taper "dossier" = cd dossier
setopt correct                  # correction orthographique
setopt extended_glob            # glob avancé
setopt hist_ignore_dups
setopt share_history
unsetopt beep

# === Historique ===
HISTFILE=~/.zsh_history
HISTSIZE=10000
SAVEHIST=10000

# === Invite ===
PROMPT='%n@%m:%~%# '            # user@hôte:chemin$
RPROMPT='[%*]'                  # heure à droite

# === PATH ===
path=(~/bin $path)
export PATH

# === Complétion ===
autoload -Uz compinit
compinit
zstyle ':completion:*' menu select
zstyle ':completion:*' matcher-list 'm:{a-z}={A-Za-z}'   # insensible à la casse

# === Fonctions ===
mkcd() { mkdir -p "$1" && cd "$1"; }
```

### Séquences d'invite Zsh
| Séquence | Effet |
|---|---|
| `%n` | utilisateur |
| `%m` / `%M` | hôte (court / complet) |
| `%~` | dossier courant |
| `%d` | dossier (absolu) |
| `%*` | heure `hh:mm:ss` |
| `%D` | date `yy-mm-dd` |
| `%#` | `%` ou `#` si root |
| `%{...%}` | code ANSI couleur |
| `%F{red}...%f` | couleur texte |

### Modes de prompt Zsh
| Variable | Rôle |
|---|---|
| `PS1` / `PROMPT` | invite principale (gauche) |
| `RPS1` / `RPROMPT` | invite à droite |
| `PS2` | invite secondaire (continuation) |
| `PS3` | invite de sélection (select) |

## Options (setopt / unsetopt)
| Option | Effet |
|---|---|
| `setopt auto_cd` | taper `dossier` seuls = `cd dossier` |
| `setopt correct` | correction orthographique des commandes |
| `setopt correct_all` | correction des arguments |
| `setopt hist_ignore_dups` | ignorer les doublons dans l'historique |
| `setopt hist_ignore_space` | ignorer les commandes préfixées d'un espace |
| `setopt share_history` | partager l'historique entre sessions |
| `setopt append_history` | ajouter à l'historique |
| `setopt extended_glob` | glob avancé (`^`, `~`, `#`) |
| `setopt noclobber` | ne pas écraser un fichier par `>` |
| `setopt notify` | notification immédiate des taches terminées |
| `setopt autocontinue` | exécution continue |
| `unsetopt beep` | désactiver le bip |
| `setopt interactivecomments` | permettre les commentaires `#` en ligne |

## Expansion glob avancée (extended_glob)
| Motif | Effet |
|---|---|
| `*.txt~privé*` | `~` = exclusion |
| `*.log^old.log` | `^` = et non |
| `*.txt(#i)` | `(#i)` = insensible à la casse |
| `**/*.py` | récursif dans tous les dossiers |
| `*.txt(.)` | `(.)` = fichiers uniquement |
| `*(/)` | dossiers uniquement |
| `*(@)` | liens symboliques |
| `*(N)` | pas d'erreur si aucun résultat (null glob) |
| `*(mh+1)` | fichiers modifiés il y a +1 heure |
| `*(L+10m)` | fichiers de plus de 10 Mo |
| `*(^m)` | fichiers sans droits |

## Raccourcis spécifiques
| Raccourci | Effet |
|---|---|
| `Ctrl+R` | recherche historique |
| `Alt+←` / `Alt+→` | mot précédent / suivant |
| `Ctrl+U` / `Ctrl+K` | effacer avant / après le curseur |
| `Ctrl+W` | effacer le mot |
| `Ctrl+A` / `Ctrl+E` | début / fin de ligne |
| `Tab` | complétion intelligente (menu si multiple) |
| `Ctrl+X` puis `Ctrl+E` | éditer la ligne dans $EDITOR |
| `Ctrl+T` | navigation fuzzy (si plugin) |
| `Ctrl+Shift+R` | reverse-menu history (compat) |

## Complétion
```zsh
# Activer la complétion
autoload -Uz compinit
compinit
zstyle ':completion:*' menu select
zstyle ':completion:*' list-colors "${(s.:.)LS_COLORS}"
zstyle ':completion:*' matcher-list 'm:{a-z}={A-Za-z}'   # insensible à la casse
```
Astuce : `Tab` plusieurs fois pour parcourir le menu.

## Modificateurs d'expansion
| Syntaxe | Effet |
|---|---|
| `$var:t` | nom de fichier (basename) |
| `$var:h` | répertoire (dirname) |
| `$var:r` | enlever l'extension |
| `$var:e` | extension uniquement |
| `$var:u` | majuscules |
| `$var:l` | minuscules |
| `$var:0` | premier élément d'un tableau |
| `$var:^` | chaque élément sur une ligne |

## Aliases et fonctions
```zsh
alias ll='ls -lah'
alias -s txt=nano    # taper "note.txt" ouvre nano (suffix aliases)
alias -g G='| grep'  # alias global : "ps aux G apache"

mafonction() {
  echo "Bonjour $1"
}
```
Suffix aliases (`alias -s`) : associer une extension à une application.

## Historique
```zsh
setopt HISTFILE=~/.zsh_history
HISTSIZE=10000
SAVEHIST=10000
fc -l        # lister
fc -e vi     # éditer puis rejouer
```
`Ctrl+R` recherche, `Alt+.` dernier argument.

## Variable `path`
```zsh
# Ajouter au PATH de façon propre
path=(~/bin $path)
export PATH
```

## oh-my-zsh — plugins utiles
| Plugin | Rôle |
|---|---|
| `git` | alias git (`gst`, `ga`, `gc`) + infos |
| `zsh-autosuggestions` | suggestions grises |
| `zsh-syntax-highlighting` | coloration syntaxique |
| `docker` / `docker-compose` | alias docker |
| `kubectl` | alias kubernetes |
| `sudo` | double Échap préfixe sudo |
| `history-substring-search` | recherche comme ctrl+r |
| `extract` | `x archive` = décompresse selon l'extension |
| `web-search` | `google motif`, `stackoverflow motif` |

Thème populaire : `powerlevel10k` (personnalisation au lancement).

## Astuces
- **`!!` / `!$` / `!42`** : identiques à Bash pour l'historique.
- **`where`** : équivalent de `which -a` (toutes les occurrences).
- **`=cmd`** : exécuter une commande dans le PATH (bash-like).
- **Multi-lignes** : une ligne se poursuit avec `\` ou entre `(` `)`.
- **Prompt** : gauche `PS1`, droite `RPS1` (ex. : `export RPROMPT='[%~]'`).
- **Correction** : si `correct` actif, taper `grep -l` → suggère `grep`.
- **`print -l`** : afficher un élément par ligne.

## Pièges
| Piège | Explication |
|---|---|
| Compatibilité bash partielle | certains `[[ ]]` fonctionnent, `$(( ))` OK, mais pas tout |
| Globbing actif par défaut | penser à `noglob` ou quoter les métacaractères |
| `rm *` peut correspondre aux fichiers cachés selon l'option `glob_dots` | vérifier avant suppression |
| Beaucoup d'options à connaître | `zsh` est très configurable, ne pas tout activer d'un coup |
