# Rapport hors séance - TP1

## Page de garde

- **Module** : Linked Open Data
- **TP** : TP1 - Introduction aux données ouvertes liées
- **Groupe** : 6
- **Étudiants** : Soufiane Eloulji - Mouhsine Elaoula
- **Date** : 18/04/2026

## Consigne

Ce rapport est à remettre **avant la prochaine séance**. Il constitue une **étude distincte** du travail réalisé pendant le TP1 en classe et prépare directement le TP2.

## 1. Introduction

Dans le cadre du cours sur le Linked Open Data, ce travail préparatoire porte sur le jeu de données des établissements universitaires publics marocains (2024-2025). L'objectif est de transformer un tableau de données plat (format CSV/XLSX) en un graphe de données exploitable. Le problème d'ingénierie soulevé ici est classique : l'absence d'identifiants uniques standardisés (URI) dans la source originale et la présence de données non normalisées. Nous nous concentrons sur la création d'une stratégie d'identification pour trois entités fondamentales : l'Université, l'Établissement et la Ville.

## 2. Types d'entités étudiés

Les trois types d'entités retenus sont :
- **L'Université** : Entité administrative de niveau supérieur.
- **L'Établissement** : Entité locale dispensant l'enseignement (ex: Faculté, École d'ingénieurs).
- **La Ville** : Entité géographique.

Ce choix s'explique par la nature intrinsèquement hiérarchique et spatiale du domaine de l'enseignement supérieur. [cite_start]Pour les apparier, nous disposons des champs textuels en français et en arabe ("المؤسسة" [cite: 1][cite_start], "الجامعة" [cite: 1][cite_start]), des acronymes ("Etablissement (Abr)" [cite: 1][cite_start]), ainsi que du nom de la ville ("Ville" [cite: 1]).

## 3. Benchmark des référentiels externes

Nous avons évalué trois référentiels majeurs :
- **Wikidata** : Offre une couverture très large, une précision satisfaisante pour le Maroc et une grande richesse sémantique (labels multilingues, types de bâtiments). Les identifiants (Q-ids) sont très robustes.
- **ROR (Research Organization Registry)** : C'est le standard de l'industrie pour la recherche institutionnelle. La qualité des identifiants est parfaite, mais la couverture se limite souvent aux "Universités", omettant la granularité des facultés individuelles.
- **GeoNames** : Essentiel pour le maillage territorial, il offre une résolution spatiale précise pour les "Villes", facilitant grandement la désambiguïsation géographique.

## 4. Cas d'appariement manuel

[cite_start]Le cas le plus significatif est l'appariement de l'établissement local `"Ecole Mohammadia d'Ingénieurs Rabat"`[cite: 3].
- **Référentiel cible** : Wikidata
- **Indices utilisés** : Nom exact, ville, acronyme (EMI).
- **Niveau de confiance** : Élevé.
- **Décision finale** : Alignement avec l'entité Wikidata `Q3578187`.

[cite_start]Une ambiguïté notable a été repérée avec la ville de `"Berkene"`[cite: 39]. Le nom est orthographié avec un 'e' au lieu du classique 'a' ("Berkane"). L'alignement sur l'entité Wikidata `Q793312` (Berkane) nécessite une étape de normalisation préalable.

## 5. Plan de normalisation et d'identification

Avant toute modélisation RDF (TP2), plusieurs transformations sont impératives :
1. [cite_start]**Nettoyage textuel** : Retrait des espaces et tirets de décoration (ex: "Université Mohammed V - Rabat -" [cite: 1, 2] doit devenir "Université Mohammed V").
2. [cite_start]**Normalisation des formats** : Harmonisation des téléphones pour en extraire des valeurs conformes au standard E.164, et remplacement des tirets par des valeurs nulles dans la colonne des effectifs[cite: 5].
3. **Création d'URIs** :
   - Universités : `http://data.gov.ma/resource/university/{slug}`
   - Établissements : `http://data.gov.ma/resource/establishment/{slug-abbreviation}`

Ces identifiants locaux stables seront interconnectés via la propriété `owl:sameAs` vers Wikidata/GeoNames.

## 6. Analyse des risques

Le risque principal est le **faux appariement** (homonymie), en particulier pour les villes (ex: des villes portant le même nom dans d'autres pays). L'utilisation du contexte ("Maroc") lors de l'appel aux API de réconciliation est vitale.
[cite_start]Les limites résident dans l'absence de certains petits établissements très récents dans Wikidata (ex: "Ecole Supérieure de l'Education et de la Formation Oujda" [cite: 39] - établissement nouvellement créé). Il manque également dans le jeu source un code UAI ou un identifiant interne propre au MESRSI qui aurait sécurisé l'architecture.

## 7. Difficultés rencontrées

L'obstacle majeur est **l'absence totale d'identifiants techniques** dans le fichier source ; tout repose sur des chaînes de caractères.
De plus, la qualité de certaines valeurs est inégale :
- [cite_start]Le champ "Téléphone" contient parfois plusieurs numéros séparés par des " / " ("0537 7745 48/49/50" [cite: 6]).
- [cite_start]Présence d'espaces blancs invisibles à la fin des chaînes ("Al Hoceima " [cite: 42]).
- [cite_start]Données manquantes sur les effectifs ("-" au lieu de 0 ou vide [cite: 5]).

## 8. Conclusion

Cette étude préparatoire démontre que le jeu de données, bien que structuré sous forme tabulaire, est sémantiquement pauvre en l'état. L'identification rigoureuse des stratégies d'URI et la définition des règles de nettoyage sont des prérequis absolus. Ce travail garantit que lors de la phase de transformation (TP2), le graphe RDF généré sera cohérent, interconnectable avec le Web de Données mondiales, et prêt pour des requêtes SPARQL fiables.