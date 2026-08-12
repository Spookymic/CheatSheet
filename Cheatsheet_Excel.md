# Cheatsheet Excel

## Raccourcis clavier
| Raccourci | Effet |
|---|---|
| `Ctrl+C` / `Ctrl+X` / `Ctrl+V` | copier / couper / coller |
| `Ctrl+Z` / `Ctrl+Y` | annuler / rétablir |
| `Ctrl+S` | enregistrer |
| `Ctrl+A` | tout sélectionner |
| `Ctrl+F` | rechercher |
| `Ctrl+H` | rechercher et remplacer |
| `F2` | modifier la cellule |
| `Entrée` | valider (cellule du dessous) |
| `Tab` | valider (cellule à droite) |
| `Échap` | annuler la saisie |
| `Ctrl+↑/↓/←/→` | aller au bord de la plage de données |
| `Ctrl+Shift+↑/↓/←/→` | sélectionner jusqu'au bord |
| `Ctrl+Home` / `Ctrl+End` | cellule A1 / dernière cellule utilisée |
| `Ctrl+Page Down / Up` | feuille suivante / précédente |
| `Ctrl+D` | recopier le contenu du dessus |
| `Ctrl+R` | recopier le contenu de gauche |
| `Ctrl+;` | date du jour |
| `Ctrl+Shift+:` | heure actuelle |
| `Alt+Entrée` | retour à la ligne dans une cellule |
| `Ctrl+1` | format des cellules |
| `Ctrl+Shift+%` | format pourcentage |
| `Ctrl+Shift+$` | format monétaire |
| `Ctrl+Shift+#` | format date |
| `F4` | répéter la dernière action / références absolues |

## Formules essentielles
| Formule | Usage |
|---|---|
| `=SOMME(A1:A10)` | somme |
| `=MOYENNE(A1:A10)` | moyenne |
| `=MAX(A1:A10)` / `=MIN(A1:A10)` | max / min |
| `=NB(A1:A10)` | nombre de cellules numériques |
| `=NBVAL(A1:A10)` | nombre de cellules non vides |
| `=SI(condition; vrai; faux)` | condition |
| `=SI(ET(...);...)` / `=SI(OU(...);...)` | conditions multiples |
| `=SOMME.SI(plage; critère; somme)` | somme conditionnelle |
| `=SOMME.SI.ENS(plage; crit1; val1; crit2; val2)` | multi-critères |
| `=NB.SI(plage; critère)` | comptage conditionnel |
| `=NB.SI.ENS(...)` | comptage multi-critères |
| `=RECHERCHEV(valeur; tableau; colonne; faux)` | recherche verticale |
| `=RECHERCHEH(valeur; tableau; ligne; faux)` | recherche horizontale |
| `=INDEX(plage; ligne; colonne)` | valeur à une position |
| `=EQUIV(valeur; plage; 0)` | position d'une valeur |
| `=SOUS.TOTAL(9; plage)` | somme dans un filtre |
| `=AUJOURDHUI()` / `=MAINTENANT()` | date / date+heure |
| `=TEXTE(valeur; "jj/mm/aaaa")` | formatage de texte |
| `=GAUCHE(...)` / `=DROITE(...)` / `=STXT(...)` | extraction de texte |
| `=CONCATENER(...)` / `=A1&A2` | concaténation |
| `=SUPPRESPACE(...)` | supprimer les espaces |
| `=SIERREUR(valeur; résultat)` | gérer les erreurs |
| `=ARRONDI(valeur; nb)` | arrondi |
| `=RECHERCHEX(valeur; plage; résultat)` | remplaçant moderne de RECHERCHEV |

## Raccourcis utiles avec plages
| Raccourci | Effet |
|---|---|
| `Ctrl+Shift+L` | activer/désactiver les filtres |
| `Alt+=` | somme automatique |
| `Ctrl+T` | créer un tableau structuré |
| `Ctrl+L` | tableau structuré (autre version) |
| `Ctrl+Shift+Entrée` (anciennes versions) | formule matricielle |

## Fonctionnement des références
| Référence | Signification |
|---|---|
| `A1` | relative (bouge lors du copier-coller) |
| `$A$1` | absolue (fixe colonne et ligne) |
| `$A1` | colonne fixe, ligne relative |
| `A$1` | ligne fixe, colonne relative |
| `F4` | basculer entre les modes |

## Noms de plages
| Action | Comment |
|---|---|
| Créer | `Formules` → `Définir un nom`, ou dans la zone Nom |
| Utiliser | `=SOMME(MonNom)` |
| Gérer | `Formules` → `Gestionnaire de noms` |

## Mise en forme conditionnelle
1. Sélectionner les cellules.
2. `Accueil` → `Mise en forme conditionnelle`.
3. Choisir une règle (plus grand que, doublons, barres de données...).
4. Règle personnalisée : `Règle de mise en surbrillance` → `Autres règles`.

## Tableaux croisés dynamiques (TCD)
1. Sélectionner les données → `Insertion` → `Tableau croisé dynamique`.
2. Glisser les champs vers lignes, colonnes, valeurs, filtres.
3. Actualiser : clic droit → `Actualiser` (ou `Alt+F5`).

## Données
| Action | Comment |
|---|---|
| Filtres | `Données` → `Filtrer` ou `Ctrl+Shift+L` |
| Tri | `Données` → `Trier` |
| Supprimer doublons | `Données` → `Supprimer les doublons` |
| Texte en colonnes | `Données` → `Convertir` |
| Validation | `Données` → `Validation des données` (listes déroulantes) |
| Consolider | `Données` → `Consolider` |

## Formules classiques combinées
| Besoin | Formule |
|---|---|
| Si entre deux valeurs | `=SI(ET(A1>=10;A1<=20);"OK";"NOK")` |
| Double recherche | `=INDEX(B1:B10;EQUIV(A1;A1:A10;0))` |
| Total sur condition date | `=SOMME.SI.ENS(B:B;A:A;">="&DATE(2026;1;1);A:A;"<="&DATE(2026;12;31))` |
| Uniquement entiers | `=SI(A1=ENT(A1);"entier";"décimal")` |

## Astuces
- **Ajuster les colonnes** : double-clic sur la limite de colonne.
- **Figer les volets** : `Affichage` → `Figer les volets`.
- **Collage spécial** : `Ctrl+Alt+V` (valeurs, formats, transposer...).
- **Goutte de remplissage** : `Ctrl+E` pour remplir automatiquement selon le motif.
- **Analyse rapide** : `Ctrl+Q` pour obtenir statistiques et graphiques rapides.
