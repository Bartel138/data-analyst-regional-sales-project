# data-analyst-regional-sales-project
Projet d'analyse de données en Python (Pandas, Matplotlib, Seaborn) : Exploration des performances commerciales, analyse régionale approfondie, calcul des parts de marché et visualisations graphiques avancées.

# 📊 Projet Portfolio : Analyse des Ventes et Performance Régionale

## 🔍 Contexte du Projet
Ce projet s'inscrit dans le cadre de mon portfolio de **Data Analyst**. L'objectif est d'explorer un jeu de données de ventes, d'en extraire des indicateurs clés de performance (KPIs) et de structurer des visualisations graphiques claires pour aider à la prise de décision stratégique.

Cette section se concentre spécifiquement sur **l'analyse régionale** (Chiffre d'Affaires, parts de marché, panier moyen et volumes de commandes).

---

## 🛠️ Stack Technique & Bibliothèques
* **Python** (Langage principal)
* **Pandas** (Manipulation et nettoyage des données, regroupements `groupby`, fusions `merge`)
* **Matplotlib** (Génération du camembert de répartition)
* **Seaborn** (Génération du diagramme en barres comparatif)

---

## 📈 Méthodologie et Traitement des Données

### 1. Agrégation Régionale et Parts de Marché
Les données brutes ont été groupées par région à l'aide de Pandas pour calculer :
* Le chiffre d'affaires total par région (`.sum()`).
* Le pourcentage de part de marché de chaque région par rapport au CA global de l'entreprise (via vectorisation).
* Le panier moyen par transaction (`.mean()`).
* Le volume total des commandes (`.count()`).
* La quantité moyenne d'articles par commande.

### 2. Constitution d'un Tableau Récapitulatif Unique
À l'aide de la fonction `.merge()`, l'ensemble des indicateurs calculés a été fusionné dans un tableau global unique (`tableau_recap`) indexé par nom de région (`RegionName`).

---

## 💻 Code Source Complet de la Partie Régionale

```python
# ==========================================
# PARTIE 2 : ANALYSE RÉGIONALE & INDICATEURS CLÉS
# ==========================================

# 1. Calcul du chiffre d'affaires total groupé par région
ca_par_region = Sales.groupby("RegionName")["TotalPrice"].sum().reset_index()

# 2. Calcul des parts de marché en pourcentage (%)
ca_par_region["Pourcentage"] = (ca_par_region["TotalPrice"] / ca_total) * 100

# 3. Calcul des indicateurs complémentaires de performance par région
panier_moy_region = Sales.groupby("RegionName")["TotalPrice"].mean().reset_index()
commandes_par_region = Sales.groupby("RegionName")["TotalPrice"].count().reset_index()
nombre_article_moyen = Sales.groupby("RegionName")["Quantity"].mean().reset_index()

# 4. Fusion de toutes les métriques dans un grand tableau récapitulatif unique (.merge)
tableau_recap = ca_par_region.copy()
tableau_recap = tableau_recap.merge(panier_moy_region, on="RegionName", suffixes=('', '_moyen'))
tableau_recap = tableau_recap.merge(commandes_par_region, on="RegionName", suffixes=('', '_commandes'))
tableau_recap = tableau_recap.merge(nombre_article_moyen, on="RegionName", suffixes=('', '_qte_moy'))

print("Tableau récapitulatif complet par région :")
print(tableau_recap)
print("\n" + "="*50 + "\n")

# ==========================================
# PARTIE 2 : VISUALISATIONS GRAPHIQUES
# ==========================================

# A. LE CAMEMBERT (Répartition en % du CA par région)
plt.figure(figsize=(8, 8))
mes_couleurs = ['#ff9999', '#66b3ff', '#99ff99', '#ffcc99']
mon_decalage = (0.05, 0, 0, 0)

plt.pie(
    ca_par_region["TotalPrice"],
    labels=ca_par_region["RegionName"],
    autopct='%1.1f%%',
    startangle=90,
    colors=mes_couleurs,
    explode=mon_decalage,
    pctdistance=0.75,
    textprops={'fontsize': 10, 'weight': 'bold'}
)
plt.title("Répartition du Chiffre d'Affaires par Région (%)", fontsize=14, fontweight='bold', pad=20)
plt.show()

# B. LE DIAGRAMME EN BARRES (Comparaison des montants globaux)
plt.figure(figsize=(12, 8))
sns.barplot(
    data=ca_par_region,
    x="RegionName",
    y="TotalPrice",
    palette=mes_couleurs
)
plt.title("Comparatif du Chiffre d'Affaires par Région (€)", fontsize=14, fontweight='bold', pad=15)
plt.xlabel("Nom de la Région", fontsize=11)
plt.ylabel("Chiffre d'Affaires (€)", fontsize=11)
plt.show()
