# Benchmark des référentiels externes

## Identification

- **Groupe** : 6
- **Jeu de données étudié** : Etablissements de l'enseignement supérieur universitaire public ouverts au titre de l'année 2024-2025
- **Types d'entités retenus** : Université, Établissement, Ville

## 1. Référentiels comparés

| Référentiel | URL | Types d'entités couverts | Intérêt potentiel | Limites observées |
| --- | --- | --- | --- | --- |
| **Wikidata** | `https://www.wikidata.org/` | Université, Établissement, Ville | Hub central du web de données, multilingue (utile pour l'arabe/français), relie de nombreux autres identifiants. | La couverture des instituts secondaires (écoles, facultés spécifiques) est parfois incomplète ou non mise à jour. |
| **ROR (Research Organization Registry)** | `https://ror.org/` | Université, Établissement (Recherche/Enseignement) | Standard international spécifique à l'enseignement supérieur et la recherche. Identifiants uniques et pérennes. | Conçu principalement pour les entités mères (Universités) ; les facultés individuelles n'ont pas toujours leur propre ROR ID. |
| **GeoNames** | `https://www.geonames.org/` | Ville, Région géographique | Base de données géographique mondiale de référence, excellente pour les entités spatiales. | Ne couvre pas les entités institutionnelles (universités). Les limites administratives peuvent parfois différer des usages locaux. |

## 2. Critères de comparaison

Expliquez les critères utilisés pour comparer les référentiels :

- **Couverture** : Capacité du référentiel à inclure l'exhaustivité de nos données marocaines (ex: Wikidata couvre bien les villes, ROR couvre bien les grandes universités).
- **Qualité des identifiants** : Pérennité et standardisation des URIs fournies (les ROR IDs et les entités 'Q' de Wikidata sont d'excellents standards).
- **Précision sémantique** : Capacité à faire la distinction entre l'entité administrative (l'Université) et le bâtiment ou l'entité géographique (le campus ou la ville).
- **Facilité d'appariement** : Présence d'API publiques ou de dumps facilitant la réconciliation semi-automatique (ex: OpenRefine s'intègre nativement avec Wikidata).
- **Richesse des informations disponibles** : Présence de métadonnées complémentaires (coordonnées GPS pour les villes, sites web, acronymes).

## 3. Cas d'appariement manuel

Documentez au moins `6` cas.

| Entité locale | Type | Référentiel cible | Correspondance candidate | Indices utilisés | Niveau de confiance | Décision |
| --- | --- | --- | --- | --- | --- | --- |
| [cite_start]Université Mohammed V - Rabat - [cite: 1] | Université | Wikidata | `Q3109312` (Université Mohammed-V de Rabat) | [cite_start]Nom, Ville ("Rabat" [cite: 1]) | Élevé | Apparié |
| [cite_start]Ecole Mohammadia d'Ingénieurs Rabat [cite: 3] | Établissement | Wikidata | `Q3578187` (École Mohammadia d'ingénieurs) | [cite_start]Nom, Abréviation ("EMI Rabat" [cite: 3]), Ville | Élevé | Apparié |
| [cite_start]Casablanca [cite: 7] | Ville | GeoNames | `2553604` (Casablanca) | Nom, Contexte (Maroc) | Élevé | Apparié |
| [cite_start]Université Hassan II - Casablanca - [cite: 6] | Université | ROR | `https://ror.org/02tr3k616` | [cite_start]Nom de l'université, Ville ("Casablanca" [cite: 7]) | Élevé | Apparié |
| [cite_start]Berkene [cite: 39] | Ville | Wikidata | `Q793312` (Berkane) | Nom (Recherche floue : Berkene / Berkane) | Moyen | Apparié (Nécessite une normalisation orthographique) |
| [cite_start]Faculté des Sciences Semlalia Marrakech [cite: 21] | Établissement | Wikidata | `Q3064506` (Faculté des sciences Semlalia) | [cite_start]Nom, Ville ("Marrakech" [cite: 21]) | Élevé | Apparié |

## 4. Synthèse

- **Quel référentiel paraît le plus utile pour chaque type d'entité ?** Wikidata est le plus polyvalent et couvre particulièrement bien les "Établissements" et les "Universités". GeoNames est le standard incontournable pour les "Villes". ROR est excellent pour identifier de manière univoque l'entité mère ("Université").
- [cite_start]**Quels cas restent ambigus ?** Les entités géographiques avec des variations orthographiques dans le jeu de données (ex: "Berkene" [cite: 39] [cite_start]au lieu de Berkane, ou l'espace dans "Al Hoceima " [cite: 42]). De plus, certaines écoles très récentes ou petites facultés polydisciplinaires n'existent pas dans Wikidata.
- [cite_start]**Quels champs du dataset aident le plus à l'appariement ?** La combinaison des champs `Etablissement (Abr)` [cite: 1] [cite_start](ex: "FSJES Agdal Rabat" [cite: 1]) et `Ville` [cite: 1] est déterminante pour éviter les faux positifs entre établissements de même type situés dans des villes différentes. [cite_start]Le champ en arabe `المؤسسة` [cite: 1] est également un excellent indice pour confirmer l'appariement sur Wikidata via les libellés multilingues.