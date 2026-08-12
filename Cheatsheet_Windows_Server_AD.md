# Cheatsheet Windows Server / Active Directory

## Rôles courants
| Rôle | Rôle Windows | Usage |
|---|---|---|
| AD DS | Active Directory Domain Services | domaines, comptes, GPO |
| DNS | DNS Server | résolution du domaine |
| DHCP | DHCP Server | distribution d'adresses IP |
| Fichiers | File Server | partages (SMB) |
| RDS | Remote Desktop Services | bureaux distants |
| WSUS | Windows Server Update Services | mises à jour centralisées |
| IIS | Web Server (IIS) | sites web |
| DFS | DFS Namespaces / Replication | espaces de noms et réplication de partages |

## Installation du contrôleur de domaine (PowerShell)
```powershell
# Ajouter les rôles
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools

# Promouvoir en contrôleur de domaine (nouvelle forêt)
Install-ADDSForest `
  -DomainName "corp.local" `
  -DomainNetbiosName "CORP" `
  -ForestMode WinThreshold `
  -SafeModeAdministratorPassword (ConvertTo-SecureString "MotDePasse!" -AsPlainText -Force) `
  -InstallDns

# Vérifier
Get-ADDomain
Get-ADForest
```

## Joindre une machine au domaine
```powershell
# PowerShell (à exécuter sur le client, admin local)
Add-Computer -DomainName "corp.local" -Credential "CORP\admin" -Restart

# Version classique : propriétés Système → Modifier → rejoindre le domaine
```
`nltest /dsgetdc:corp.local` : tester la connexion au contrôleur de domaine.

## Utilisateurs et groupes (module ActiveDirectory)
| Commande | Effet |
|---|---|
| `Get-ADUser -Filter *` | lister les utilisateurs |
| `Get-ADUser -Identity jdupont -Properties *` | détails d'un utilisateur |
| `New-ADUser -Name "Dupont Jean" -SamAccountName jdupont -UserPrincipalName jdupont@corp.local -AccountPassword (Read-Host -AsSecureString) -Enabled $true` | créer un utilisateur |
| `New-ADUser -Name "dupont" -Path "OU=Comptes,DC=corp,DC=local" -AccountPassword ...` | créer dans une OU |
| `Set-ADUser jdupont -GivenName Jean -Surname Dupont -Department "IT"` | modifier un utilisateur |
| `Enable-ADAccount jdupont` / `Disable-ADAccount jdupont` | activer / désactiver un compte |
| `Unlock-ADAccount jdupont` | déverrouiller un compte |
| `Get-ADGroup -Filter *` | lister les groupes |
| `New-ADGroup -Name "G-IT" -GroupScope Global -Path "OU=Groupes,DC=corp,DC=local"` | créer un groupe |
| `Add-ADGroupMember -Identity "G-IT" -Members jdupont` | ajouter un membre |
| `Get-ADGroupMember -Identity "G-IT"` | lister les membres |
| `Remove-ADGroupMember -Identity "G-IT" -Members jdupont -Confirm:$false` | retirer un membre |
| `Add-ADGroupMember -Identity "Domain Admins" -Members jdupont` | ajouter aux admins de domaine |
| `Get-ADComputer -Filter *` | lister les machines du domaine |
| `Get-ADUser -Filter {Enabled -eq $false}` | utilisateurs désactivés |
| `Search-ADAccount -AccountDisabled` | comptes désactivés |

**OU** (Organizational Unit) : conteneur d'administration (utilisateurs, groupes, machines, GPO).
**Distinguished Name (DN)** : `CN=Nom,OU=...,DC=domaine,DC=local`.
**UPN** : `utilisateur@domaine.local`.

## Stratégies de groupe (GPO)
| Outil / commande | Rôle |
|---|---|
| **GPMC** (gpmc.msc) | console de gestion des GPO |
| `gpedit.msc` | éditeur de GPO local |
| `gpupdate /force` | appliquer les GPO immédiatement |
| `gpresult /r` | GPO appliquées à la machine / utilisateur |
| `gpresult /h rapport.html` | rapport HTML |
| `Get-GPO -All` | lister les GPO (PowerShell) |
| `Get-GPResultantSetOfPolicy` | GPO effectives (PowerShell) |
| `rsop.msc` | résultat des stratégies (ancien) |

### Ordre d'application des GPO
`Local → Site → Domaine → OU` (les GPO de l'OU gagnent par défaut). Héritage bloquable (`Enforced` / `Block Inheritance`).

Exemples de stratégies : mot de passe (longueur, complexité, verrouillage), restriction logicielle/AppLocker, redirection de dossiers, scripts de connexion, pare-feu, mise à jour Windows.

## DNS
| Commande | Effet |
|---|---|
| `Get-DnsServerZone` | lister les zones |
| `Get-DnsServerResourceRecord -ZoneName corp.local` | enregistrements d'une zone |
| `Add-DnsServerResourceRecordA -ZoneName corp.local -Name web -IPv4Address 10.0.0.10` | créer un enregistrement A |
| `dnscmd /enumzones` | lister les zones (CLI) |
| `nslookup nom` / `Resolve-DnsName nom` | résoudre un nom |

## DHCP
| Commande | Effet |
|---|---|
| `Get-DhcpServerv4Scope` | lister les plages DHCP |
| `Get-DhcpServerv4Lease -ScopeId 10.0.0.0` | lister les baux |
| `Get-DhcpServerv4Reservation -ScopeId 10.0.0.0` | lister les réservations |
| `Add-DhcpServerv4Reservation -ScopeId 10.0.0.0 -IPAddress 10.0.0.50 -ClientId "AA-BB-CC-DD-EE-FF"` | créer une réservation MAC → IP |

## Partages SMB
| Commande | Effet |
|---|---|
| `New-SmbShare -Name "Data" -Path "D:\Data" -FullAccess "G-IT"` | créer un partage |
| `Get-SmbShare` | lister les partages |
| `Set-SmbShare -Name Data -ChangeAccess "G-User"` | modifier les permissions |
| `Remove-SmbShare -Name Data` | supprimer un partage |
| `net share` | lister (CLI classique) |

## Sauvegarde et restauration AD
| Élément | Détail |
|---|---|
| Sauvegarde | Windows Server Backup (état système) |
| Mode de restauration | DSRM (*Directory Services Restore Mode*) |
| Corbeille AD | active par défaut depuis 2012 R2 |
| Autorité de restauration | après restauration, marquer l'écart comme autoritatif (`ntdsutil` → `authoritative restore`) |

## RDS (Remote Desktop Services)
| Rôle | Rôle |
|---|---|
| RD Session Host | hôte de session (bureaux) |
| RD Connection Broker | répartition entre hôtes |
| RD Gateway | accès RDP sécurisé depuis Internet |
| RD Web Access | accès web aux bureaux |

## Dépannage
| Problème | Commande / action |
|---|---|
| Machine hors domaine | `nltest /dsgetdc:`, vérifier DNS du client |
| Connexion impossible | `Get-ADUser -Filter * -Properties LastLogonDate` |
| GPO non appliquées | `gpupdate /force`, `gpresult /h`, vérifier l'héritage |
| Réplication cassée | `repadmin /replsum` |
| DNS en erreur | `dcdiag /test:dns` |
| Diagnostic global | `dcdiag` |
| Heure décalée (Kerberos) | synchroniser le temps (`w32tm /resync`) |
| Comptes verrouillés | `Unlock-ADAccount`, stratégie de verrouillage |
