# Cheatsheet Vi / Vim

## Modes
| Action | Touche |
|---|---|
| Passer en mode insertion (avant curseur) | `i` |
| Passer en mode insertion (après curseur) | `a` |
| Insérer en début de ligne | `I` |
| Insérer en fin de ligne | `A` |
| Ajouter une ligne en dessous | `o` |
| Ajouter une ligne au-dessus | `O` |
| Retour en mode normal | `Échap` |

## Déplacements (mode normal)
| Touche | Effet |
|---|---|
| `h` / `j` / `k` / `l` | gauche / bas / haut / droite |
| `w` / `b` | mot suivant / mot précédent |
| `e` | fin du mot |
| `0` | début de ligne |
| `$` | fin de ligne |
| `gg` | début du fichier |
| `G` | fin du fichier |
| `:42` | aller à la ligne 42 |
| `Ctrl+d` / `Ctrl+u` | demi-écran bas / haut |
| `Ctrl+f` / `Ctrl+b` | écran suivant / précédent |
| `%` | parenthèse/ accolade correspondante |

## Édition (mode normal)
| Touche | Effet |
|---|---|
| `x` | supprimer le caractère |
| `dd` | supprimer la ligne |
| `5dd` | supprimer 5 lignes |
| `dw` | supprimer le mot |
| `D` | supprimer jusqu'à la fin de ligne |
| `yy` | copier la ligne |
| `5yy` | copier 5 lignes |
| `p` / `P` | coller après / avant le curseur |
| `u` | annuler |
| `Ctrl+r` | rétablir |
| `r` | remplacer un caractère |
| `cw` | changer le mot |
| `cc` | changer la ligne |
| `.` | répéter la dernière commande |

## Recherche et remplacement
| Commande | Effet |
|---|---|
| `/mot` | rechercher vers l'avant |
| `?mot` | rechercher vers l'arrière |
| `n` / `N` | résultat suivant / précédent |
| `:%s/ancien/nouveau/g` | remplacer tout |
| `:%s/ancien/nouveau/gc` | remplacer avec confirmation |
| `:s/ancien/nouveau/` | remplacer sur la ligne courante |

## Sélection visuelle
| Touche | Effet |
|---|---|
| `v` | sélection mode caractères |
| `V` | sélection mode lignes |
| `Ctrl+v` | sélection mode bloc |
| `d` / `y` | supprimer / copier la sélection |

## Fichiers et fenêtres
| Commande | Effet |
|---|---|
| `:w` | enregistrer |
| `:wq` ou `ZZ` | enregistrer et quitter |
| `:q` | quitter |
| `:q!` | quitter sans enregistrer |
| `:e fichier` | ouvrir un fichier |
| `:sp fichier` | split horizontal |
| `:vsp fichier` | split vertical |
| `Ctrl+w` puis `h/j/k/l` | naviguer entre les splits |

## Autres raccourcis utiles
| Touche | Effet |
|---|---|
| `:set number` | afficher les numéros de ligne |
| `:set nonumber` | masquer les numéros |
| `:noh` | retirer la surbrillance de recherche |
| `>>` / `<<` | indenter / désindenter la ligne |
| `gv` | re-sélectionner la dernière sélection |

## Personnalisation (.vimrc)

Le fichier `~/.vimrc` est lu à chaque démarrage de Vim. Il n'existe pas par défaut : il faut le créer.

### Options essentielles
| Commande | Effet |
|---|---|
| `set number` | numéros de ligne |
| `set relativenumber` | numéros relatifs (idéal avec dd/yy) |
| `set tabstop=4` | largeur d'une tabulation |
| `set shiftwidth=4` | largeur d'indentation (`>>` / `<<`) |
| `set expandtab` | tabulations remplacées par des espaces |
| `set autoindent` | indentation automatique |
| `set smartindent` | indentation intelligente |
| `set hlsearch` | surbrillance de la recherche |
| `set incsearch` | recherche incrémentale |
| `set ignorecase` / `set smartcase` | casse insensible (sauf si majuscule tapée) |
| `set cursorline` | surligner la ligne courante |
| `set ruler` | position du curseur |
| `set laststatus=2` | barre de statut toujours visible |
| `set mouse=a` | souris active |
| `set background=dark` | thème sombre |
| `set wrap` / `set nowrap` | retour à la ligne |
| `syntax on` | coloration syntaxique |
| `set undofile` | historique d'annulation persistant |

### Exemple de .vimrc complet
```
" Commentaire vim (le " marque le commentaire)
set number relativenumber
set tabstop=4 shiftwidth=4 expandtab
set autoindent smartindent
set hlsearch incsearch smartcase
set cursorline
set laststatus=2
set mouse=a
syntax on
colorscheme desert

" Raccourcis personnalisés (nnoremap = mode normal, sans récursion)
nnoremap <C-s> :w<CR>
nnoremap <C-q> :q<CR>
nnoremap <F5> :set number!<CR>
inoremap jj <Esc>               " taper 'jj' sort du mode insertion

" Copier/coller avec le presse-papiers système
vnoremap <C-c> "+y
nnoremap <C-v> "+p
```

### Mappings : quelle commande ?
| Commande | Mode concerné |
|---|---|
| `map` | normal, visuel, opérateur |
| `nmap` / `nnoremap` | normal |
| `imap` / `inoremap` | insertion |
| `vmap` / `vnoremap` | visuel |
| `omap` / `onoremap` | opérateur-pending |
| `noremap` (ou `nnoremap`) | sans récursion (à privilégier) |

### Plugins avec vim-plug
Installer vim-plug (une fois) :
```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```
Dans `~/.vimrc` :
```
call plug#begin('~/.vim/plugged')
Plug 'preservim/nerdtree'          " explorateur de fichiers
Plug 'vim-airline/vim-airline'     " barre de statut moderne
Plug 'junegunn/fzf.vim'            " recherche floue
Plug 'dense-analysis/ale'          " linting / erreurs de code
call plug#end()
```
| Commande | Effet |
|---|---|
| `:PlugInstall` | installer les plugins déclarés |
| `:PlugUpdate` | mettre à jour |
| `:PlugClean` | supprimer les plugins retirés |
| `:PlugStatus` | état des plugins |

### La commande :set
| Commande | Effet |
|---|---|
| `:set nom?` | afficher la valeur actuelle |
| `:set nom=valeur` | modifier |
| `:set nom!` | basculer on/off |
| `:set all` | lister toutes les options |
| `:options` | navigation dans les options |

> Astuce : `vimtutor` en ligne de commande pour apprendre pas à pas.
