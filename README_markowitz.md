# Simulateur de Portefeuille — Optimisation Markowitz

Simulateur Monte Carlo construisant la **frontière efficiente** d'un portefeuille à 4 actifs, selon la théorie moderne du portefeuille (Markowitz, 1952).

## 📐 Le modèle

L'idée de Markowitz : pour un niveau de rendement attendu donné, il existe une allocation qui **minimise le risque** (et inversement, pour un niveau de risque donné, une allocation qui **maximise le rendement**). L'ensemble de ces allocations optimales forme la **frontière efficiente**.

**Rendement et risque d'un portefeuille** à poids `w = (w₁, ..., wₙ)` :

- Rendement attendu : `μ_p = w · μ`
- Volatilité : `σ_p = √(wᵀ Σ w)`, où `Σ` est la matrice de covariance des actifs

**Méthode utilisée ici** : plutôt qu'une optimisation analytique directe, le notebook génère **10 000 portefeuilles aléatoires** via une distribution de Dirichlet (poids positifs sommant à 1), calcule leur rendement/volatilité/Sharpe, puis identifie le portefeuille de **ratio de Sharpe maximal** parmi les simulations. C'est une approche par échantillonnage de la frontière, plus intuitive à visualiser qu'une résolution par programmation quadratique, au prix d'une précision légèrement moindre sur le portefeuille optimal exact.

**Contraintes imposées** :
- Somme des poids = 1 (portefeuille pleinement investi)
- Poids ≥ 0 pour chaque actif (pas de vente à découvert)

**Données** : rendements journaliers simulés sur 252 jours via une loi normale multivariée, à partir de rendements moyens, volatilités et une matrice de corrélation choisis pour 4 actifs synthétiques (`ACTIF_A` à `ACTIF_D`). Ce choix permet de contrôler précisément les paramètres du modèle pour en illustrer le comportement, indépendamment du bruit des données de marché réelles.

## 📊 Frontière efficiente obtenue

![Frontière efficiente](frontiere_efficiente.png)

Chaque point représente un portefeuille simulé, coloré selon son ratio de Sharpe. Le point doré marque le portefeuille de **Sharpe maximal** (1.363) parmi les 10 000 simulations.

## 📈 Exemple de résultat — Portefeuille optimal (Sharpe max)

| Indicateur | Valeur |
|---|---|
| Rendement annualisé | 19.1% |
| Volatilité annualisée | 14.0% |
| Sharpe Ratio | 1.363 |

**Allocation optimale :**

| Actif | Poids |
|---|---|
| ACTIF_A | 5.8% |
| ACTIF_B | 12.6% |
| ACTIF_C | 11.7% |
| ACTIF_D | 69.9% |

> Le portefeuille optimal concentre la majorité du poids sur `ACTIF_D`, l'actif le moins volatil (σ = 1.0%) du jeu de données, ce qui illustre l'arbitrage rendement/risque au cœur de l'optimisation de Markowitz : maximiser le Sharpe favorise les actifs à faible variance même si leur rendement individuel est plus modeste.

*Résultats obtenus avec `np.random.seed(42)` fixé avant la génération des rendements — reproductibles à l'identique.*

## 🛠️ Stack technique

`Python` `NumPy` `Pandas` `Matplotlib`

## 🚀 Limites et améliorations possibles

- Remplacer l'échantillonnage Monte Carlo par une résolution analytique (programmation quadratique via `scipy.optimize`) pour obtenir le portefeuille optimal exact, pas un point proche parmi les simulations
- Tester sur des données de marché réelles (`yfinance`) plutôt que des rendements simulés
- Ajouter une contrainte de poids maximal par actif pour limiter la concentration
