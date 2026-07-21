# Déterminants de la Violence en France : Approche Économétrique Départementale

## 1. INTRODUCTION

### Cadre de réalisation
Projet d'analyse économétrique et quantitative sur données territoriales françaises, réalisé dans le cadre de l'UE outil informatique pour le traitement des données de mon M1 IREF.

### Présentation du sujet et problématique
L'insécurité et la criminalité en France suscitent de vifs débats publics et politiques, souvent caractérisés par une simplification des facteurs explicatifs. Or, la violence constitue un phénomène complexe qui prend des formes profondément hétérogènes. Ce projet adopte une démarche scientifique et empirique visant à identifier et quantifier les déterminants sociodémographiques, économiques et territoriaux de ces différentes dimensions de la violence à l'échelle des 101 départements français.

### Intérêt et cas d'usage
L'analyse apporte un éclairage statistique rigoureux utile aux analystes de politiques publiques, chercheurs en sciences sociales et décideurs institutionnels, en distinguant l'impact spécifique de la densité urbaine, de la précarité économique et du niveau de formation sur chaque typologie de délinquance.

---

## 2. SOURCES ET DONNÉES

### Origine des données et périmètre
L'étude exploite des données officielles associant les statistiques administratives de l'INSEE, du Ministère de l'Éducation Nationale et du Service Statistique Ministériel de la Sécurité Intérieure (SSMSI). Le périmètre d'analyse couvre l'année 2020 pour l'ensemble de la France métropolitaine (les départements d'Outre-Mer étant exclus pour des raisons d'homogénéité territoriale).


### 1. Variables Explicatives (X) — Causes & Contexte

| Nom Technique | Unité | Description | Source | Lien URL |
| :--- | :--- | :--- | :--- | :--- |
| `densite_population` | **hab/km²** | Densité de population | INSEE | [Lien](https://www.insee.fr/fr/statistiques/5544529?sommaire=5435421#tableau-figure2) |
| `population_insee` | **Nombre** | Population totale du département | INSEE | [Lien](https://www.insee.fr/fr/statistiques/5544529?sommaire=5435421#tableau-figure2) |
| `taux_chomage` | **%** | Part des actifs au chômage | INSEE | [Lien](https://www.insee.fr/fr/statistiques/5391982?sommaire=5392045) |
| `taux_pauvrete_60` | **%** | Part sous le seuil de pauvreté | INSEE | [Lien](https://www.insee.fr/fr/statistiques/6692414?sommaire=6692394#tableau-figure1_radio1) |
| `niveau_vie_median` | **Euros** | Revenu annuel médian | INSEE | [Lien](https://www.insee.fr/fr/statistiques/6692414?sommaire=6692394#tableau-figure1_radio1) |
| `part_jeunes_difficulte_lecture` | **%** | Difficultés de lecture (JDC) | Ministère Éducation | [Lien](https://www.education.gouv.fr/journee-defense-et-citoyennete-2020-pres-d-un-jeune-francais-sur-dix-en-difficulte-de-lecture-323603) |
| `part_non_diplomes` | **%** | Jeunes (15-24 ans) non diplômés | INSEE | [Lien](https://www.insee.fr/fr/statistiques/5020064?sommaire=5040030) |
| `part_moins_25_ans` | **%** | Part de la population < 25 ans | INSEE | [Lien](https://www.insee.fr/fr/statistiques/5544529?sommaire=5435421#tableau-figure2) |
| `part_immigres` | **%** | Part des immigrés | INSEE | [Lien](https://www.insee.fr/fr/statistiques/6793282?sommaire=6793391) |
| `part_descendants_immigres` | **%** | Part des descendants d'immigrés | INSEE | [Lien](https://www.insee.fr/fr/statistiques/6793282?sommaire=6793391) |
| `trafic_stupefiants` | **Taux ‰** | Trafic de stupéfiants | SSMSI | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `usage_stupefiants_total` | **Taux ‰** | Usage de stupéfiants | SSMSI | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |

---

### 2. Variables Dépendantes (Y) — Crimes & Délits

| Nom Technique | Unité | Description | Lien URL |
| :--- | :--- | :--- | :--- |
| `homicides` | **Taux ‰** | Homicides | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `tentatives_homicide` | **Taux ‰** | Tentatives d'homicide | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `violences_physiques_hors_famille` | **Taux ‰** | Violences physiques hors cadre familial | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `violences_physiques_intrafamiliales` | **Taux ‰** | Violences physiques intrafamiliales | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `violences_sexuelles` | **Taux ‰** | Violences sexuelles | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `vols_avec_armes` | **Taux ‰** | Vols commis avec une arme | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `vols_violents_sans_arme` | **Taux ‰** | Vols violents sans arme | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `vols_sans_violence` | **Taux ‰** | Vols sans violence contre des personnes | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `cambriolages_logement` | **Taux ‰** | Cambriolages de logement | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `vols_vehicule` | **Taux ‰** | Vols de véhicule | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `vols_dans_vehicules` | **Taux ‰** | Vols dans les véhicules | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `vols_accessoires_vehicules` | **Taux ‰** | Vols d'accessoires sur véhicules | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `destructions_degradations` | **Taux ‰** | Destructions et dégradations volontaires | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |
| `escroqueries_fraudes` | **Taux ‰** | Escroqueries et fraudes | [Lien](https://www.data.gouv.fr/datasets/bases-statistiques-communale-departementale-et-regionale-de-la-delinquance-enregistree-par-la-police-et-la-gendarmerie-nationales/) |

---

**Référentiel Géographique :** [Départements de France (Etalab)](https://www.data.gouv.fr/datasets/departements-de-france/)

---

## 3. MÉTHODOLOGIE ET DÉTAILS TECHNIQUES

### Pipeline de traitement (ETL)
1. **Fetch (Extraction)** : Ingestion automatisée de fichiers CSV et Excel multi-onglets. Extraction directe de tables HTML via `pandas.read_html` depuis le site de l'INSEE pour les variables de pauvreté et de niveau de vie.
2. **Clean (Normalisation)** :
   * Nettoyage des métadonnées et en-têtes complexes.
   * Harmonisation des codes départements sur deux caractères via la méthode `str.zfill(2)`.
   * Traitement du secret statistique en convertissant la chaîne `'ns'` en valeurs manquantes (`NaN`).
   * Pivotement de la base SSMSI du format long vers un format large (matrice individus-variables).
3. **Structure (Jointure)** :
   * Appariement par jointure gauche sur le code département via le référentiel géospatial d'Etalab.
   * Filtrage strict sur la France métropolitaine (exclusion des codes `97`).
   * Consolidation et exportation de la matrice d'analyse `base_departements_complete_2020.csv`.

### Approche économétrique
Une classification ascendante hiérarchique (méthode de Ward basée sur la matrice de corrélation de Spearman) identifie trois blocs distincts de délinquance. Pour traiter la multicolinéarité et capturer les déterminants propres à chaque dimension, chaque cluster est estimé par régression linéaire (Moindres Carrés Ordinaires - OLS) avec sélection de variables par élimination descendante (Stepwise Backward).

#### Modèle 1 : Cluster Crimes Graves (Focus Homicides)
*   **Spécification** : 
    $$\text{Homicides} = -0.0063 + 0.0022 \times \text{TauxChomage}$$
*   **R²** : $0.182$
*   **Analyse** : Les crimes extrêmes présentent une composante stochastique marquée au niveau départemental. Seul le taux de chômage ressort comme variable explicative statistiquement significative.

#### Modèle 2 : Cluster Tensions Urbaines et Délinquance de Rue
*   **Variable synthétique** : Indice de Tension Urbaine (ITU) construit par agrégation des dégradations et des violences physiques (intrafamiliales et hors famille).
*   **Spécification** : 
    $$\text{ITU} = 0.7967 + 0.8456 \times \ln(\text{DensitePopulation}) + 0.3285 \times \text{PartNonDiplomes}$$
*   **R²** : $0.615$
*   **Analyse** : Le modèle présente une forte puissance explicative. Le niveau de sous-diplômation apparaît comme un prédicteur structurel plus explicatif que le seul revenu monétaire.

#### Modèle 3 : Cluster Vols et Urbanisation
*   **Variable synthétique** : Agrégation des infractions d'appropriation (`Total_Vols`).
*   **Spécification** : 
    $$\text{TotalVols} = -17.4076 + 5.6564 \times \ln(\text{DensitePopulation}) + 0.4666 \times \text{TauxPauvrete}$$
*   **R²** : $0.621$
*   **Analyse** : Les vols répondent à une logique d'opportunité spatiale : ils s'intensifient lorsque la précarité économique s'associe à une forte densité d'opportunités d'infraction.

### Technologies et bibliothèques
*   **Langage** : Python 3.10+
*   **Manipulation et ETL** : `pandas`, `numpy`
*   **Économétrie et Modélisation** : `statsmodels`, `scikit-learn`, `scipy`
*   **Visualisation et Cartographie** : `plotly`, `folium`, `seaborn`, `matplotlib`, `branca`, `ipywidgets`, `itables`

---

## 4. CONCLUSION ET RÉSULTATS CLÉS

### Principaux enseignements
1. **Hétérogénéité des facteurs explicatifs** : Il n'existe pas une cause unique à la délinquance. Les crimes graves relèvent en partie d'une dynamique stochastique, tandis que la délinquance de rue et la délinquance d'appropriation sont fortement structurées par des variables territoriales et socio-économiques.
2. **Rôle prédominant de la densité urbaine** : La concentration spatiale est le premier déterminant des vols et des tensions urbaines.
3. **Impact des déficits de formation** : Le taux de jeunes non diplômés constitue un prédicteur des tensions urbaines supérieur aux seuls indicateurs monétaires.

### Visualisations interactives et interfaces
*   **Diagnostic des modèles** : Module interactif (`ipywidgets` + `Plotly`) permettant de comparer les valeurs observées et ajustées pour évaluer les effets de régression vers la moyenne.
*   **Analyse cartographique** : Cartes choroplèthes interactives (`Folium` + `GeoJSON`) juxtaposant les effectifs bruts et les taux pour 1 000 habitants afin d'éviter les biais d'interprétation liés à la taille des populations.

### Limites et perspectives
*   **Inférence causale vs corrélation** : L'approche par régression MCO (OLS) identifie des associations statistiques et des corrélations structurelles, mais ne permet pas d'établir des relations de causalité strictes en l'absence de variables instrumentales.
*   **Biais de variables omises** : La spécification actuelle ne contrôle pas certaines variables démographiques majeures (ex. la part des hommes jeunes, statistiquement surreprésentés dans les actes criminels), ni des facteurs institutionnels locaux (ex. effectifs de police/gendarmerie ou politiques de prévention ciblées).
*   **Maillage géographique** : Une analyse infra-départementale (niveau communal) permettrait d'atténuer le biais d'agrégation spatiale (*Modifiable Areal Unit Problem - MAUP*) et d'isoler des micro-dynamiques locales.
*   **Autocorrélation spatiale** : L'utilisation de modèles économétriques spatiaux (ex. SAR, SEM ou SDM) permettrait de modéliser les effets de débordement (*spatial spillovers*) et la dépendance géographique entre départements voisins.

---

## 5. STRUCTURE DU DÉPÔT

```text
.
├── DATA_PROJET/                     ----------- # DONNÉES BIENTÔT DISPONIBLES -----------
│   ├── base_departements_complete_2020.csv  # Matrice d'analyse nettoyée et consolidée
│   ├── departements-france.csv             # Référentiel géospatial officiel
│   └── [Sources brutes INSEE/SSMSI]        # Données brutes et extractions d'origine 
├── violence.ipynb                           # Notebook principal (ETL, économétrie, cartes)
└── README.md                                # Documentation du projet
```

---
