# Commentaires descriptifs — Figures du notebook

Ce document fournit un commentaire concis et interprétable pour chaque figure générée dans le notebook `Projet11_Segmentation_RFM (1).ipynb`.

1) Figure — Distributions brutes des variables RFM
- Ce que montre la figure : trois histogrammes (Récence, Fréquence, Montant) montrant la distribution brute de chaque variable.
- Comment l'interpréter : on observe une forte asymétrie à droite (quelques clients très actifs ou très dépensiers). Les queues longues indiquent des valeurs extrêmes qui peuvent dominer les mesures de distance.
- Conclusion / action : la transformation logarithmique est justifiée avant tout clustering pour atténuer l'influence des outliers.

2) Figure — Distributions après transformation logarithmique
- Ce que montre la figure : histogrammes des variables `log1p(Recency)`, `log1p(Frequency)`, `log1p(Monetary)`.
- Comment l'interpréter : les distributions sont plus symétriques, les valeurs extrêmes sont compressées, ce qui rend les variables plus adaptées à une comparaison par distance euclidienne.
- Conclusion / action : poursuivre avec la standardisation (moyenne=0, écart-type=1) avant K-Means.

3) Figure — Courbe du coude (inertie) et score de silhouette
- Ce que montre la figure : à gauche la décroissance de l'inertie quand k augmente, à droite le score de silhouette moyen pour chaque k testé (2 à 10).
- Comment l'interpréter : la courbe du coude indique la zone de rendement décroissant (souvent k=3..5). Le score de silhouette identifie quantitativement le k qui maximise la cohésion/séparation.
- Conclusion / action : combiner les deux critères et privilégier un k >= 3 pour conserver une granularité métier. Le notebook retient `k = 4` comme compromis actionnable.

4) Figure — Projection 2D (ACP) des clusters K-Means
- Ce que montre la figure : projection des clients sur les deux composantes principales, colorée par cluster K-Means.
- Comment l'interpréter : permet de visualiser la séparation globale des clusters ; regarder l'étendue et le chevauchement des nuages pour évaluer la qualité visuelle de la partition.
- Conclusion / action : si les groupes apparaissent distincts, cela renforce la pertinence du clustering; sinon investiguer transformations, features ou k.

5) Figure — Profil moyen RFM par cluster (barplots)
- Ce que montre la figure : pour chaque cluster, barplots comparant la Récence moyenne, la Fréquence moyenne et le Montant moyen.
- Comment l'interpréter : identifier les clusters à forte valeur (faible récence, fréquence et montant élevés) vs clusters à risque (récence élevée, fréquence/montant faibles).
- Conclusion / action : utiliser ces profils pour nommer les segments (Champions, Fidèles, À risque, Hibernants, etc.) et prioriser les actions marketing.

6) Figure — Heatmap des profils RFM normalisés
- Ce que montre la figure : heatmap z-score des trois métriques par cluster (couleur = position relative, annotation = valeur réelle).
- Comment l'interpréter : visualisation rapide des positions relatives des clusters sur les trois axes RFM (vert = au-dessus de la moyenne, rouge = en dessous).
- Conclusion / action : facilite le classement des clusters par contribution relative au CA et au risque de churn.

7) Figure — Projection 2D des segments métier
- Ce que montre la figure : scatter des deux composantes principales, coloré par segment métier (après mappage RFM → segment).
- Comment l'interpréter : vérifie si les segments définis par règles métier correspondent à des régions cohérentes dans l'espace RFM projeté.
- Conclusion / action : permet de détecter segments mal séparés ou segments trop hétérogènes nécessitant un reclustering ou une révision des règles.

8) Figure — Dendrogramme du clustering hiérarchique (vue tronquée)
- Ce que montre la figure : arbre de regroupement (Ward) tronqué sur les dernières fusions pour lisibilité.
- Comment l'interpréter : explorer des granularités alternatives (k=2..6) sans ré-entraîner un modèle ; repérer paliers naturels de fusion.
- Conclusion / action : utiliser comme validation croisée de la structure trouvée par K-Means.

9) Figure — Comparaison visuelle K-Means vs Clustering Hiérarchique
- Ce que montre la figure : deux scatterplots côte-à-côte projetés en PCA, l'un coloré par K-Means, l'autre par la partition hiérarchique.
- Comment l'interpréter : comparer la consistance des partitions ; regarder le score de silhouette affiché et l'ARI (Adjusted Rand Index) pour un indicateur quantitatif d'accord.
- Conclusion / action : si la concordance est élevée (ARI proche de 1 et silhouettes proches), la segmentation est robuste; sinon, réévaluer choix de k ou features.

---

Si vous préférez, j'insère ces commentaires directement dans le notebook, en ajoutant une cellule Markdown après chaque cellule de code produisant une figure. Voulez-vous que je fasse cette insertion automatique ?
