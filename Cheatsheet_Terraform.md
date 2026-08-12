# Cheatsheet Terraform

## Généralités
- **IaC** (*Infrastructure as Code*) : déclarer l'infrastructure en code HCL (`.tf`).
- **Provider** : plugin vers un fournisseur (AWS, Azure, GCP, Proxmox, vSphere, Docker...).
- **State** (`terraform.tfstate`) : état réel de l'infrastructure, à stocker dans un **backend distant** (S3, Azure Blob, GCS, Terraform Cloud).
- **Resource** : objet géré (VM, réseau, DNS...).
- **Module** : bloc de ressources réutilisable.
- Idempotent : `apply` ne modifie que ce qui diffère.

## Commandes
| Commande | Effet |
|---|---|
| `terraform init` | initialiser le projet, télécharger les providers |
| `terraform fmt` | formater le code HCL |
| `terraform validate` | valider la syntaxe et la cohérence |
| `terraform plan` | prévisualiser les changements (dry-run) |
| `terraform apply` | appliquer les changements (demande confirmation) |
| `terraform apply -auto-approve` | appliquer sans confirmation |
| `terraform destroy` | supprimer toute l'infrastructure |
| `terraform destroy -target=resource.nom` | supprimer une ressource |
| `terraform show` | afficher l'état courant |
| `terraform state list` | lister les ressources de l'état |
| `terraform state show resource.nom` | détails d'une ressource |
| `terraform state rm resource.nom` | retirer de l'état (sans supprimer) |
| `terraform state pull` / `push` | exporter / importer l'état |
| `terraform import ressource.nom ID` | importer une ressource existante |
| `terraform providers` | lister les providers utilisés |
| `terraform workspace list` | lister les workspaces |
| `terraform plan -out=plan.tfplan` | sauvegarder un plan |
| `terraform apply plan.tfplan` | appliquer un plan sauvegardé |
| `terraform taint resource.nom` | forcer la recréation (déprécié → `-replace`) |
| `terraform apply -replace=resource.nom` | recréer une ressource |
| `terraform graph` | exporter le graphe des dépendances |
| `terraform output` | afficher les sorties définies |
| `terraform console` | tester des expressions HCL |

Options utiles : `-var="clé=valeur"`, `-var-file="pro.tfvars"`, `-target=resource.nom`, `-no-color`, `-compact-warnings`.

## Syntaxe HCL
```hcl
# Provider
terraform {
  required_providers {
    proxmox = { source = "bpg/proxmox", version = "0.71.0" }
  }
  backend "local" { path = "terraform.tfstate" }
}

provider "proxmox" {
  pm_api_url = "https://pve.example.com:8006/api2/json"
}

# Variable
variable "vm_name" {
  type        = string
  default     = "web"
  description = "Nom de la VM"
}

# Resource
resource "proxmox_vm_qemu" "web" {
  name        = var.vm_name
  target_node = "pve"
  clone       = "debian12-template"
  cores       = 2
  memory      = 2048
  network {
    bridge = "vmbr0"
  }
  disk {
    storage = "local-lvm"
    size    = "20G"
  }
}

# Sortie
output "vm_id" {
  value = proxmox_vm_qemu.web.vm_id
}
```

## Variables et interpolation
| Syntaxe | Usage |
|---|---|
| `var.nom` | référence une variable |
| `"${var.env}-vm"` | interpolation dans une chaîne |
| `count = 3` | créer 3 ressources |
| `count.index` | index de l'itération |
| `for_each = { a = 1, b = 2 }` | itérer sur une map |
| `each.key` / `each.value` | clé / valeur de for_each |
| `local.nom` | valeur calculée (`locals { ... }`) |
| `data.provider.ressource.nom` | référence des données (data source) |
| `resource.prox.ressource.nom` | référence une ressource |
| `terraform.workspace` | workspace courant |

### Fichiers
| Fichier | Rôle |
|---|---|
| `main.tf` | ressources principales |
| `variables.tf` | déclaration des variables |
| `outputs.tf` | sorties |
| `providers.tf` | providers et backend |
| `terraform.tfvars` / `*.auto.tfvars` | valeurs des variables (auto-chargé) |
| `terraform.tfvars.json` | valeurs au format JSON |

## Modules
```hcl
module "web" {
  source  = "./modules/vm"
  vm_name = "web-01"
  cores   = 2
}
```
Structure d'un module : `main.tf`, `variables.tf`, `outputs.tf`.

## Workspaces (environnements)
| Commande | Effet |
|---|---|
| `terraform workspace new dev` | créer un workspace |
| `terraform workspace select dev` | basculer |
| `terraform workspace show` | workspace actif |
| `terraform workspace delete dev` | supprimer |

## Backends distants (état partagé)
```hcl
terraform {
  backend "s3" {
    bucket = "mon-bucket-tfstate"
    key    = "prod/terraform.tfstate"
    region = "eu-west-3"
  }
}
```
Puis `terraform init -migrate-state`.

## Bonnes pratiques
- Toujours stocker l'état dans un **backend distant** en équipe (verrouillage).
- Ne jamais committer `terraform.tfstate` ni les fichiers `.tfstate.*`.
- Garder les secrets dans des variables et utiliser un **Vault / variables CI** (jamais en dur).
- Formater et valider avant chaque `apply` (`terraform fmt -recursive`, `terraform validate`).
- Privilégier les **modules** pour réutiliser le code.
- Utiliser `plan` avant `apply` et `-out` en CI/CD.
- Ajouter `terraform.tfstate` et `*.tfstate.*` au `.gitignore`.
