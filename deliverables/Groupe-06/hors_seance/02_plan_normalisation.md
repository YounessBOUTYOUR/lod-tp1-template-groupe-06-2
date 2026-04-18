# Plan de normalisation et d'identification

## 1. Entités prioritaires

| Type d'entité | Pourquoi est-il prioritaire ? | Identifiant actuel | Stratégie proposée |
| --- | --- | --- | --- |
| **Université** | C'est l'entité faîtière qui regroupe les établissements. Essentiel pour créer la hiérarchie du graphe. | Chaîne de caractères (ex: "Université Cadi Ayyad - Marrakech -") | Créer une URI locale basée sur un nom normalisé, et l'aligner avec l'URI Wikidata ou ROR. |
| **Établissement** | C'est le cœur du jeu de données avec la granularité la plus fine (effectifs, contacts locaux). | Chaîne de caractères + Acronyme (ex: "FMP Rabat" ) | Créer une URI locale unique utilisant l'acronyme normalisé (slug) et la ville. |
| **Ville** | Permet la dimension spatiale des données et l'interconnexion avec d'autres datasets externes. | Chaîne de caractères (ex: "Fès") | Réconcilier avec GeoNames pour utiliser directement les URIs de ce référentiel. |

## 2. Champs à normaliser

| Champ | Problème observé | Règle de normalisation proposée | Impact attendu |
| --- | --- | --- | --- |
| **Université** | Caractères parasites à la fin (ex: " - Rabat -", " - Casablanca - "). | Expression régulière pour supprimer les tirets et espaces finaux : `trim(replace(value, r"\s*-\s*$", ""))`. | Regroupement correct des établissements sous la même entité mère exacte. |
| **Téléphone / Fax** | Formats très hétérogènes : "537772732", espaces "05 24 41 44 98", multiples "0537 7745 48/49/50". | Supprimer les espaces, extraire le premier numéro si multiples, convertir au format international standard E.164 (`+212...`). | Possibilité de traiter la donnée comme une propriété `tel:` valide dans RDF. |
| **Effectifs des Etudiants** | Contient des valeurs non numériques comme "-" ou vides. | Convertir "-" en valeur `null` (ou omettre le triplet) et typer la donnée en entier (`xsd:integer`). | Permet de réaliser des requêtes de calcul (sommes, moyennes) via SPARQL. |
| **Ville** | Espaces superflus (ex: "Al Hoceima ") ou orthographes alternatives ("Berkene" vs Berkane). | Fonction *trim()* pour les espaces, et dictionnaire de remplacement pour standardiser l'orthographe. | Évite la création de deux entités géographiques distinctes pour la même ville. |

## 3. Stratégie d'identifiants

| Entité cible | Base de construction envisagée | Risque | Recommandation |
| --- | --- | --- | --- |
| **Université** | `http://data.gov.ma/resource/university/{slug}` (ex: `.../mohammed-v-rabat`) | Changements de nom institutionnels (ex: fusion d'universités). | Associer systématiquement un `owl:sameAs` vers Wikidata pour maintenir le lien malgré les renommages. |
| **Établissement** | `http://data.gov.ma/resource/establishment/{abr-slug}` (ex: `.../fsjes-agdal-rabat`) | Collisions si l'abréviation est identique entre deux villes (ex: ENCG sans préciser la ville). | Inclure la ville ou l'université dans la clé générant le slug, conformément au champ "Etablissement (Abr)" qui le fait déjà majoritairement. |
| **Ville** | `http://sws.geonames.org/{id}/` | Erreur d'appariement sur un homonyme (ex: une ville étrangère du même nom). | Restreindre le contexte de réconciliation géographique strictement au pays "Maroc". |

## 4. Risques et limites

- **Quels champs sont trop faibles pour servir d'identifiant ?** Le champ `Adresse` est inutilisable comme identifiant (trop de variations textuelles, présence de B.P. au lieu de coordonnées physiques).
- **Quelles collisions ou ambiguïtés sont possibles ?** Plusieurs établissements partagent des acronymes génériques (ex: "FP" pour Faculté Polydisciplinaire, "EST" pour Ecole Supérieure de Technologie). Si le nom de la ville n'est pas suffixé dans la logique d'identifiant, il y aura collision.
- **Quelles informations supplémentaires seraient nécessaires ?** Il manque les coordonnées géospatiales exactes (Latitude/Longitude) pour les campus, l'URL du site web officiel de l'établissement, et idéalement un code administratif unique attribué par le MESRSI.
