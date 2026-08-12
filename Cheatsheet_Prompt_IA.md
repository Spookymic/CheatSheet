# Cheatsheet Prompt IA

## Principes de base
1. **Soyez précis** : une demande vague donne une réponse vague.
2. **Donnez du contexte** : rôle, objectif, contraintes, public.
3. **Structurez** : séparez instruction / contexte / format attendu.
4. **Itérez** : affinez en fonction des réponses obtenues.
5. **Explicitez le format** : liste, tableau, code, résumé...

## Le cadre STAR / RACI adapté au prompt
| Élément | Question à se poser |
|---|---|
| **Rôle** | Qui doit jouer l'IA ? (expert, relecteur, formateur...) |
| **Tâche** | Que doit-elle faire exactement ? |
| **Contexte** | Quelles informations lui donner ? |
| **Format** | Quelle forme pour la réponse ? |
| **Contraintes** | Limites : longueur, ton, interdits... |

### Exemple de prompt structuré
```
Tu es un expert en rédaction technique. (Rôle)
Rédige une documentation pour utiliser la commande docker ps. (Tâche)
Public : développeurs débutants. (Contexte)
Utilise des tableaux Markdown. (Format)
Maximum 300 mots, ton neutre et simple. (Contraintes)
```

## Types de prompts
| Type | Description | Exemple |
|---|---|---|
| Instruction directe | demande simple | « Résume ce texte » |
| Rôle | persona imposé | « Agis comme un avocat » |
| Chat-contexte | dialoguer avec historique | « Précise ce point » |
| Réflexion (CoT) | demander de raisonner pas à pas | « Explique ton raisonnement étape par étape » |
| Few-shot | donner des exemples | « Voici 2 exemples, fais pareil » |
| Structuration | imposer un format de sortie | « Réponds en JSON avec les champs x, y » |

## Techniques avancées
| Technique | Astuce |
|---|---|
| **CoT (Chaîne de pensée)** | « Raisonne étape par étape avant de conclure » |
| **Few-shot** | fournir 1 à 3 exemples de la réponse attendue |
| **Rôles multiples** | « Joue le rédacteur puis le critique » |
| **Contraintes négatives** | « Ne fais pas ça, évite ceci » |
| **Amorces** | terminer par « La réponse est : » |
| **Persona** | définir un expert précis avec compétences |
| **Budget** | « Réponds en 5 phrases max », « 200 mots » |
| **Question inverse** | « Pose-moi des questions pour clarifier avant de répondre » |
| **Vérification** | « Relis et corrige ton texte » / « Critique ton propre travail » |
| **Itération** | « Améliore : plus concis, plus formel, avec exemples » |

## Structure de la réponse attendue
| Format | Usage |
|---|---|
| Tableau Markdown | comparaison de données |
| Liste à puces | instructions / résumé |
| Code avec langage | extraits de code ` ```python ` |
| JSON / YAML | données structurées |
| Paragraphe structuré | texte avec titres |
| Diagramme Mermaid | schéma/flow |

## Exemples de prompts réutilisables
### Résumé
> Résume ce document en 5 points clés. Pour chaque point, donne une phrase de max 25 mots. Public : direction.

### Relecture
> Relis ce texte et corrige : fautes, clarté, ton. Donne la version corrigée + la liste des changements.

### Génération de code
> Écris une fonction Python qui valide une adresse e-mail avec regex. Ajoute la gestion des erreurs. Suis le style PEP8. Ajoute 3 tests.

### Explication
> Explique le principe de la pile réseau TCP/IP comme si j'avais 12 ans, avec une analogie de la vie quotidienne.

### Traduction technique
> Traduis ce texte du français vers l'anglais technique en conservant la terminologie exacte. Liste les termes ambigus.

### Plan de projet
> Crée un plan de projet pour [tâche]. Structure : objectifs, étapes, jalons, risques, ressources. Format tableau.

## Erreurs courantes
| Erreur | Correction |
|---|---|
| Prompt trop vague | préciser le résultat attendu |
| Oublier le contexte | donner le public, le but, les contraintes |
| Demander plusieurs choses confondues | séparer les demandes |
| Accepter la 1re réponse | itérer, demander des variations |
| Ne pas préciser le format | imposer la structure de sortie |
| Surcharger d'informations | aller à l'essentiel, sans noyer |

## Itération efficace
1. Demande initiale.
2. Évaluation (vrai/faux, complet, utilisable ?).
3. Reformulation ciblée : « Plus court », « Avec un exemple », « Dans un tableau », « Sans jargon ».
4. Si nécessaire, demander une alternative : « Propose une autre version ».
5. Validation finale par l'humain : **toujours relire**.

## Check-list d'un bon prompt
- [ ] Rôle défini
- [ ] Objectif clair
- [ ] Contexte fourni
- [ ] Format précisé
- [ ] Contraintes données (longueur, ton, interdits)
- [ ] Exemples si nécessaire
- [ ] Demande de vérification / itération prévue
