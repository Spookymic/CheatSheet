# Cheatsheet Linux

## Navigation fichiers / dossiers
| Commande | Effet |
|---|---|
| `pwd` | dossier courant |
| `ls` / `ls -la` | lister / lister détaillé (cachés) |
| `cd dossier` | changer de dossier |
| `cd ..` / `cd ~` / `cd -` | parent / home / précédent |
| `find /chemin -name "*.txt"` | rechercher des fichiers |
| `locate nom` | recherche rapide (base indexée) |
| `tree` | afficher l'arborescence |

## Manipulation de fichiers
| Commande | Effet |
|---|---|
| `touch fichier` | créer un fichier vide |
| `mkdir -p a/b/c` | créer des dossiers (imbriqués) |
| `cp source dest` | copier |
| `cp -r source dest` | copier un dossier |
| `mv source dest` | déplacer / renommer |
| `rm fichier` | supprimer |
| `rm -rf dossier` | supprimer récursivement (dangereux) |
| `ln -s cible lien` | créer un lien symbolique |
| `cat fichier` | afficher le contenu |
| `less fichier` | lire fichier (navigable, `q` pour quitter) |
| `head -n 10 fichier` | premières lignes |
| `tail -n 10 fichier` | dernières lignes |
| `tail -f fichier` | suivre un fichier en temps réel |

## Permissions
| Commande | Effet |
|---|---|
| `chmod 755 fichier` | changer les permissions (u=7 g=5 o=5) |
| `chmod u+x fichier` | ajouter l'exécution pour le user |
| `chown user:group fichier` | changer le propriétaire |
| `chgrp group fichier` | changer le groupe |
| `umask` | permissions par défaut des nouveaux fichiers |

Chiffres : 4=lecture, 2=écriture, 1=exécution → `chmod 750` = rwx r-x ---

## Processus
| Commande | Effet |
|---|---|
| `ps aux` | lister les processus |
| `top` / `htop` | moniteur de processus |
| `kill PID` | terminer un processus |
| `kill -9 PID` | forcer l'arrêt |
| `pkill nom` | tuer par nom |
| `jobs` / `fg` / `bg` | gestion des taches en arrière-plan |
| `commande &` | lancer en arrière-plan |
| `nohup cmd &` | lancer détaché du terminal |
| `Ctrl+z` | suspendre, `Ctrl+c` interrompre |

## Réseau
| Commande | Effet |
|---|---|
| `ip a` | adresses IP |
| `ip route` | table de routage |
| `ping hôte` | tester la connectivité |
| `traceroute hôte` | tracer le chemin |
| `ss -tulpn` | ports et sockets en écoute |
| `netstat -tulpn` | ancien équivalent |
| `curl URL` / `wget URL` | requêter / télécharger |
| `host domaine` / `dig domaine` | DNS |
| `scp user@hôte:source dest` | copie via SSH |

## Utilisateurs et droits
| Commande | Effet |
|---|---|
| `sudo commande` | exécuter en admin |
| `su - user` | changer d'utilisateur |
| `whoami` / `id` | identité courante |
| `useradd` / `userdel` | créer / supprimer utilisateur |
| `passwd` | changer le mot de passe |
| `usermod -aG groupe user` | ajouter au groupe |
| `adduser user sudo` | donner les droits sudo |

## Disques et archives
| Commande | Effet |
|---|---|
| `df -h` | espace disque |
| `du -sh dossier` | taille d'un dossier |
| `tar -czvf arch.tar.gz dossier` | compresser |
| `tar -xzvf arch.tar.gz` | extraire |
| `zip -r arch.zip dossier` / `unzip arch.zip` | zip |
| `mount` / `umount` | monter / démonter |
| `fdisk -l` / `lsblk` | listage des disques |

## Recherche dans les fichiers
| Commande | Effet |
|---|---|
| `grep motif fichier` | rechercher |
| `grep -r motif dossier` | récursif |
| `grep -i motif` | insensible à la casse |
| `grep -v motif` | exclure les correspondances |
| `rg` / `ack` | alternatives modernes |

## Divers
| Commande | Effet |
|---|---|
| `alias ll='ls -la'` | créer un alias |
| `history` | historique des commandes |
| `man commande` | aide en ligne |
| `shutdown` / `reboot` | éteindre / redémarrer |
| `systemctl status service` | état d'un service |
| `systemctl start/stop/restart/enable service` | gérer un service |
| `export VAR=valeur` | variable d'environnement |
| `echo $VAR` | lire une variable |

## Redirections et pipes
| Syntaxe | Effet |
|---|---|
| `cmd > fichier` | rediriger la sortie |
| `cmd >> fichier` | rediriger en ajout |
| `cmd 2>&1` | rediriger stderr vers stdout |
| `cmd1 \| cmd2` | chaîner des commandes |
| `xargs` | passer la sortie en arguments |
