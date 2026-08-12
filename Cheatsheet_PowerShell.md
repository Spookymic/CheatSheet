# Cheatsheet PowerShell

## Base : commandes (cmdlets)
Syntaxe : `Verbe-Nom -Paramètre valeur`

| Commande | Effet |
|---|---|
| `Get-Command` | liste les cmdlets disponibles |
| `Get-Help cmdlet` | aide (`-Detailed`, `-Examples`, `-Online`) |
| `Get-Alias` | liste des alias |
| `Update-Help` | mettre à jour l'aide locale |
| `Get-Verb` | liste des verbes standard |

Verbes courants : `Get` (lire), `Set` (modifier), `New` (créer), `Remove` (supprimer), `Copy`, `Move`, `Test`, `Start`, `Stop`, `Restart`.

## Fichiers et dossiers
| Commande | Effet |
|---|---|
| `Get-ChildItem` / `dir` / `ls` | lister (`-Recurse`, `-Force` pour les cachés) |
| `Set-Location` / `cd` | changer de dossier |
| `Get-Location` / `pwd` | dossier courant |
| `New-Item fichier` | créer un fichier |
| `New-Item -ItemType Directory dossier` | créer un dossier |
| `Copy-Item source dest -Recurse` | copier |
| `Move-Item source dest` | déplacer / renommer |
| `Remove-Item cible -Recurse -Force` | supprimer (attention) |
| `Test-Path chemin` | vérifier l'existence |
| `Get-Content fichier` / `cat` | lire un fichier |
| `Set-Content fichier "texte"` | écrire un fichier |
| `Add-Content fichier "texte"` | ajouter à la fin |
| `Get-Content fichier -Tail 50` | dernières lignes (comme `tail`) |
| `Get-Content fichier -Wait` | suivre en temps réel (`-Wait`) |
| `Rename-Item` | renommer |
| `Select-Object` | sélectionner des propriétés |

## Affichage et filtrage
| Commande | Effet |
|---|---|
| `cmdlet \| Select-Object -Property Nom,Type` | choisir les colonnes |
| `cmdlet \| Where-Object {$_.Property -eq 'valeur'}` | filtrer |
| `cmdlet \| Sort-Object -Property Nom -Descending` | trier |
| `cmdlet \| Group-Object -Property Nom` | grouper et compter |
| `cmdlet \| Measure-Object` | statistiques (count, sum...) |
| `cmdlet \| Format-Table` / `Format-List` / `Format-Wide` | formats d'affichage |
| `cmdlet \| Out-GridView` | afficher dans une fenêtre filtrable |
| `cmdlet \| Export-Csv fichier.csv -NoTypeInformation` | exporter en CSV |
| `cmdlet \| ConvertTo-Json` | convertir en JSON |
| `cmdlet \| Out-File fichier.txt` | écrire dans un fichier |

Opérateurs de comparaison : `-eq`, `-ne`, `-gt`, `-lt`, `-ge`, `-le`, `-like`, `-notlike`, `-match`, `-contains`, `-in`. Insensibles à la casse par défaut (utiliser `c` : `-ceq` pour sensible).

## Système
| Commande | Effet |
|---|---|
| `Get-Process` | lister les processus |
| `Get-Process \| Where-Object {$_.CPU -gt 100}` | processus gourmands |
| `Stop-Process -Name nom -Force` | tuer un processus |
| `Get-Service` / `Get-Service -Name nom` | services |
| `Start-Service nom` / `Stop-Service nom` / `Restart-Service nom` | gérer un service |
| `Set-Service nom -StartupType Automatic` | démarrage automatique |
| `Get-ComputerInfo` | infos système détaillées |
| `Get-Date` | date/heure |
| `Get-NetIPAddress` | adresses IP |
| `Get-NetAdapter` | interfaces réseau |
| `Get-NetTCPConnection -State Listen` | ports en écoute |
| `Test-NetConnection hôte -Port 443` | tester un port (nc-like) |
| `Resolve-DnsName domaine` | résolution DNS |
| `Get-EventLog -LogName System -Newest 20` | journaux d'événements |
| `Get-WinEvent -FilterHashtable @{LogName='System'; Level=2}` | événements filtrés |

## Variables et types
```powershell
$nom = "valeur"          # chaîne
$nb = 42                 # entier
$liste = @("a","b","c")  # tableau
$dico = @{cle = "valeur"} # hashtable
```
| Opération | Code |
|---|---|
| Concaténer | `"Bonjour $nom"` ou `$nom + $nom` |
| Interpolation | `"Le total est $total"` |
| Type d'une variable | `$var.GetType()` |
| Portée globale | `$global:var` |
| Variable d'environnement | `$env:PATH` |
| Sortie d'une fonction | `return "valeur"` ou écriture en sortie |

## Conditions et boucles
```powershell
if ($a -gt 10) {
    "grand"
} elseif ($a -eq 10) {
    "exact"
} else {
    "petit"
}

switch ($jour) {
    "lundi" { "début de semaine" }
    default { "autre jour" }
}
```
```powershell
foreach ($el in $liste) { $el }

for ($i = 0; $i -lt 5; $i++) { $i }

$i = 0
while ($i -lt 5) { $i++; $i }

1..5 | ForEach-Object { $_ * 2 }   # pipeline, $_ = élément courant
```

## Fonctions
```powershell
function Get-Bonjour {
    param(
        [string]$Nom,
        [int]$Age = 0
    )
    "Bonjour $Nom, $Age ans"
}
Get-Bonjour -Nom "Alice" -Age 30
```
| Point | Détail |
|---|---|
| Noms de fonctions | Verbe-Nom obligatoire pour les cmdlets |
| `param(...)` | déclaration des paramètres |
| Valeurs par défaut | `[int]$Age = 0` |
| `$PSBoundParameters` | paramètres réellement passés |
| `$MyInvocation` | contexte d'appel |

## Gestion d'erreurs
```powershell
try {
    Get-Content "fichier.txt" -ErrorAction Stop
} catch {
    Write-Host "Erreur : $($_.Exception.Message)"
} finally {
    Write-Host "Fin"
}
```
| Élément | Rôle |
|---|---|
| `-ErrorAction Stop` | transformer les erreurs non bloquantes en exceptions |
| `$_` dans catch | objet erreur (`$_.Exception.Message`) |
| `$Error` | historique des erreurs |
| `$ErrorActionPreference = 'Stop'` | comportement global |
| `-WhatIf` | simuler sans exécuter (avec les cmdlets qui le supportent) |

## Sécurité d'exécution
| Commande | Effet |
|---|---|
| `Get-ExecutionPolicy` | politique actuelle |
| `Set-ExecutionPolicy RemoteSigned` | autoriser les scripts locaux |
| `Unblock-File fichier.ps1` | débloquer un fichier téléchargé |
| `powershell -File script.ps1` | exécuter un script |
| `& .\script.ps1` | exécuter dans la session courante |
| `powershell -ExecutionPolicy Bypass -File script.ps1` | exécuter sans contrainte |

Politiques : `Restricted` (défaut), `RemoteSigned`, `AllSigned`, `Unrestricted`, `Bypass`.

## Raccourcis et astuces
| Raccourci | Effet |
|---|---|
| `Tab` | complétion |
| `Ctrl+R` | recherche dans l'historique |
| `F7` / `F8` | historique visuel |
| `Ctrl+L` | effacer l'écran |
| `Ctrl+C` | interrompre |
| `&&` / `;` | enchaîner des commandes |
| `#` | commentaire |
| `` ` `` | caractère d'échappement / continuation |
| `!` | pas un historique comme bash ; utiliser `Get-History` |

## Modules
| Commande | Effet |
|---|---|
| `Get-Module -ListAvailable` | modules installés |
| `Import-Module nom` | charger un module |
| `Install-Module nom -Scope CurrentUser` | installer (PowerShell Gallery) |
| `Update-Module nom` | mettre à jour |
| `Find-Module nom` | rechercher |
| `Remove-Module nom` | décharger |
| `Get-Command -Module nom` | cmdlets d'un module |

## Personnalisation (profil $PROFILE)

Le profil est un script PowerShell exécuté à chaque démarrage. Il sert à définir aliases, fonctions, invite, couleurs, raccourcis.

### Emplacement du profil
```powershell
$PROFILE                 # chemin complet du profil courant
$PROFILE.CurrentUserAllHosts
Test-Path $PROFILE       # le profil existe-t-il ?
```
| Variable | Fichier |
|---|---|
| `$PROFILE.CurrentUserCurrentHost` | profil courant (recommandé) |
| `$PROFILE.CurrentUserAllHosts` | profil de l'utilisateur, tous les hôtes |
| `$PROFILE.AllUsersCurrentHost` | tous les utilisateurs, hôte courant |

### Créer / éditer
```powershell
if (-not (Test-Path $PROFILE)) { New-Item -Path $PROFILE -ItemType File -Force }
notepad $PROFILE          # ouvrir dans le Bloc-notes
. $PROFILE                # recharger dans la session courante
```

### Exemple de profil
```powershell
# === Alias ===
Set-Alias ll Get-ChildItem
Set-Alias g git

# === Fonctions ===
function .. { Set-Location .. }
function mkcd { param($d) New-Item -ItemType Directory -Path $d -Force | Out-Null; Set-Location $d }
function grep { Select-String -Pattern $args }

# === Invite de commande personnalisée ===
function prompt {
    "PS $($executionContext.SessionState.Path.CurrentLocation)$('>' * ($nestedPromptLevel + 1)) "
}

# === PSReadLine (raccourcis de saisie) ===
Set-PSReadLineOption -PredictionSource History
Set-PSReadLineKeyHandler -Key Tab -Function MenuComplete
Set-PSReadLineKeyHandler -Key Ctrl+r -Function ReverseSearchHistory

# === Couleurs ===
Set-PSReadLineOption -Colors @{
    Command = 'Green'
    Parameter = 'Cyan'
    String = 'Yellow'
    Comment = 'DarkGray'
}

# === Valeurs par défaut ===
$ErrorActionPreference = 'Continue'
Set-Location ~
```

### Gestion de l'invite (prompt)
| Élément | Exemple |
|---|---|
| Fonction `prompt` | redéfinir entièrement l'invite |
| `Get-Location` | dossier courant dans le prompt |
| `[DateTime]::Now.ToString("HH:mm")` | heure dans le prompt |
| Couleurs ANSI | `` "`e[32m" `` (vert) au sein du prompt |

### Chargement automatique
- Créer le dossier des modules : `$HOME\Documents\PowerShell\Modules` → les modules y sont chargés automatiquement.
- `$env:PSModulePath` : chemins de recherche des modules.
- Un module de profil : `$PROFILE` peut lui-même `Import-Module`.

### Astuces
- Sauvegarder le profil dans le dépôt (dotfiles) et le symlinker.
- `Get-Content $PROFILE` pour le lire.
- Vérifier qu'il n'y a pas d'erreur au chargement : relancer et observer la console.


## Scripts utiles en exemple
```powershell
# Lister les machines et leur espace disque libre
Get-Process | Where-Object {$_.WorkingSet64 -gt 500MB} |
    Select-Object Name, Id, @{N='RAM_Go';E={[math]::Round($_.WorkingSet64/1GB,2)}} |
    Sort-Object RAM_Go -Descending
```
```powershell
# Vérifier la disponibilité d'un port sur plusieurs hôtes
"server1", "server2" | ForEach-Object {
    $r = Test-NetConnection $_ -Port 3389 -WarningAction SilentlyContinue
    [PSCustomObject]@{ Hôte = $_; RDP_Dispo = $r.TcpTestSucceeded }
}
```
