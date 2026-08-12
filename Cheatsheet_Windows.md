# Cheatsheet Windows

## Raccourcis généraux
| Raccourci | Effet |
|---|---|
| `Win` | menu Démarrer |
| `Win+D` | afficher le bureau |
| `Win+E` | Explorateur de fichiers |
| `Win+R` | boîte Exécuter |
| `Win+I` | Paramètres |
| `Win+L` | verrouiller la session |
| `Win+X` | menu super utilisateur (PowerShell, Gestionnaire de périphériques...) |
| `Win+Tab` | Affichage des tâches |
| `Alt+Tab` | changer d'application |
| `Ctrl+Shift+Échap` | Gestionnaire des tâches |
| `Win+Pause` | informations système |
| `Win+PrtScn` | capture d'écran (enregistrée dans Images) |
| `Win+Shift+S` | capture d'écran (outil Capture) |
| `PrtScn` | capture plein écran (presse-papiers) |
| `Win+Flèche` | fenêtres (ancrer, maximiser, réduire) |
| `Win+.` | emojis / symboles |
| `Win+V` | presse-papiers (historique) |
| `Alt+F4` | fermer la fenêtre / éteindre |
| `Ctrl+Alt+Suppr` | écran de sécurité (verrouiller, gérer les tâches) |
| `Win+1` à `Win+9` | lancer l'app de la barre des tâches |

## Commandes utiles (Win+R ou PowerShell)
| Commande | Effet |
|---|---|
| `cmd` | invite de commandes |
| `powershell` / `pwsh` | PowerShell |
| `msconfig` | configuration système |
| `services.msc` | services Windows |
| `diskmgmt.msc` | gestion des disques |
| `devmgmt.msc` | gestionnaire de périphériques |
| `appwiz.cpl` | programmes et fonctionnalités |
| `control` | Panneau de configuration |
| `control panel` | Panneau de configuration (Windows 11) |
| `ncpa.cpl` | connexions réseau |
| `firewall.cpl` | Pare-feu |
| `taskmgr` | Gestionnaire des tâches |
| `regedit` | Éditeur du registre |
| `notepad` | Bloc-notes |
| `mstsc` | Connexion Bureau à distance |
| `osk` | clavier virtuel |
| `winver` | version de Windows |
| `dxdiag` | DirectX / infos système |
| `cleanmgr` | Nettoyage de disque |
| `compmgmt.msc` | gestion de l'ordinateur |
| `gpedit.msc` | Éditeur de stratégie de groupe (Pro) |
| `secpol.msc` | stratégie de sécurité locale (Pro) |
| `sysdm.cpl` | propriétés système (avancées) |
| `ipconfig` | configuration IP |
| `perfmon` | moniteur de performances |
| `resmon` | moniteur de ressources |

## Explorateur de fichiers
| Raccourci | Effet |
|---|---|
| `Alt+↑` | dossier parent |
| `Alt+←` / `Alt+→` | précédent / suivant |
| `F2` | renommer |
| `Ctrl+Shift+N` | nouveau dossier |
| `Ctrl+Maj+Suppr` | supprimer définitivement |
| `Ctrl+C` / `Ctrl+V` | copier / coller |
| `Ctrl+X` / `Ctrl+V` | couper / coller |
| `F5` | actualiser |
| `Ctrl+L` | barre d'adresse |

## Outils intégrés
| Outil | Comment l'ouvrir |
|---|---|
| PowerShell (admin) | `Win+X` → Windows PowerShell (Admin) |
| Terminal Windows | `Win+X` → Terminal |
| Outil Capture | `Win+Shift+S` |
| Gestionnaire de périphériques | `devmgmt.msc` |
| Moniteur de ressources | `resmon` |
| Planificateur de tâches | `taskschd.msc` |
| Observateur d'événements | `eventvwr.msc` |
| Analyseur de fiabilité | `perfmon /rel` |
| Optimiser les lecteurs | `dfrgui` |

## Paramètres réseau
| Action | Chemin |
|---|---|
| Wi-Fi / Ethernet | `Paramètres` → `Réseau et Internet` |
| Adresses IP statiques | Paramètres → Réseau → Ethernet → Modifier (IPv4) |
| DNS | `ncpa.cpl` → clic droit → Propriétés → IPv4 |
| VPN | Paramètres → Réseau → VPN |
| Partage de connexion | Paramètres → Réseau → Point d'accès mobile |
| Vider le cache DNS | `ipconfig /flushdns` |

## Maintenance
| Action | Commande |
|---|---|
| Vérifier les disques | `chkdsk C:` |
| Vérifier l'intégrité du système | `sfc /scannow` |
| Réparer les images système | `DISM /Online /Cleanup-Image /RestoreHealth` |
| Redémarrer le réseau | `ipconfig /release` puis `/renew` |
| Mettre à jour Windows | `Paramètres` → `Windows Update` |
| Nettoyage | `cleanmgr` ou Paramètres → Système → Stockage |
| Mode sans échec | `Win+R` → `msconfig` → onglet Démarrage, ou Shift + Redémarrer |

## Sécurité
| Action | Comment |
|---|---|
| Sécurité Windows | `Paramètres` → `Confidentialité et sécurité` → `Sécurité Windows` |
| Analyse rapide | Sécurité Windows → Protection contre les virus |
| BitLocker | `Paramètres` → Confidentialité et sécurité → Chiffrement de l'appareil |
| Compte administrateur | Paramètres → Comptes → Famille et autres utilisateurs |
| Réinitialiser ce PC | Paramètres → Système → Récupération |
| Point de restauration | `Paramètres` → Système → À propos → Protection du système |
| Pare-feu avancé | `wf.msc` |

## Dépannage
| Problème | Commande / action |
|---|---|
| Réseau lent / coupé | `ipconfig /flushdns`, `netsh winsock reset`, redémarrer le routeur |
| Souris/écran figé | `Ctrl+Alt+Suppr` → Gestionnaire des tâches → forcer l'arrêt |
| Imprimante bloquée | `services.msc` → redémarrer `Spouleur d'impression` |
| Écran bleu récurrent | analyser avec `perfmon /rel` et les journaux d'événements |
| Application ne s'ouvre pas | `Win+X` → Terminal (Admin) → `sfc /scannow` |

## Astuces
- **Défragmentation automatique** : activée par défaut, ne pas lancer `dfrgui` sur un SSD (utiliser TRIM, géré automatiquement).
- **Historique du presse-papiers** : `Win+V` (l'activer dans Paramètres → Système → Presse-papiers).
- **Installation de logiciels** : privilégier `winget install nom` pour l'automatisation.
- **Créer un raccourci rapide** : clic droit sur le bureau → Nouveau → Raccourci.
- **Clavier multilingue** : `Win+Espace` pour changer la langue de saisie.
