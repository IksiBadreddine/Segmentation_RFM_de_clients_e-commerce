# Rapport — Projet 11 : Segmentation RFM de clients e-commerce

## 1. Contexte

Ce projet utilise le dataset *Online Retail* du UCI Machine Learning Repository pour réaliser une segmentation client basée sur les indicateurs RFM :
- **R**écence : date du dernier achat
- **F**réquence : nombre de commandes
- **M**ontant : valeur monétaire des achats

L'objectif est de produire des segments métier actionnables, puis d'associer à chaque segment des recommandations marketing concrètes.

## 2. Objectifs

- Charger et explorer le dataset transactionnel.
- Nettoyer le jeu de données en supprimant les transactions non pertinentes.
- Calculer les indicateurs RFM par client.
- Préparer les données pour un clustering robuste.
- Sélectionner un nombre de clusters cohérent avec la qualité statistique et la granularité métier.
- Traduire les clusters en segments marketing.
- Valider la segmentation avec une méthode alternative (clustering hiérarchique).

## 3. Jeu de données

- Dataset utilisé : *Online Retail Dataset* (UCI ML Repository, id=352).
- Volume initial : environ **541 909 lignes**.
- Périmètre : transactions e-commerce principalement au Royaume-Uni.
- InvoiceNo:Numéro de facture. Numéro numérique à 6 chiffres attribué de manière unique à chaque transaction. Si ce code commence par la lettre « c », cela indique une annulation.
- StockCode: Code produit (article). Numéro numérique à 5 chiffres attribué de manière unique à chaque produit.
- Description: Nom du produit (article).
- Quantity : Quantité de chaque produit (article) par transaction.
- InvoiceDate: Date et heure de la facture.
- UnitPrice:  Prix unitaire du produit en livres sterling.
- CustomerID : Numéro de client. Numéro numérique à 5 chiffres attribué de manière unique à chaque client.
- Country:  Nom du pays de résidence du client.



## 4. Préparation des données

### 4.1 Nettoyage

Le notebook supprime ou traite :
- les lignes sans `CustomerID` (impossibles à affecter à un client),
- les factures annulées (`InvoiceNo` commençant par `C`),
- les quantités négatives (retours/avoirs),
- les prix unitaires nuls ou négatifs.

Cette étape est critique pour garantir que les indicateurs RFM reflètent des achats réels et ne sont pas faussés par des opérations comptables ou des retours.

### 4.2 Calcul de RFM

Pour chaque client, le notebook calcule :
- `Recency` : nombre de jours depuis le dernier achat,
- `Frequency` : nombre total de commandes,
- `Monetary` : montant total dépensé.

Après nettoyage, la segmentation est réalisée sur une base d'environ **4 338 clients**.

## 5. Prétraitement RFM

### 5.1 Analyse des distributions

Les variables RFM présentent des distributions très asymétriques, avec de nombreux clients peu actifs et quelques clients très puissants. Ce comportement est attendu en e-commerce.

### 5.2 Transformation logarithmique

Une transformation `log1p` est appliquée à `Recency`, `Frequency` et `Monetary` afin de réduire l'asymétrie et de limiter l'influence des valeurs extrêmes.

### 5.3 Standardisation

Les trois variables log-transformées sont standardisées avec `StandardScaler` (moyenne 0, écart-type 1).

Cela est indispensable avant d'appliquer K-Means, car sans standardisation la variable `Monetary` dominerait la distance euclidienne et rendrait les clusters non interprétables.

## 6. Choix du nombre de clusters

### 6.1 Méthodes utilisées

Le notebook teste K-Means pour `k` de 2 à 10 :
- inertie (méthode du coude),
- score de silhouette.

### 6.2 Critère de décision

Le choix du nombre de clusters retient à la fois :
- un score de silhouette élevé,
- un nombre de clusters suffisant pour produire une segmentation marketing actionnable.

Le projet privilégie un **k ≥ 3** pour distinguer au moins les clients sains des clients à risque/perdus, puis choisi le `k` le plus pertinent dans cette plage.

### 6.3 Résultat retenu

- `k` retenu : **4 clusters** (meilleur `k >= 3` selon le score de silhouette et la granularité métier).

## 7. Segmentation métier

Les clusters K-Means sont traduits en segments métier suivants :

- **Champions / Clients VIP**
- **Nouveaux clients / Occasionnels**
- **Clients fidèles**
- **Clients à risque**
- **Clients hibernants / Perdus**

Chaque segment est défini à partir des moyennes réelles de `Recency`, `Frequency` et `Monetary`, ce qui garantit une interprétation métier solide.

## 8. Actions marketing recommandées

### 8.1 Champions / Clients VIP

- Programme de fidélité premium et avantages exclusifs.
- Accès anticipé aux nouveautés.
- Remises VIP.
- Objectif : maximiser la rétention et transformer ces clients en ambassadeurs (parrainage, avis clients).

### 8.2 Nouveaux clients / Occasionnels

- Programme de bienvenue et onboarding.
- Email de remerciement, guide produits, code promo sur le 2ᵉ achat.
- Objectif : accélérer la transition vers une fréquence d'achat plus régulière.

### 8.3 Clients fidèles

- Recommandations personnalisées (cross-sell / up-sell).
- Communication régulière via newsletter ciblée.
- Objectif : maintenir l'engagement et pousser vers le segment Champions.

### 8.4 Clients à risque

- Campagne de relance proactive avant décrochage.
- Email personnalisé, offre de réactivation limitée.
- Enquête de satisfaction pour identifier un motif d'insatisfaction.

### 8.5 Clients hibernants / Perdus

- Campagne de réactivation « win-back ».
- Offre forte et message de type « vous nous manquez ».
- Si absence de réaction après 1 ou 2 relances, exclure ces contacts des campagnes coûteuses pour préserver le ROI.

## 9. Validation alternative

Le notebook compare également la segmentation K-Means avec un clustering hiérarchique ascendant (Ward) sur les mêmes variables RFM standardisées.

- Le clustering hiérarchique sert de validation croisée.
- L'analyse compare la cohérence des partitions et l'accord entre les deux méthodes.
- En pratique, la segmentation K-Means est retenue comme segmentation de référence, la hiérarchique confirmant la robustesse de la structure trouvée.

## 10. Conclusions

- Le projet livre une segmentation client basée sur le modèle RFM, bien adaptée au dataset e-commerce.
- La préparation des données (nettoyage, log-transform, standardisation) a été traitée avec les précautions requises pour éviter des biais.
- Le choix de `k = 4` est justifié à la fois statistiquement et métier.
- Les clusters sont transformés en segments actionnables avec des recommandations marketing concrètes.

## 11. Limites et pistes d'amélioration

- Le RFM ne capture pas la diversité des produits achetés (catégories, marges) ni la saisonnalité.
- Un enrichissement par des variables produits, des catégories, ou une valeur vie client (CLV) prédictive permettrait d'affiner la segmentation.
- Le périmètre géographique, essentiellement Royaume-Uni, limite la généralisation à d'autres marchés.
- Une segmentation par pays ou par saison serait pertinente pour les marchés les plus représentés.
