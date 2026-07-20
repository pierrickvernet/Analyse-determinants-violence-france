# DÉTERMINANTS DE LA VIOLENCE EN FRANCE : APPROCHE ÉCONOMÉTRIQUE DÉPARTEMENTALE

## 1. INTRODUCTION

### Cadre de réalisation
Projet d'analyse économétrique et quantitative sur données territoriales françaises.

### Présentation du sujet et problématique
L'insécurité et la criminalité en France suscitent de vifs débats publics et politiques, souvent caractérisés par une simplification des facteurs explicatifs. Ce projet adopte une démarche scientifique et empirique visant à identifier et quantifier les déterminants sociodémographiques, économiques et territoriaux de la délinquance à l'échelle des 101 départements français.

### Intérêt et cas d'usage
L'analyse apporte un éclairage statistique rigoureux utile aux analystes de politiques publiques, chercheurs en sciences sociales et décideurs institutionnels, en distinguant l'impact spécifique de la densité urbaine, de la précarité économique et du niveau de formation sur chaque typologie de délinquance.

---

## 2. SOURCES ET DONNÉES

### Origine des données et périmètre
L'étude exploite des données officielles associant les statistiques administratives de l'INSEE, du Ministère de l'Éducation Nationale et du Service Statistique Ministériel de la Sécurité Intérieure (SSMSI). Le périmètre d'analyse couvre l'année 2020 pour l'ensemble de la France métropolitaine (les départements d'Outre-Mer étant exclus pour des raisons d'homogénéité territoriale).

### Variables explicatives (X)
*   **densite_population** : Densité de population (habitants / km²) [INSEE]
*   **population_insee** : Population totale du département [INSEE]
*   **taux_chomage** : Part des actifs au chômage (%) [INSEE]
*   **taux_pauvrete_60%** : Taux de pauvreté au seuil de 60% du revenu médian (%) [INSEE]
*   **niveau_vie_median** : Niveau de vie médian annuel (Euros) [INSEE]
*   **part_jeunes_difficulte_lecture** : Part des jeunes en difficulté de lecture à la JDC (%) [Ministère de l'Éducation Nationale]
*   **part_non_diplomes** : Part des 15-24 ans non diplômés et non scolarisés (%) [INSEE]
*   **part_moins_25_ans** : Part de la population âgée de moins de 25 ans (%) [INSEE]
*   **part_immigres** : Part des immigrés dans la population totale (%) [INSEE]
*   **part_descendants_immigres** : Part des descendants d'immigrés (%) [INSEE]
*   **trafic_stupefiants** : Taux de faits constatés pour trafic de stupéfiants (‰) [SSMSI]
*   **usage_stupefiants_total** : Taux de faits constatés pour usage de stupéfiants (‰) [SSMSI]

### Variables dépendantes (Y)
Afin de neutraliser l'effet de taille démographique, l'ensemble des indicateurs de criminalité est exprimé en taux pour 1 000 habitants (données 2020) :
*   **Crimes Graves** : Homicides, tentatives d'homicide, violences sexuelles.
*   **Atteintes aux Personnes** : Violences physiques intrafamiliales, violences physiques hors famille.
*   **Atteintes aux Biens** : Vols avec armes, vols violents sans arme, vols sans violence, cambriolages de logement, vols de véhicule, vols dans les véhicules, vols d'accessoires sur véhicules.
*   **Autres infractions** : Destructions et dégradations, escroqueries et fraudes.

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
    $$\text{homicides} = -0.0063 + 0.0022 \times \text{taux\_chomage}$$
*   **R²** : $0.182$
*   **Analyse** : Les crimes extrêmes présentent une composante stochastique marquée au niveau départemental. Seul le taux de chômage ressort comme variable explicative statistiquement significative.

#### Modèle 2 : Cluster Tensions Urbaines et Délinquance de Rue
*   **Variable synthétique** : Indice de Tension Urbaine (ITU) construit par agrégation des dégradations et des violences physiques (intrafamiliales et hors famille).
*   **Spécification** : 
    $$\text{ITU} = 0.7967 + 0.8456 \times \ln(\text{densite\_population}) + 0.3285 \times \text{part\_non\_diplomes}$$
*   **R²** : $0.615$
*   **Analyse** : Le modèle présente une forte puissance explicative. Le niveau de sous-diplômation apparaît comme un prédicteur structurel plus explicatif que le seul revenu monétaire.

#### Modèle 3 : Cluster Vols et Urbanisation
*   **Variable synthétique** : Agrégation des infractions d'appropriation (`Total_Vols`).
*   **Spécification** : 
    $$\text{Total\_Vols} = -17.4076 + 5.6564 \times \ln(\text{densite\_population}) + 0.4666 \times \text{taux\_pauvrete\_60\%}$$
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
*   **Modélisation spatiale** : L'intégration d'un modèle économétrique spatial (type SAR/SEM) permettrait de prendre en compte l'autocorrélation spatiale et les effets de débordement interdépartementaux.
*   **Maillage géographique** : Une analyse à l'échelle communale ou infra-communale (IRIS) affinerait la détection des micro-dynamiques locales.

---

## 5. STRUCTURE DU DÉPÔT

```text
.
├── DATA_PROJET/
│   ├── base_departements_complete_2020.csv  # Matrice d'analyse nettoyée et consolidée
│   ├── departements-france.csv             # Référentiel géospatial officiel
│   └── [Sources brutes INSEE/SSMSI]        # Données brutes et extractions d'origine
├── violence.ipynb                           # Notebook principal (ETL, économétrie, cartes)
└── README.md                                # Documentation du projet
```

---

## 6. INSTALLATION ET REPRODUCTION

### Prérequis
*   Python 3.10 ou supérieur
*   Environnement Jupyter (Jupyter Lab ou Jupyter Notebook)

### Installation des dépendances

```bash
# Cloner le dépôt
git clone [https://github.com/utilisateur/determinants-violence-france.git](https://github.com/utilisateur/determinants-violence-france.git)
cd determinants-violence-france

# Créer et activer un environnement virtuel
python -m venv venv
source venv/bin/activate  # Sur Windows: venv\Scripts\activate

# Installer les packages requis
pip install pandas numpy statsmodels scikit-learn scipy plotly folium seaborn matplotlib branca ipywidgets itables
```

### Exécution du projet

```bash
# Lancer Jupyter Lab
jupyter lab
```

Ouvrir le fichier `violence.ipynb` et exécuter l'ensemble des cellules dans l'ordre séquentiel pour reproduire l'ETL, les estimations économétriques et les visualisations interactives.
