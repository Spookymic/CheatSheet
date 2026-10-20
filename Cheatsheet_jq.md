# Cheatsheet jq

> `jq` est un processeur JSON en ligne de commande : il lit du JSON (fichier ou stdin), applique un **filtre** et écrit le résultat. Syntaxe : `jq [options] 'filtre' [fichier...]`. Toujours entourer le filtre de **guillemets simples** sous Linux/macOS.
>
> Dans les tableaux ci-dessous, `\|` correspond au caractère pipe `|` (échappé pour le Markdown).

## Installation
| Commande | Système |
|---|---|
| `sudo apt install jq` | Debian / Ubuntu |
| `sudo dnf install jq` | RHEL / Fedora / Rocky |
| `brew install jq` | macOS |
| `winget install jqlang.jq` | Windows |
| `choco install jq` | Windows (Chocolatey) |
| `jq --version` | vérifier la version installée |

## Options courantes
| Option | Effet |
|---|---|
| `jq '.' fichier.json` | afficher le JSON formaté (pretty-print) |
| `-r` / `--raw-output` | sortie brute (chaînes sans guillemets) |
| `-j` / `--join-output` | comme `-r` sans retour à la ligne |
| `-c` / `--compact-output` | sortie compacte sur une ligne |
| `-s` / `--slurp` | lire toutes les entrées dans un seul tableau |
| `-n` / `--null-input` | ne pas lire d'entrée (entrée = `null`) |
| `-R` / `--raw-input` | lire chaque ligne comme une chaîne (pas du JSON) |
| `-e` / `--exit-status` | code retour ≠ 0 si le résultat est `false` ou `null` |
| `-S` / `--sort-keys` | trier les clés des objets |
| `-C` / `-M` | forcer / désactiver la couleur |
| `--tab` / `--indent n` | indenter avec tabulations / `n` espaces |
| `--arg nom val` | passer une variable chaîne (`$nom`) |
| `--argjson nom json` | passer une variable JSON (`$nom`) |
| `--slurpfile nom fichier` | charger un fichier JSON dans `$nom` (tableau) |
| `--rawfile nom fichier` | charger un fichier texte dans `$nom` (chaîne) |
| `-f fichier.jq` | lire le filtre depuis un fichier |
| `--seq` | sortie au format JSON Text Sequences (RFC 7464) |
| `--stream` | parser en flux (gros fichiers) |

## Accès aux données
| Filtre | Effet |
|---|---|
| `.` | identité (tout le document) |
| `.nom` | valeur de la clé `nom` |
| `.a.b.c` | accès imbriqué |
| `."cle-avec-tiret"` / `.["cle"]` | clé avec caractères spéciaux |
| `.nom?` | accès sans erreur si l'entrée n'est pas un objet |
| `.[0]` | premier élément d'un tableau |
| `.[-1]` | dernier élément |
| `.[2:5]` | tranche (index 2 à 4) |
| `.[:3]` / `.[-2:]` | 3 premiers / 2 derniers |
| `.[]` | itérer sur tous les éléments (tableau ou objet) |
| `.[]?` | itérer sans erreur si non itérable |
| `.users[].name` | le `name` de chaque utilisateur |
| `..` | descente récursive (toutes les valeurs) |
| `.. \| .id? // empty` | toutes les clés `id` à n'importe quel niveau |

## Combiner les filtres
| Filtre | Effet |
|---|---|
| `f \| g` | pipe : la sortie de `f` alimente `g` |
| `f, g` | produire les résultats de `f` puis de `g` |
| `(f)` | regroupement |
| `[f]` | collecter les résultats dans un tableau |
| `{a: .x, b: .y}` | construire un objet |
| `{name, age}` | raccourci pour `{name: .name, age: .age}` |
| `{(.key): .value}` | clé calculée dynamiquement |
| `.a as $x \| ...` | stocker une valeur dans une variable |
| `f // "défaut"` | valeur par défaut si `null` / `false` / vide |
| `empty` | ne produire aucun résultat |

## Types et littéraux
| Filtre | Effet |
|---|---|
| `type` | type de la valeur (`object`, `array`, `string`, `number`, `boolean`, `null`) |
| `length` | taille (tableau, objet, chaîne) ou valeur absolue (nombre) |
| `keys` / `keys_unsorted` | liste des clés (triées / ordre d'origine) |
| `values` | ignorer les `null` |
| `has("cle")` | la clé existe-t-elle ? |
| `in({...})` | l'entrée est-elle une clé de l'objet donné ? |
| `tostring` / `tonumber` | conversion chaîne ↔ nombre |
| `tojson` / `fromjson` | sérialiser / parser du JSON dans une chaîne |
| `arrays`, `objects`, `strings`, `numbers`, `booleans`, `nulls` | ne garder que ce type |
| `infinite`, `nan`, `null`, `true`, `false` | littéraux |

## Filtrer et sélectionner
| Filtre | Effet |
|---|---|
| `select(.age > 30)` | garder si la condition est vraie |
| `.[] \| select(.actif)` | éléments dont `actif` est vrai |
| `.[] \| select(.nom == "web")` | égalité |
| `select(.tags \| index("prod"))` | tableau contenant une valeur |
| `select(.nom \| test("^srv"))` | correspondance regex |
| `select(.x != null)` | champ présent et non nul |
| `map(select(.port == 443))` | filtrer un tableau en gardant un tableau |
| `==`, `!=`, `<`, `<=`, `>`, `>=` | comparaisons |
| `and`, `or`, `not` | logique booléenne (`not` est un filtre : `.x \| not`) |
| `if .a then "x" elif .b then "y" else "z" end` | condition |
| `any` / `all` | au moins un / tous vrais |
| `any(.[]; .x > 5)` | test sur un générateur |
| `contains({a: 1})` / `inside(...)` | inclusion partielle |
| `first` / `last` / `nth(n)` | premier / dernier / n-ième élément |
| `first(f)` / `limit(n; f)` | premiers résultats d'un générateur |

## Tableaux
| Filtre | Effet |
|---|---|
| `map(f)` | appliquer `f` à chaque élément (`[.[] \| f]`) |
| `map_values(f)` | appliquer `f` aux valeurs (objet ou tableau) |
| `add` | somme des nombres / concaténation de chaînes, tableaux ou objets |
| `sort` / `sort_by(.age)` | trier |
| `sort_by(.a, .b)` | tri multi-critères |
| `reverse` | inverser |
| `unique` / `unique_by(.id)` | dédoublonner |
| `group_by(.type)` | regrouper en sous-tableaux |
| `min` / `max` / `min_by(.x)` / `max_by(.x)` | minimum / maximum |
| `flatten` / `flatten(1)` | aplatir (récursivement / sur 1 niveau) |
| `index("x")` / `rindex("x")` / `indices("x")` | position(s) d'une valeur |
| `[.[] \| .x] \| add / length` | moyenne |
| `. + [4]` / `. - [1]` | ajouter / retirer des éléments |
| `transpose` | transposer une matrice |
| `range(5)` / `range(0; 10; 2)` | générer des nombres |
| `[limit(3; .[])]` | 3 premiers éléments |
| `combinations` | produit cartésien |
| `to_entries` / `from_entries` / `with_entries(f)` | objet ↔ tableau `{key, value}` |

## Objets
| Filtre | Effet |
|---|---|
| `keys` | noms des clés |
| `to_entries` | `{"a":1}` → `[{"key":"a","value":1}]` |
| `from_entries` | inverse de `to_entries` |
| `with_entries(.value \|= tostring)` | transformer chaque clé/valeur |
| `with_entries(select(.key \| startswith("x_")))` | filtrer des clés |
| `. + {new: 1}` | ajouter / écraser une clé (fusion superficielle) |
| `. * {a: {b: 2}}` | fusion récursive (profonde) |
| `del(.cle)` / `del(.a, .b)` | supprimer une ou plusieurs clés |
| `del(.[] \| select(.x == null))` | supprimer des éléments d'un tableau |
| `pick(.a, .b.c)` | ne garder que certains chemins (jq 1.7+) |
| `paths` / `leaf_paths` | tous les chemins / chemins des feuilles |
| `getpath(["a","b"])` / `setpath(["a","b"]; 1)` | lire / écrire via un chemin |
| `delpaths([["a"],["b"]])` | supprimer plusieurs chemins |
| `to_entries \| map("\(.key)=\(.value)") \| .[]` | exporter en `cle=valeur` |

## Modification (affectation)
| Filtre | Effet |
|---|---|
| `.a = 1` | affecter une valeur |
| `.a \|= . + 1` | mettre à jour à partir de la valeur actuelle |
| `.a += 1` / `-=` / `*=` / `/=` / `%=` | opérateurs arithmétiques d'affectation |
| `.a //= "défaut"` | affecter seulement si `null` / absent |
| `.items[].prix \|= . * 1.2` | modifier tous les éléments |
| `(.[] \| select(.id == 2) \| .actif) = false` | modifier un élément ciblé |
| `.tags += ["nouveau"]` | ajouter à un tableau |
| `.date = now` | timestamp actuel |

> Astuce : `=` évalue la partie droite sur l'**entrée globale** ; `|=` l'évalue sur la **valeur à modifier**.

## Chaînes de caractères
| Filtre | Effet |
|---|---|
| `"Bonjour \(.nom)"` | interpolation |
| `ascii_downcase` / `ascii_upcase` | minuscules / majuscules |
| `ltrimstr("x")` / `rtrimstr("x")` | retirer un préfixe / suffixe |
| `trim` / `ltrim` / `rtrim` | supprimer les espaces (jq 1.7.1+) |
| `startswith("x")` / `endswith("x")` | commence / finit par |
| `split(",")` / `join(",")` | découper / joindre |
| `.[2:5]` | sous-chaîne |
| `test("regex"; "i")` | correspond à la regex (flags : `i`, `x`, `g`, `n`...) |
| `match("regex")` | détails de la correspondance |
| `capture("(?<an>\\d{4})-(?<mois>\\d{2})")` | groupes nommés → objet |
| `scan("\\d+")` | toutes les correspondances |
| `sub("a"; "b")` / `gsub("a"; "b")` | remplacer la 1re / toutes les occurrences |
| `splits("\\s+")` | découper par regex |
| `explode` / `implode` | chaîne ↔ tableau de codes caractères |
| `tostring` / `tojson` | convertir en chaîne |
| `@base64` / `@base64d` | encoder / décoder base64 |
| `@uri` | encoder pour une URL |
| `@html` / `@sh` | échapper pour HTML / shell |
| `@csv` / `@tsv` | tableau → ligne CSV / TSV |
| `@json` / `@text` | sérialiser en JSON / texte |

## Dates
| Filtre | Effet |
|---|---|
| `now` | timestamp Unix actuel (secondes, flottant) |
| `todate` / `todateiso8601` | timestamp → `2026-10-20T08:00:00Z` |
| `fromdate` / `fromdateiso8601` | ISO 8601 → timestamp |
| `strftime("%Y-%m-%d %H:%M")` | formater un timestamp (UTC) |
| `strflocaltime("%d/%m/%Y")` | formater en heure locale |
| `strptime("%d/%m/%Y") \| mktime` | parser une date → timestamp |
| `gmtime` / `localtime` | timestamp → tableau « broken down time » |
| `now - (.date \| fromdate) > 86400` | plus vieux qu'un jour ? |

## Mathématiques
| Filtre | Effet |
|---|---|
| `+`, `-`, `*`, `/`, `%` | opérations arithmétiques |
| `floor` / `ceil` / `round` | arrondis |
| `sqrt`, `pow(.; 2)`, `log`, `exp` | fonctions mathématiques |
| `fabs` / `abs` | valeur absolue (`abs` en jq 1.7.1+) |
| `tostring \| .[0:4]` | tronquer un nombre en chaîne |
| `. * 100 \| round / 100` | arrondir à 2 décimales |

## Réductions et fonctions avancées
| Filtre | Effet |
|---|---|
| `reduce .[] as $x (0; . + $x)` | accumulateur (somme ici) |
| `reduce .[] as $i ({}; .[$i.type] += 1)` | compter par type |
| `foreach .[] as $x (0; . + $x)` | comme `reduce` mais émet chaque étape |
| `group_by(.type) \| map({type: .[0].type, n: length})` | compter par groupe |
| `INDEX(.id)` | tableau → objet indexé par `id` |
| `IN(1, 2, 3)` | la valeur fait-elle partie de la liste ? |
| `until(. > 100; . * 2)` | boucle jusqu'à condition |
| `while(. < 100; . * 2)` | émettre tant que la condition est vraie |
| `recurse(.children[]?)` | parcours récursif d'une arborescence |
| `walk(if type == "string" then ascii_downcase else . end)` | transformer toutes les valeurs récursivement |
| `def double: . * 2; map(double)` | définir une fonction |
| `def f($a; $b): $a + $b;` | fonction avec paramètres |
| `try f catch "erreur"` | gérer une erreur |
| `error("message")` | lever une erreur |
| `debug` / `stderr` | afficher la valeur sur stderr (débogage) |
| `input` / `inputs` | lire l'entrée suivante / toutes les entrées restantes |
| `$ENV.HOME` / `env.HOME` | variables d'environnement |
| `$__loc__` | fichier et ligne courants |
| `paths(type == "number")` | chemins des valeurs répondant à une condition |
| `limit(1; .[] \| select(.x))` | s'arrêter au premier résultat |

## Variables depuis le shell
```bash
# Chaîne
jq --arg nom "web01" '.[] | select(.host == $nom)' hosts.json

# Nombre / booléen / objet JSON
jq --argjson port 443 '.[] | select(.port == $port)' services.json

# Variable d'environnement
export SEUIL=80
jq '.[] | select(.cpu > ($ENV.SEUIL | tonumber))' metrics.json

# Plusieurs arguments positionnels
jq -n '$ARGS.positional' --args a b c          # ["a","b","c"]
jq -n '$ARGS.named' --arg x 1 --arg y 2        # {"x":"1","y":"2"}
```
> Attention : ne jamais concaténer une variable shell dans le filtre (`jq ".[] | select(.x == \"$VAR\")"`) : risque d'injection et d'erreurs d'échappement. Utiliser `--arg`.

## Exemples pratiques
```bash
# Joli affichage d'une réponse API
curl -s https://api.github.com/repos/jqlang/jq | jq '.'

# Extraire une valeur brute (sans guillemets) pour un script
VERSION=$(jq -r '.version' package.json)

# Lister les noms des conteneurs Docker en cours
docker ps --format '{{json .}}' | jq -r '.Names'

# Inspecter l'IP d'un conteneur
docker inspect web | jq -r '.[0].NetworkSettings.Networks[].IPAddress'

# Pods Kubernetes non "Running"
kubectl get pods -A -o json \
  | jq -r '.items[] | select(.status.phase != "Running") | "\(.metadata.namespace)/\(.metadata.name)"'

# Instances AWS avec leur nom (tag Name)
aws ec2 describe-instances \
  | jq -r '.Reservations[].Instances[] | [.InstanceId, (.Tags[]? | select(.Key=="Name") | .Value)] | @tsv'

# Outputs Terraform
terraform output -json | jq -r '.ip_publique.value'

# Convertir un tableau d'objets en CSV (avec en-tête)
jq -r '(.[0] | keys_unsorted) as $k | $k, (.[] | [.[$k[]]]) | @csv' data.json > data.csv

# CSV simple vers JSON
jq -R -s 'split("\n") | map(select(length > 0) | split(",")) | .[1:] | map({nom: .[0], age: (.[1] | tonumber)})' data.csv

# Logs JSON (une ligne = un objet) : erreurs uniquement
jq -c 'select(.level == "error")' app.log

# Compter les occurrences par niveau dans un log JSON Lines
jq -s 'group_by(.level) | map({level: .[0].level, total: length})' app.log

# Top 5 des IP dans un log JSON
jq -r '.ip' access.json | sort | uniq -c | sort -rn | head -5

# Fusionner deux fichiers JSON (le second écrase le premier)
jq -s '.[0] * .[1]' base.json override.json

# Fusionner tous les tableaux de plusieurs fichiers
jq -s 'add' fichier1.json fichier2.json

# Modifier un fichier "en place" (jq n'a pas d'option -i)
jq '.version = "2.0.0"' package.json > tmp.json && mv tmp.json package.json
# ou avec sponge (paquet moreutils)
jq '.debug = false' config.json | sponge config.json

# Construire du JSON proprement depuis le shell
jq -n --arg user "$USER" --argjson age 42 '{user: $user, age: $age, date: (now | todate)}'

# Valider qu'un fichier est du JSON correct
jq empty fichier.json && echo "JSON valide"

# Tester une condition dans un script (code retour)
if jq -e '.enabled == true' config.json > /dev/null; then echo "activé"; fi

# Boucler en shell sur des éléments JSON
jq -c '.[]' users.json | while read -r user; do
  nom=$(jq -r '.name' <<< "$user")
  echo "Traitement de $nom"
done

# Lister tous les chemins d'un document (comprendre sa structure)
jq -c 'paths' fichier.json
jq -r '[paths | map(tostring) | join(".")] | unique[]' fichier.json

# Aplatir un JSON imbriqué en clés "a.b.c"
jq '[leaf_paths as $p | {key: ($p | map(tostring) | join(".")), value: getpath($p)}] | from_entries' fichier.json

# Gros fichier : traitement en flux
jq -cn --stream 'fromstream(1 | truncate_stream(inputs))' gros.json
```

## jq sous Windows (PowerShell / cmd)
| Shell | Syntaxe |
|---|---|
| PowerShell 7.3+ | `jq '.items[] \| select(.name == "x")' f.json` (guillemets simples OK) |
| Windows PowerShell 5.1 | `jq '.items[] \| select(.name == \"x\")' f.json` (échapper les `"` internes) |
| cmd.exe | `jq ".items[] \| select(.name == \"x\")" f.json` (guillemets doubles) |
| Toutes | `jq -f filtre.jq f.json` (filtre dans un fichier, évite les soucis d'échappement) |

> Astuce : en PowerShell, `ConvertFrom-Json` / `ConvertTo-Json` sont une alternative native à jq.

## Pièges courants
| Problème | Solution |
|---|---|
| Sortie avec des guillemets dans une variable shell | utiliser `-r` |
| `Cannot index array with "nom"` | ajouter `[]` : `.[].nom` au lieu de `.nom` |
| `Cannot iterate over null` | utiliser `.[]?` ou `// []` |
| `null` au lieu d'une erreur pour une clé absente | c'est normal ; tester avec `has("cle")` |
| Plusieurs résultats au lieu d'un tableau | entourer de `[ ]` ou utiliser `map()` |
| Fichier JSON Lines (un objet par ligne) | jq le gère nativement ; `-s` pour tout regrouper |
| Clé avec tiret ou point | `."ma-cle"` ou `.["ma.cle"]` |
| Fichier vidé par `jq ... f.json > f.json` | passer par un fichier temporaire ou `sponge` |
| Nombres très grands arrondis | jq < 1.7 utilise des doubles ; préférer jq 1.7+ |

## Alternatives et outils associés
| Outil | Usage |
|---|---|
| `yq` | même syntaxe pour YAML / XML / TOML |
| `gojq` | réimplémentation de jq en Go (messages d'erreur plus clairs) |
| `jaq` | clone de jq en Rust, plus rapide |
| `fx` / `jless` | explorateurs JSON interactifs |
| `gron` | aplatir le JSON pour pouvoir le `grep` |
| `jqplay.org` | tester des filtres jq en ligne |

> Astuce : `man jq` ou https://jqlang.org/manual/ pour la référence complète. Construire un filtre progressivement en ajoutant un `| ...` à la fois.
