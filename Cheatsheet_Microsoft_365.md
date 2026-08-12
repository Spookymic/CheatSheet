# Cheatsheet Microsoft 365

## Vue d'ensemble
Suite cloud de Microsoft : bureautique, messagerie, collaboration, stockage et sécurité.

| Application | Rôle |
|---|---|
| **Outlook** | messagerie, calendrier, contacts |
| **Teams** | messagerie, visioconférence, collaboration |
| **SharePoint** | intranet, sites d'équipe, gestion documentaire |
| **OneDrive** | stockage personnel dans le cloud |
| **Exchange Online** | messagerie hébergée (backend d'Outlook) |
| **Word / Excel / PowerPoint** | bureautique (versions cloud + locales) |
| **Microsoft 365 Apps** | suite bureautique complète |
| **Power Platform** | Power BI, Power Apps, Power Automate |
| **Intune** | gestion des appareils (MDM/MAM) |
| **Purview** | sécurité, conformité, gouvernance |

## Portails principaux
| Portail | URL | Usage |
|---|---|---|
| Accueil utilisateur | `portal.office.com` | accès à toutes les apps |
| Admin centre | `admin.microsoft.com` | gestion des utilisateurs, licences, services |
| Exchange admin | `admin.exchange.microsoft.com` | messagerie, transport, protection |
| SharePoint admin | `admin.microsoft.com` → SharePoint | sites, OneDrive, gouvernance |
| Teams admin | `admin.teams.microsoft.com` | politiques Teams, réunions |
| Security & Compliance | `purview.microsoft.com` | sécurité, conformité, rétention |
| Azure AD (Entra) | `entra.microsoft.com` | identités, groupes, rôles |
| Endpoint (Intune) | `intune.microsoft.com` | appareils, applications |
| Defender | `security.microsoft.com` | menaces, alertes |
| Centre de messages | `admin.microsoft.com` → Santé | message center, service health |

## Rôles d'administration courants
| Rôle | Pouvoirs |
|---|---|
| **Global Administrator** | tout (à limiter !) |
| **Global Reader** | lecture seule |
| **Exchange Administrator** | messagerie |
| **SharePoint Administrator** | sites et stockage |
| **Teams Administrator** | Teams et réunions |
| **User Administrator** | comptes et licences |
| **Password Administrator** | réinitialiser les mots de passe |
| **Helpdesk Administrator** | réinitialiser mots de passe, gérer les incidents |
| **Billing Administrator** | facturation et licences |
| **Security Administrator** | sécurité, alertes |

Raccourcis : cliquer sur le rôle dans Admin → "Rôles" pour attribuer.

## Gestion des utilisateurs (Admin centre)
| Action | Chemin |
|---|---|
| Créer un utilisateur | `Utilisateurs` → `Utilisateurs actifs` → `Ajouter un utilisateur` |
| Réinitialiser un mot de passe | sélectionner l'utilisateur → `Réinitialiser le mot de passe` |
| Bloquer la connexion | `États de connexion` → `Bloquer la connexion` |
| Attribuer une licence | sélectionner → `Licences et applications` |
| Supprimer un utilisateur | `Supprimer l'utilisateur` (période de grâce 30 j) |
| Restaurer un utilisateur | `Utilisateurs supprimés` → `Restaurer` |
| Groupes | `Groupes` → `Groupes Microsoft 365` (boîtes aux lettres partagées) |

## Licences et plans
| Plan | Caractéristiques |
|---|---|
| **Business Basic** | apps web + Exchange, Teams, SharePoint, OneDrive |
| **Business Standard** | + apps de bureau |
| **E3** | tout + sécurité avancée, eDiscovery, DLP |
| **E5** | E3 + sécurité/analytique avancée, Phone System |
| **F3 / F1** | « firstline workers », fonctions réduites |

Vérifier la consommation : Admin → `Facturation` → `Licences`.

## Exchange Online
| Concept | Détail |
|---|---|
| Boîte aux lettres | `utilisateur@domaine.com` |
| Boîte partagée | plusieurs accès, mot de passe géré par l'admin |
| Salle / équipement | ressources de calendrier |
| Groupe Microsoft 365 | boîte + équipe Teams + site |
| Liste de distribution | groupe de messagerie |
| Alias | adresses secondaires d'une boîte |

### Tâches courantes
| Tâche | Où / commande |
|---|---|
| Créer une boîte partagée | `Exchange admin` → `Boîtes aux lettres` → `Ajouter une boîte aux lettres partagée` |
| Ajouter un alias | Exchange admin → boîte → `Adresses e-mail` |
| Déléguer l'accès | boîte → `Délégués` (Envoyer en tant que, Envoyer de la part de, Accès complet) |
| Règles de transport | Exchange admin → `Règles de transport` |
| Règles de flux de courrier | filtrer, rediriger, disclaimers |
| Restreindre l'envoi | `Restreindre les destinataires` |

### PowerShell Exchange Online
```powershell
Connect-ExchangeOnline                     # se connecter (module requis)
Get-Mailbox -Identity user@domaine.com
Get-Mailbox -ResultSize Unlimited | Measure-Object
New-Mailbox -Shared -Name "compta" -PrimarySmtpAddress compta@domaine.com
Add-MailboxPermission -Identity compta -User "user@domaine.com" -AccessRights FullAccess
Set-Mailbox -Identity user@domaine.com -Alias nouvelalias
Get-EXOMailboxStatistics -Identity user@domaine.com | Select-Object TotalItemSize, ItemCount
Set-Mailbox -Identity user@domaine.com -LitigationHoldEnabled $true
Remove-Mailbox -Identity user@domaine.com -Confirm
```
Module : `Install-Module ExchangeOnlineManagement -Scope CurrentUser`.

## SharePoint / OneDrive
| Élément | Détail |
|---|---|
| Site d'équipe | espace collaboratif d'un groupe |
| Site de communication | site vitrine / d'annonces |
| Bibliothèque de documents | ensemble de fichiers versionnés |
| Quota OneDrive | par défaut 1 To par utilisateur |
| Versioning | activé par défaut (500 versions) |
| Liens de partage | autoriser/désactiver via les politiques |

### Commandes PowerShell SharePoint
```powershell
Connect-SPOService -Url https://<tenant>-admin.sharepoint.com
Get-SPOSite -Limit All
Set-SPOSite -Identity https://<tenant>.sharepoint.com/sites/nom -StorageQuota 100
Get-SPOSite -Identity URL | Select-Object Url, StorageUsageCurrent
Set-SPOSite -Identity URL -AllowExternalSharing Disabled
New-SPOSite -Url https://<tenant>.sharepoint.com/sites/projet -Owner user@domaine.com -StorageQuota 25
Remove-SPOSite -Identity URL -Confirm
```
Gestion des politiques de partage : Admin SharePoint → `Politiques` → `Partage`.

## Teams
| Élément | Détail |
|---|---|
| Équipe | conteneur de canaux |
| Canal | espace de discussion d'une équipe |
| Réunion | visio + chat + partage |
| Réunion publique / Teams Live | webinaires |

### Actions courantes (Admin Teams)
| Action | Chemin |
|---|---|
| Créer une équité | Teams admin → `Équipes` → `Créer` |
| Politique de réunion | `Réunions` → `Politiques de réunion` |
| Politique de messagerie | `Politiques de messagerie` |
| Limiter la création d'équipes | `Politiques d'équipe` → désactiver la création |
| Téléphones Teams | `Voix` → `Plans d'appel` |

### Commandes PowerShell Teams
```powershell
Connect-MicrosoftTeams
Get-Team -DisplayName "Direction"
New-Team -DisplayName "Projet X" -Visibility Private
Add-TeamUser -GroupId <id> -User user@domaine.com -Role Member
Set-Team -GroupId <id> -AllowUserEditMessages $false
Remove-Team -GroupId <id>
```

## Sécurité et conformité (Purview)
| Élément | Rôle |
|---|---|
| Stratégies de rétention | conserver / supprimer des contenus |
| Étiquettes de confidentialité | classifier et protéger (chiffrement) |
| Prévention de perte de données (DLP) | bloquer les données sensibles |
| eDiscovery | recherche et exportation de contenu |
| Journaux d'audit | traçabilité des actions |
| Verrouillage de conformité | protection juridique du contenu |
| Protection contre les menaces | anti-phishing, anti-malware (Defender) |

### PowerShell Sécurité & Conformité
```powershell
Connect-IPPSSession
Get-RetentionCompliancePolicy
New-RetentionCompliancePolicy -Name "Retention1an" -SharePointLocation All -RetentionComplianceAction Keep -RetentionDuration 365
New-ComplianceTag -Name "Confidentiel"
```

## Microsoft Graph (API principale)
Base : `https://graph.microsoft.com/v1.0`
```bash
# Exemples de requêtes
GET /users                                     # liste des utilisateurs
GET /users/{id}/mailboxSettings                # réglages boîte
GET /groups                                    # groupes
POST /users                                    # créer un utilisateur
PATCH /users/{id}                              # modifier
DELETE /users/{id}                             # supprimer
GET /reports                                   # rapports d'usage
```

### PowerShell avec Microsoft Graph
```powershell
Install-Module Microsoft.Graph -Scope CurrentUser
Connect-MgGraph -Scopes "User.Read.All", "Group.Read.All"
Get-MgUser -All | Select-Object DisplayName, UserPrincipalName, UserType
Get-MgUser -Filter "userPrincipalName eq 'user@domaine.com'"
New-MgUser -DisplayName "Alice" -UserPrincipalName "alice@domaine.com" -MailNickname "alice" -PasswordProfile @{Password="..."} -AccountEnabled
```

## Microsoft 365 Apps (bureautique)
| Élément | Détail |
|---|---|
| Installation | Admin → `Installations d'applications` → `Microsoft 365 Apps` |
| Portail de téléchargement | `portal.office.com` → Installer les applications |
| Version | Canal Entreprise (stable), Semi-annuelle |
| Activation | compte M365 (pas de clé) |
| Web Apps | version navigateur (Word, Excel, PowerPoint) |

## Rapports et usage
| Rapport | Où |
|---|---|
| Activité utilisateurs | Admin → `Rapports` → `Utilisation` |
| Messagerie | boîtes, délais, taille |
| Teams | activités, réunions |
| OneDrive / SharePoint | stockage, partage |
| Graph API | `GET /reports` |

## PowerShell : module de connexion rapide
| Module | Connexion |
|---|---|
| `ExchangeOnlineManagement` | `Connect-ExchangeOnline` |
| `Microsoft.Online.SharePoint.PowerShell` | `Connect-SPOService` |
| `MicrosoftTeams` | `Connect-MicrosoftTeams` |
| `Microsoft.Graph` | `Connect-MgGraph` |
| `ExchangeOnlineManagement` (Security) | `Connect-IPPSSession` |
| `MSOnline` (héritage, à éviter) | `Connect-MsolService` |

Prérequis : PowerShell 7+ recommandé, module installé, droits admin et MFA.

## Dépannage rapide
| Problème | Vérifications |
|---|---|
| Utilisateur ne reçoit pas de mail | `Get-Mailbox` existe ? boîte en attente ? règles de transport ? |
| Mot de passe oublié | Admin → réinitialiser (l'utilisateur change ensuite) |
| Quota OneDrive atteint | Admin OneDrive → augmenter le quota |
| Licence manquante | Admin → Utilisateurs → Licences et applications |
| Équipe Teams invisible | politique de création bloquée, licence Teams absente |
| MFA demandé en boucle | vérifier les politiques d'accès conditionnel (Entra) |
| Récupérer un utilisateur supprimé | Admin → Utilisateurs supprimés → Restaurer (sous 30 j) |

## Bonnes pratiques
- Restreindre le rôle **Global Administrator** au strict minimum.
- Activer **MFA** pour tous les administrateurs (accès conditionnel).
- Utiliser **les groupes Microsoft 365** plutôt que les listes de distribution.
- Activer la **rétention** et l'**audit** avant les incidents.
- Réaliser des **exports de sauvegarde** des sites et boîtes critiques.
- Vérifier régulièrement les **licences inutilisées** pour maîtriser les coûts.
- Documenter les **URLs des tenants** (`<tenant>.sharepoint.com`, `<tenant>.onmicrosoft.com`).
