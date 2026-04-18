# Fiche d'analyse du jeu de données

## Identification

- **Groupe** : 6
- **Date** : 15/04/2026
- **Nom du jeu de données** : higher_education_sample.
- **Source / producteur** : Le portail open data national du Maroc. La source est déduite du ``Portal_hint`` : data.gov.ma.
- **URL de la source** : Non spécifié.
- **Domaine métier** : l'éducation.

## Description générale

- **Objectif du jeu de données** :présenter quelques établissements de l'enseignement supérieur universitaire public.
- **Format(s) disponible(s)** : csv.
- **Langue(s)** : français et anglais. Les noms des établissements sont en français, tandis que les autre colonnes sont en anglais.
- **Licence** : Non spécifiée.
- **Fréquence de mise à jour** : Non spécifiée.
- **Unité d'observation** : Les établissements d'enseignement supérieur.
- **Granularité des enregistrements** : Un établissement par enregistrement.

## Structure du jeu de données

| Champ / colonne | Signification | Exemple de valeur | Observation |
| --- | --- | --- | --- |
| record_id | Identifiant de l'enregistrement | 1 | C'est la clé primaire de la table |
| institution_name | Nom complet de l'établissement | Universite Hassan 1er | c'est la seule colonne dont les valeurs sont en français |
| short_name | Acronyme de l'établissement | ENSIAS | Aucune |
| city | Ville où se trouve l'établissement | Rabat | Aucune |
| country | Pays où se trouve l'établissement | Morocco | la colonne est de valeur constante dans cet échantillon. |
| website | URL du site institutionnel | https://www.ensias.um5.ac.ma | Aucune |
| institution_type | Statut de l'établissement | public school | Aucune |
| main_field | Domaine de spécialité | computer science | Aucune |
| year_created | Année de création | 1992 | Aucune |
| portal_hint | Indication sur le portail source | data.gov.ma | Utile pour la traçabilité de la donnée |

## Évaluation "Open Data"

| Critère | Observation | Score / 5 |
| --- | --- | --- |
| Accessibilité | Fichier CSV facilement lisible par une machine | 5 |
| Réutilisabilité | Structure tabulaire claire et exploitable immédiatement, mais nécessite un nettoyage préalable (harmonisation des langues, ajout de coordonnées spatiales) pour des usages avancés. | 4 |
| Documentation | Aucune métadonnée n'est incluse dans le fichier, sauf le READMED.pdf qui introduit un peu le contexte | 2 |
| Format exploitable | Le format CSV est un standard non propriétaire | 5 |
| Présence d'une licence | Aucune mention de licence dans les données | 0 |

## Évaluation "Linked Data readiness"

| Point observé | Oui / Non / Partiel | Commentaire |
| --- | --- | --- |
| Identifiants stables pour les enregistrements | Partiel | ``record_id`` est stable mais purement local à ce fichier |
| Entités clairement distinguables | Oui | Chaque établissement a son propre nom, acronyme et site web |
| Valeurs normalisées | Oui | Les valeurs des attributs comme ``institution_type``, ``main_field``,  semblent récurrents (par exemple "multidisciplinary" apparaît souvent ) |
| Informations géographiques exploitables | Partiel | Seuls le pays et la ville sont présents, on n'a pas les données suffisantes pour bien localiser les établissement |
| Possibilité de lien vers des référentiels externes | Oui | Les URL des sites web et les noms permettent un alignement futur avec des bases de connaissances comme DBpedia par exemple |
| Présence de clés candidates plausibles | Oui | L'URL du site web ou le nom court sont de bons candidats |
| Présence de codes ou nomenclatures réutilisables | Non | Le fichier utilise du texte libre (par exemple "Morocco" au lieu de MA/MAR) plutôt que des codes standards internationaux |

## Maturité "5 étoiles"

| Niveau | Atteint ? | Justification |
| --- | --- | --- |
| 1 étoile : données disponibles sur le Web | Oui | La mention de l'indice data.gov.ma |
| 2 étoiles : données structurées | Oui | Le fichier est un tableau organisé en lignes et en colonnes. |
| 3 étoiles : format non propriétaire | Oui | Le format CSV utilisé est un format non propriétaire. |
| 4 étoiles : usage d'URI pour identifier les choses | Non | Les éléments sont identifiés par du texte |
| 5 étoiles : liens vers d'autres données | Non | Il n'y a aucun lien explicite vers d'autres jeux de données externes |

## Clés candidates et identifiants

| Objet ou entité cible | Champ(s) candidat(s) | Fiabilité | Limites | Recommandation |
| --- | --- | --- | --- | --- |
| Établissement | record_id | Haute | Inutilisable en dehors du jeux de données | À conserver comme clé primaire interne pour organiser les données. |
| Établissement | short_name | Moyenne | Les acronymes peuvent être ambigus ou dupliqués à l'échelle internationale. | À utiliser pour la recherche utilisateur, mais pas comme clé primaire globale. |
| Établissement | website | Haute | Les URL peuvent expirer, changer de domaine ou rediriger avec le temps | à utiliser comme clé primaire |

## Normalisation préalable nécessaire

| Champ | Problème observé | Impact pour le LOD | Action recommandée |
| --- | --- | --- | --- |
| city et country | Texte libre en anglais. | Rend les requêtes spatiales et les jointures géographiques complexes | Aligner ces colonnes avec un référentiel géographique comme Wikidata pour obtenir des URI. |
| institution_type et main_field | Typologies définies non standarisées ("engineering school", "applied sciences") | Freine l'interopérabilité sémantique avec des jeux de données internationaux sur l'éducation. | Faire correspondre ces valeurs avec un vocabulaire standardisé reconnu dans le domaine éducatif. |
## Qualité des données

- **Forces observées** : La structure est constante, il n'y a aucune valeur nulle ou manquante dans cet échantillon, et les types de données sont respectés.
- **Faiblesses observées** : La langue est inconsistante (noms en français, catégories en anglais) , et l'absence totale de documentation/métadonnées diminue le contexte.
- **Ambiguïtés ou doublons potentiels** : Ambiguïté des types d'établissements : La colonne institution_type n'est pas standardisée. Les valeurs comme "public school", "engineering school" et "faculty" se chevauchent sémantiquement.
- **Champs manquants pour une meilleure interconnexion** : Coordonnées géographiques, codes internationaux pour les pays/villes.
- **Risque principal pour une future mise en relation** : L'incapacité à faire la distinction entre l'établissement physique et l'entité administrative sans modélisation ontologique plus poussée.

## Conclusion courte

En `8 à 12` lignes, expliquez si ce jeu de données constitue une bonne base pour une future publication en données ouvertes liées, à quelles conditions, et quelles actions techniques devraient être menées en priorité.

Ce jeu de données constitue une base de départ correcte, mais insuffisante en l'état pour une publication immédiate en données ouvertes liées. Sa structure tabulaire en format CSV , exempte de valeurs manquantes dans cet échantillon, garantit l'atteinte du niveau 3 étoiles du modèle de maturité de Tim Berners-Lee. Cependant, l'absence totale des URIs et de liens vers des référentiels externes (comme Wikidata pour les établissements ou GeoNames pour les villes) bloque le passage aux niveaux supérieurs (4 et 5 étoiles). Pour y remédier, la priorité technique absolue est la normalisation sémantique : il est impératif de transformer les chaînes de caractères littérales des colonnes city, country, et institution_type  en entités liées à des ontologies standards. Enfin, un nettoyage linguistique s'impose pour corriger l'absence d'accents sur les noms français et harmoniser l'usage hybride franco-anglais observé.