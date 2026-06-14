# 📊 Simulateur de Portefeuille — Frontière Efficiente de Markowitz

Simulation Monte Carlo de **10 000 portefeuilles multi-actifs** pour identifier l'allocation optimale selon la théorie moderne du portefeuille (Markowitz, 1952). Le projet couvre la modélisation des rendements corrélés, le calcul des métriques de performance et la visualisation de la **Frontière Efficiente**.

---

## 🎯 Objectif

> *"Parmi tous les portefeuilles possibles d'un univers de 4 actifs, lequel maximise le rendement par unité de risque ?"*

Le simulateur génère aléatoirement 10 000 combinaisons de pondérations et identifie le **portefeuille tangent** — celui qui maximise le Sharpe Ratio — en le positionnant sur la Frontière Efficiente.

---

## 🧠 Méthodologie

### Étape 1 — Génération des rendements corrélés

Les rendements journaliers de 4 actifs sont simulés via une **loi normale multivariée**, paramétrée par des espérances, des volatilités et une matrice de corrélation réaliste :

| Actif    | µ journalier | σ journalier |
|----------|-------------|-------------|
| ACTIF_A  | 0.080 %     | 1.50 %      |
| ACTIF_B  | 0.050 %     | 2.00 %      |
| ACTIF_C  | 0.100 %     | 2.50 %      |
| ACTIF_D  | 0.030 %     | 1.00 %      |

La matrice de covariance est construite analytiquement :

```
Cov(i,j) = σᵢ × σⱼ × ρᵢⱼ
```

### Étape 2 — Performance du portefeuille équipondéré

Calcul des métriques annualisées pour une allocation de référence à 25% par actif :

```python
mu_annuel  = rendements_port.mean() * 252
vol_annuel = rendements_port.std()  * np.sqrt(252)
sharpe     = mu_annuel / vol_annuel
```

### Étape 3 — Simulation Monte Carlo

10 000 vecteurs de poids aléatoires sont générés via une **distribution de Dirichlet** (garantit que la somme des poids = 1 sans biais). Pour chaque portefeuille : rendement annualisé, volatilité annualisée et Sharpe Ratio.

### Étape 4 — Portefeuille optimal

Identification du portefeuille qui **maximise le Sharpe Ratio** parmi les 10 000 simulations (`idxmax()`).

### Étape 5 — Frontière Efficiente

Visualisation du nuage de 10 000 portefeuilles colorés par Sharpe Ratio (gradient rouge → vert), avec le portefeuille optimal (⭐) et le portefeuille équipondéré (◆) mis en évidence.

---

## 📈 Résultats

### Portefeuille équipondéré (référence)

| Indicateur            | Valeur  |
|-----------------------|---------|
| Rendement annualisé   | ~14.7 % |
| Volatilité annualisée | ~16.8 % |
| Sharpe Ratio          | ~0.87   |

> Le rendement est inférieur au niveau de risque pris — ce portefeuille sous-performe le portefeuille optimal.

### Portefeuille optimal (Sharpe maximum)

Le portefeuille tangent identifié par Monte Carlo surconcentre naturellement les actifs à meilleur rapport rendement/risque (ACTIF_C et ACTIF_D), tout en exploitant les faibles corrélations pour diversifier.

---

## 🖼️ Visualisation — Frontière Efficiente

```
Rendement annualisé (%)
        │                              ★ Sharpe Max
        │                         ●●●●●●●
        │                    ●●●●●
        │               ◆ Équipondéré
        │          ●●●●
        └────────────────────────────────── Volatilité (%)
```

Chaque point représente un portefeuille simulé. La couleur encode le Sharpe Ratio (rouge = faible, vert = élevé). L'enveloppe supérieure du nuage constitue la **Frontière Efficiente** : pour un niveau de risque donné, aucun portefeuille ne fait mieux.

---

## 🛠️ Stack Technique

```
Python 3.10
├── NumPy    — Loi normale multivariée, algèbre matricielle (@), Dirichlet
├── Pandas   — Stockage des simulations, recherche du max (idxmax)
└── Matplotlib — Scatter plot coloré, visualisation de la frontière
```

---

## 🚀 Lancer le Notebook

```bash
# 1. Cloner le dépôt
git clone https://github.com/<votre-username>/<votre-repo>.git
cd <votre-repo>

# 2. Installer les dépendances
pip install numpy pandas matplotlib

# 3. Ouvrir le notebook
jupyter notebook Simulation_de_portefeuilles_simples.ipynb
```

---

## 📁 Structure du Projet

```
.
├── Simulation_de_portefeuilles_simples.ipynb
└── README.md
```

---

## 📚 Concepts Théoriques

**Théorie Moderne du Portefeuille (Markowitz, 1952)**
Un investisseur rationnel cherche à maximiser son rendement pour un niveau de risque donné. La Frontière Efficiente représente l'ensemble de ces portefeuilles optimaux.

**Loi normale multivariée**
Permet de simuler des rendements réalistes en tenant compte des corrélations entre actifs — un actif ne se comporte pas indépendamment des autres dans un vrai marché.

**Distribution de Dirichlet**
Générer des poids aléatoires avec `np.random.dirichlet(np.ones(n))` garantit que leur somme vaut exactement 1, sans biais vers des extrêmes — supérieur à une simple normalisation de tirages uniformes.

**Sharpe Ratio**
Mesure du rendement par unité de risque : `(µ - rf) / σ`. Le portefeuille tangent maximise cette quantité sur la Frontière Efficiente (ici `rf = 0` pour simplifier).

---

## ⚠️ Limites & Pistes d'Amélioration

- Rendements **simulés** (non réels) — étendre avec des données yfinance sur un vrai univers d'actions
- Pas de **taux sans risque** dans le Sharpe (rf = 0)
- Pas de **contraintes** sur les poids (vente à découvert implicitement autorisée si poids < 0)
- Monte Carlo donne une approximation — utiliser `scipy.optimize` pour une optimisation exacte
- Étendre à la **VaR et l'Expected Shortfall** par portefeuille pour une perspective risk management

---

*Projet réalisé dans le cadre d'une transition vers la finance quantitative (risk management / model validation).*
