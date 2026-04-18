# Plan de normalisation et d'identification

## 1. Entités prioritaires

| Type d'entité | Pourquoi est-il prioritaire ? | Identifiant actuel | Stratégie proposée |
| --- | --- | --- | --- |
| **Université** | C'est l'entité faîtière qui regroupe les établissements. Essentiel pour créer la hiérarchie du graphe. | [cite_start]Chaîne de caractères (ex: "Université Cadi Ayyad - Marrakech -" [cite: 19]) | Créer une URI locale basée sur un nom normalisé, et l'aligner avec l'URI Wikidata ou ROR. |
| **Établissement** | C'est le cœur du jeu de données avec la granularité la plus fine (effectifs, contacts locaux). | [cite_start]Chaîne de caractères + Acronyme (ex: "FMP Rabat" [cite: 2]) | Créer une URI locale unique utilisant l'acronyme normalisé (slug) et la ville. |
| **Ville** | Permet la dimension spatiale des données et l'interconnexion avec d'autres datasets externes. | [cite_start]Chaîne de caractères (ex: "Fès" [cite: 14]) | Réconcilier avec GeoNames pour utiliser directement les URIs de ce référentiel. |

## 2. Champs à normaliser

| Champ | Problème observé | Règle de normalisation proposée | Impact attendu |
| --- | --- | --- | --- |
| **Université** | [cite_start]Caractères parasites à la fin (ex: " - Rabat -" [cite: 1, 2][cite_start], " - Casablanca - " [cite: 6]). | Expression régulière pour supprimer les tirets et espaces finaux : `trim(replace(value, r"\s*-\s*$", ""))`. | Regroupement correct des établissements sous la même entité mère exacte. |
| **Téléphone / Fax** | [cite_start]Formats très hétérogènes : "537772732" [cite: 1][cite_start], espaces "05 24 41 44 98" [cite: 20][cite_start], multiples "0537 7745 48/49/50"[cite: 6]. | Supprimer les espaces, extraire le premier numéro si multiples, convertir au format international standard E.164 (`+212...`). | Possibilité de traiter la donnée comme une propriété `tel:` valide dans RDF. |
| **Effectifs des Etudiants** | [cite_start]Contient des valeurs non numériques comme "-" [cite: 5] ou vides. | Convertir "-" en valeur `null` (ou omettre le triplet) et typer la donnée en entier (`xsd:integer`). | Permet de réaliser des requêtes de calcul (sommes, moyennes) via SPARQL. |
| **Ville** | [cite_start]Espaces superflus (ex: "Al Hoceima " [cite: 42][cite_start]) ou orthographes alternatives ("Berkene" [cite: 39] vs Berkane). | Fonction *trim()* pour les espaces, et dictionnaire de remplacement pour standardiser l'orthographe. | Évite la création de deux entités géographiques distinctes pour la même ville. |

## 3. Stratégie d'identifiants

| Entité cible | Base de construction envisagée | Risque | Recommandation |
| --- | --- | --- | --- |
| **Université** | `http://data.gov.ma/resource/university/{slug}` (ex: `.../mohammed-v-rabat`) | Changements de nom institutionnels (ex: fusion d'universités). | Associer systématiquement un `owl:sameAs` vers Wikidata pour maintenir le lien malgré les renommages. |
| **Établissement** | `http://data.gov.ma/resource/establishment/{abr-slug}` (ex: `.../fsjes-agdal-rabat`) | Collisions si l'abréviation est identique entre deux villes (ex: ENCG sans préciser la ville). | [cite_start]Inclure la ville ou l'université dans la clé générant le slug, conformément au champ "Etablissement (Abr)" [cite: 1] qui le fait déjà majoritairement. |
| **Ville** | `http://sws.geonames.org/{id}/` | Erreur d'appariement sur un homonyme (ex: une ville étrangère du même nom). | Restreindre le contexte de réconciliation géographique strictement au pays "Maroc". |

## 4. Risques et limites

- [cite_start]**Quels champs sont trop faibles pour servir d'identifiant ?** Le champ `Adresse` [cite: 1] [cite_start]est inutilisable comme identifiant (trop de variations textuelles, présence de B.P. au lieu de coordonnées physiques [cite: 1]).
- [cite_start]**Quelles collisions ou ambiguïtés sont possibles ?** Plusieurs établissements partagent des acronymes génériques (ex: "FP" pour Faculté Polydisciplinaire [cite: 16][cite_start], "EST" pour Ecole Supérieure de Technologie [cite: 4]). Si le nom de la ville n'est pas suffixé dans la logique d'identifiant, il y aura collision.
- **Quelles informations supplémentaires seraient nécessaires ?** Il manque les coordonnées géospatiales exactes (Latitude/Longitude) pour les campus, l'URL du site web officiel de l'établissement, et idéalement un code administratif unique attribué par le MESRSI.