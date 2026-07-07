# Déterminants de la Violence en France : Approche Économétrique Départementale

## 1. Introduction & Problématique

L’insécurité et la violence en France occupent une place centrale dans le débat public. Pourtant, ces phénomènes souffrent souvent de simplifications ou de récupérations politiques. 

Ce projet adopte une **approche rigoureuse et scientifique** pour identifier et quantifier les facteurs sociodémographiques, économiques et territoriaux qui influencent différents types de violences à l'échelle des 101 départements français.

---

## 2. Architecture des Données

### A. Variables Explicatives (X) — Causes & Contexte

| Nom Technique | Unité | Description | Source |
| :--- | :---: | :--- | :---: |
| `densite_population` | hab/km² | Densité de population | INSEE |
| `population_insee` | Nombre | Population totale du département | INSEE |
| `taux_chomage` | % | Part des actifs au chômage | INSEE |
| `taux_pauvrete_60` | % | Part sous le seuil de pauvreté (à 60%) | INSEE |
| `niveau_vie_median` | Euros | Revenu annuel médian | INSEE |
| `part_jeunes_difficulte_lecture` | % | Difficultés de lecture décelées (JDC) | Min. Éduc. |
| `part_non_diplomes` | % | Jeunes (15-24 ans) non diplômés et non scolarisés | INSEE |
| `part_moins_25_ans` | % | Part de la population âgée de moins de 25 ans | INSEE |
| `part_immigres` | % | Part des immigrés dans la population | INSEE |
| `part_descendants_immigres` | % | Part des descendants d'immigrés | INSEE |
| `trafic_stupefiants` | Taux ‰ | Taux de faits constatés pour trafic de stupéfiants | SSMSI |
| `usage_stupefiants_total` | Taux ‰ | Taux de faits constatés pour usage de stupéfiants | SSMSI |

### B. Variables Dépendantes (Y) — Crimes & Délits

Toutes les variables de criminalité proviennent du SSMSI et sont exprimées en **Taux pour 1 000 habitants** (données 2020) afin de neutraliser l'effet de taille de la population.

* **Crimes Graves :** `homicides`, `tentatives_homicide`, `violences_sexuelles`.
* **Atteintes aux Personnes :** `violences_physiques_hors_famille`, `violences_physiques_intrafamiliales`.
* **Atteintes aux Biens :** `vols_avec_armes`, `vols_violents_sans_arme`, `vols_sans_violence`, `cambriolages_logement`, `vols_vehicule`, `vols_dans_vehicules`, `vols_accessoires_vehicules`.
* **Autres :** `destructions_degradations`, `escroqueries_fraudes`.

---

## 3. Pipeline de Traitement des Données (ETL)

Le projet suit un pipeline modulaire prévisible divisé en quatre phases principales :


[1. Fetch (Extraction)] ──> [2. Clean (Normalisation)] ──> [3. Structure (Jointure)] ──> [4. Model (Régression OLS)]


### Phase 1 : Fetch (Extraction)
* Centralisation de sources hétérogènes (fichiers CSV, extractions Excel multi-onglets).
* Requêtes de tables HTML automatisées (via `pd.read_html`) directement depuis le site de l'INSEE pour récupérer le niveau de vie médian et le taux de pauvreté.

### Phase 2 : Clean (Normalisation)
* Suppression des en-têtes et lignes de commentaires spécifiques aux formats de publication de l'INSEE.
* Standardisation des identifiants territoriaux via la méthode `str.zfill(2)` pour harmoniser les codes départements (ex: le code `1` devient `01`).
* Gestion des valeurs masquées par le secret statistique en forçant la conversion des chaînes `'ns'` en `NaN`.
* Pivotement de la base SSMSI pour transformer une structure longue en matrice d'analyse large (une colonne distincte par indicateur criminel).

### Phase 3 : Structure (Jointure)
* Jointure gauche systématique (`merge`) basée sur les référentiels géographiques officiels d'Etalab.
* Filtrage de la portée de l'étude à la France métropolitaine par l'exclusion stricte des codes commençant par `97`.
* Réorganisation logique des colonnes : *Métadonnées géographiques* ──> *Variables Explicatives (X)* ──> *Variables Expliquées (Y)*.
* Sauvegarde et persistance de la structure nettoyée dans un fichier unique nommé `base_departements_complete_2020.csv`.

---

## 4. Approche Méthodologique & Modélisation

Une classification hiérarchique (méthode de Ward basée sur des corrélations de Spearman) a mis en évidence trois pôles distincts de délinquance. Pour éviter les biais de multicolinéarité et capturer les dynamiques réelles, chaque pôle est modélisé de manière indépendante via des régressions linéaires par moindres carrés ordinaires (OLS). Les variables finales ont été sélectionnées par procédure **Stepwise (Backward Elimination)**.

### [Modèle 1] Cluster Crimes Graves (Focus Homicides)
Les violences extrêmes s'avèrent hautement stochastiques à l'échelle départementale ($R^2 = 0.182$). Seul le chômage émerge comme signal prédictif significatif.

> **Équation estimée :**
> $$homicides = -0.0063 + 0.0022 \times taux\_chomage$$

### [Modèle 2] Cluster Tensions Urbaines et Délinquance de Rue
Création de l'*Indice de Tension Urbaine (ITU)* par sommation des variables cohérentes du bloc (`dégradations` + `violences physiques intrafamiliales et hors famille`).

> **Équation estimée :**
> $$ITU = 0.7967 + 0.8456 \times \ln(densite\_population) + 0.3285 \times part\_non\_diplomes$$

* **Performance :** $R^2 = 0.615$ (Modèle statistiquement très robuste).
* **Enseignement :** Le déficit de formation (absence de diplôme) est le prédicteur social le plus puissant, surpassant l'impact du revenu seul.

### [Modèle 3] Cluster Vols et Urbanisation
Agrégation des infractions d'appropriation sous la variable unique `Total_Vols` afin de stabiliser le signal en lissant la volatilité et le bruit local.

> **Équation estimée :**
> $$Total\_Vols = -17.4076 + 5.6564 \times \ln(densite\_population) + 0.4666 \times taux\_pauvrete\_60$$

* **Performance :** $R^2 = 0.621$.
* **Enseignement :** Le modèle décrit une mécanique d'opportunité spatiale pure. La délinquance d'appropriation se cristallise là où la précarité économique (+4.67 vols pour 10 points de pauvreté) rencontre la concentration de cibles potentielles engendrée par la densité urbaine.

---

## 5. Visualisations Dynamiques

Le notebook intègre des composants d'interface utilisateur avancés pour explorer les résultats :

* **Analyse de performance interactive :** Un menu déroulant `ipywidgets` lié à des graphiques `Plotly` permettant de confronter graphiquement les valeurs réelles aux valeurs prédites par les modèles. Cela met en évidence l'effet de *shrinkage* (régression vers la moyenne) sur les variables à faible pouvoir explicatif.
* **Cartographie interactive double :** Cartes choroplèthes propulsées par `Folium` et structurées via un fichier GeoJSON simplifié de la France. L'affichage juxtapose systématiquement la carte des **effectifs bruts** (naturellement biaisée par la démographie) et la carte des **taux pour 1 000 habitants**, permettant une lecture objective de l'insécurité territoriale.

---

## 6. Structure du Dépôt


```text
.
├── DATA_PROJET/
│   ├── base_departements_complete_2020.csv  # Données néttoyées et prêtes à l'emploi, exportées en csv.
│   ├── departements-france.csv             # Référentiel géographique.
│   └── [Fichiers sources bruts INSEE/SSMSI] # Fichiers de données bruts.
├── violence.ipynb                           # Notebook principal de production.
└── README.md                                # Présentation du projet.                                    

```

---

## 7. Spécifications Techniques

* **Langage maître :** Python 3.10+
* **Modélisation Économétrique :** Librairie `statsmodels` (analyses OLS exhaustives et extraction des summaries de performance)
* **Visualisation Spatiale et Graphique :** `folium`, `branca`, `plotly`, `seaborn` et `matplotlib`
* **Formatage des Tableaux :** `itables` pour un rendu HTML dynamique au sein de l'environnement Jupyter.





