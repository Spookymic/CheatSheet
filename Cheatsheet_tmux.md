# Cheatsheet tmux

> Le préfixe par défaut est `Ctrl+b`. Dans les tableaux, `Préfixe` signifie : appuyer sur `Ctrl+b`, relâcher, puis appuyer sur la touche indiquée.

## Sessions
| Commande / Touche | Effet |
|---|---|
| `tmux` | démarrer une nouvelle session |
| `tmux new -s nom` | créer une session nommée |
| `tmux ls` | lister les sessions |
| `tmux attach -t nom` | s'attacher à une session existante |
| `tmux a` | s'attacher à la dernière session |
| `tmux kill-session -t nom` | tuer une session |
| `tmux kill-server` | tuer toutes les sessions |
| `tmux rename-session -t ancien nouveau` | renommer une session |
| `Préfixe` puis `s` | choisir une session (liste interactive) |
| `Préfixe` puis `$` | renommer la session courante |
| `Préfixe` puis `d` | se détacher (session en arrière-plan) |
| `Préfixe` puis `(` / `)` | session précédente / suivante |
| `Préfixe` puis `L` | basculer sur la dernière session utilisée |

## Fenêtres (windows)
| Commande / Touche | Effet |
|---|---|
| `Préfixe` puis `c` | créer une nouvelle fenêtre |
| `Préfixe` puis `,` | renommer la fenêtre |
| `Préfixe` puis `w` | choisir une fenêtre (liste interactive) |
| `Préfixe` puis `n` / `p` | fenêtre suivante / précédente |
| `Préfixe` puis `0`-`9` | aller à la fenêtre numérotée |
| `Préfixe` puis `l` | dernière fenêtre utilisée |
| `Préfixe` puis `&` | fermer la fenêtre (avec confirmation) |
| `Préfixe` puis `'` | aller à la fenêtre par index/nom |
| `Préfixe` puis `f` | rechercher une fenêtre par nom |
| `Préfixe` puis `Ctrl+o` | faire tourner les fenêtres |

## Panneaux (panes)
| Commande / Touche | Effet |
|---|---|
| `Préfixe` puis `%` | découper verticalement |
| `Préfixe` puis `"` | découper horizontalement |
| `Préfixe` puis `flèches` | changer de panneau |
| `Préfixe` puis `o` | panneau suivant |
| `Préfixe` puis `;` | dernier panneau utilisé |
| `Préfixe` puis `q` | afficher les numéros des panneaux |
| `Préfixe` puis `x` | fermer le panneau (avec confirmation) |
| `Préfixe` puis `z` | zoom / dézoom du panneau |
| `Préfixe` puis `!` | transformer le panneau en fenêtre |
| `Préfixe` puis `{` / `}` | déplacer le panneau (gauche / droite) |
| `Préfixe` puis `Espace` | changer la disposition des panneaux |
| `Préfixe` puis `Ctrl+flèches` | redimensionner le panneau |
| `Préfixe` puis `Alt+flèches` | redimensionner par pas de 5 |

## Copier / Coller (mode copy)
| Commande / Touche | Effet |
|---|---|
| `Préfixe` puis `[` | entrer en mode copie (défilement) |
| `Espace` | débuter une sélection |
| `Entrée` | copier la sélection |
| `Préfixe` puis `]` | coller |
| `q` | quitter le mode copie |
| `Ctrl+u` / `Ctrl+d` | défiler le demi-écran |
| `g` / `G` | début / fin de l'historique |
| `/mot` / `?mot` | rechercher vers l'avant / l'arrière |
| `n` / `N` | résultat suivant / précédent |

## Gestion des commandes
| Touche | Effet |
|---|---|
| `Préfixe` puis `:` | ouvrir l'invite de commande tmux |
| `Préfixe` puis `?` | afficher l'aide (toutes les combinaisons) |
| `Préfixe` puis `t` | afficher une grande horloge |

## Scripting / ligne de commande
| Commande | Effet |
|---|---|
| `tmux new -s nom -d` | créer une session détachée |
| `tmux new-window -t nom -n fen nom_prog` | créer une fenêtre dans une session |
| `tmux split-window -h` | découper horizontalement depuis le shell |
| `tmux send-keys -t nom 'cmd' Enter` | envoyer une commande à une session |
| `tmux source-file ~/.tmux.conf` | recharger la configuration |
| `tmux list-keys` | lister tous les raccourcis |
| `tmux show-options -g` | afficher les options globales |

## Configuration (~/.tmux.conf)

Le fichier `~/.tmux.conf` est lu au démarrage du serveur tmux. Le créer s'il n'existe pas, puis recharger avec `tmux source-file ~/.tmux.conf` (ou `Préfixe` puis `:` puis `source-file ~/.tmux.conf`).

### Options essentielles
| Commande | Effet |
|---|---|
| `set -g prefix C-a` | utiliser `Ctrl+a` comme préfixe |
| `unbind C-b` | libérer l'ancien préfixe |
| `bind r source-file ~/.tmux.conf \; display "Reloaded"` | recharger avec `Préfixe r` |
| `set -g base-index 1` | fenêtres numérotées à partir de 1 |
| `setw -g pane-base-index 1` | panneaux numérotés à partir de 1 |
| `set -g mouse on` | activer la souris (sélection, redimension) |
| `set -g history-limit 10000` | taille de l'historique de défilement |
| `setw -g mode-keys vi` | navigation en mode copie avec les touches vi |
| `set -sg escape-time 10` | réduire le délai de la touche Échap |
| `set -g renumber-windows on` | renuméroter les fenêtres après fermeture |
| `set -g status-interval 1` | rafraîchir la barre de statut chaque seconde |

### Exemple de ~/.tmux.conf complet
```
# Préfixe Ctrl+a
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# Index à partir de 1
set -g base-index 1
setw -g pane-base-index 1
set -g renumber-windows on

# Souris et historique
set -g mouse on
set -g history-limit 10000

# Mode copie façon vi
setw -g mode-keys vi
bind -T copy-mode-vi v send -X begin-selection
bind -T copy-mode-vi y send -X copy-selection-and-cancel

# Recharger la config
bind r source-file ~/.tmux.conf \; display "Config rechargée"

# Découpes plus simples
bind | split-window -h
bind - split-window -v

# Navigation entre panneaux avec les flèches (et vi)
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# Barre de statut
set -g status-style 'bg=colour235 fg=colour136'
set -g status-left '#[fg=green][#S] '
set -g status-right '#[fg=yellow]%H:%M %d-%b'
```

### Plugins avec TPM (Tmux Plugin Manager)
Installer TPM (une fois) :
```bash
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```
Dans `~/.tmux.conf` :
```
# Les commentaires doivent être sur leur propre ligne : pas de commentaire en fin de ligne en tmux
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
# restaurer les sessions
set -g @plugin 'tmux-plugins/tmux-resurrect'
# sauvegarde automatique
set -g @plugin 'tmux-plugins/tmux-continuum'
# thème
set -g @plugin 'catppuccin/tmux'
run '~/.tmux/plugins/tpm/tpm'
```
| Commande | Effet |
|---|---|
| `Préfixe` puis `I` | installer les plugins |
| `Préfixe` puis `U` | mettre à jour les plugins |
| `Préfixe` puis `Alt+u` | désinstaller un plugin |
| `Préfixe` puis `Ctrl+s` | sauvegarder la session (resurrect) |
| `Préfixe` puis `Ctrl+r` | restaurer la session (resurrect) |

### Navigateur de plugins : tpack
Remplacement de TPM (compatibilité totale) écrit en Go, avec un **navigateur de plugins** en TUI et une CLI. Installation (remplace TPM) :
```bash
git clone https://github.com/tmuxpack/tpack ~/.tmux/plugins/tpm
```
La ligne `run '~/.tmux/plugins/tpm/tpm'` de `~/.tmux.conf` reste identique. Le binaire `tpack` est téléchargé automatiquement au premier lancement.

| Touche / Commande | Effet |
|---|---|
| `Préfixe` puis `T` | ouvrir le navigateur TUI (chercher, installer, thèmes) |
| `Préfixe` puis `I` | installer les plugins déclarés |
| `Préfixe` puis `U` | mettre à jour (via TUI) |
| `Préfixe` puis `Alt+u` | supprimer les plugins non déclarés |
| `tpack install` | installer depuis la ligne de commande |
| `tpack update` | mettre à jour |
| `tpack clean` | supprimer les plugins non déclarés |
| `tpack tui` | ouvrir la TUI |
| `tpack version` | afficher la version |

> Attention : tpack range chaque plugin dans un dossier lié au dépôt (ex. `tmux-87a1216f1f68`). Le retour vers TPM n'est pas transparent, et les scripts utilisant les anciens chemins doivent être mis à jour.

### Lister les plugins
```bash
# Déclarés dans la config (méthode fiable)
grep '@plugin' ~/.tmux.conf

# Plugins réellement installés (dossiers clonés)
ls -1 ~/.tmux/plugins
```
> Attention : `tmux show-options -g | grep @plugin` n'affiche que le **dernier** plugin. tmux écrase les `@plugin` successifs ; TPM contourne cela en lisant directement le fichier de config.

> Astuce : `tmux kill-server` pour repartir de zéro, `man tmux` pour la référence complète.
