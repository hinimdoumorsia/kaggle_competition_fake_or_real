# 📰 Fake or Real : L’imposteur – Compétition Kaggle

Ce dépôt contient mon approche pour la compétition Kaggle  
**[Fake or Real: The Impostor Hunt](https://www.kaggle.com/competitions/fake-or-real-the-impostor-hunt)**.

Le but est de développer un **modèle de classification de texte** permettant de différencier un **texte généré par une IA** d’un **texte écrit par un humain**.

---

## 🚀 Approche

Plusieurs approches ont été utilisées pour approcher le problème, incluant **prétraitement du texte**, **représentation des données**, et **modélisation**.

---

## 📂 Aperçu des données

**Exemple du jeu d’entraînement :**

| text | label |
|------|-------|
| The VIRSA (Visible Infrared Survey Telescope Array) observes galaxies. | 1 |
| The China relay network has released a significant amount of data. | 0 |
| The project aims to achieve an accuracy level of 95%. | 1 |
| China\nThe goal of this project involves achieving better coverage. | 0 |
| Scientists can learn about how galaxies form and evolve. | 1 |

- **Total exemples d’entraînement :** 190  

**Exemple du jeu de test :**

| article_id | text |
|------------|------|
| article_0000_1 | "Music" Music music music Music music Music mu... |
| article_0000_2 | Since its launch on Paranal observatory's Very... |
| article_0001_1 | underground exploration on SN's birth has prov... |
| article_0001_2 | SN 1987A provides valuable insights as newer o... |
| article_0002_1 | This research aimed to understand how star sha... |

- **Total exemples de test :** 2,136

---

## 🧹 Prétraitement du texte – Étapes expliquées

Pour chaque texte du dataset `df_dataset` :

1️⃣ **Mise en minuscules**  
Standardiser tous les mots pour que `"China"` et `"china"` soient considérés identiques.  

| Texte original | Après mise en minuscules |
|----------------|------------------------|
| The VIRSA observes galaxies. | the virsa observes galaxies |
| China 2025 report! | china 2025 report! |

2️⃣ **Suppression de la ponctuation et des nombres**  
Supprimer tous les caractères non alphabétiques (ponctuation, chiffres, symboles).  

| Après minuscules | Après suppression ponctuation/nombres |
|-----------------|--------------------------------------|
| the virsa observes galaxies | the virsa observes galaxies |
| china 2025 report! | china report |

3️⃣ **Tokenisation**  
Diviser le texte en mots (tokens).  

| Texte nettoyé | Tokens |
|---------------|--------|
| the virsa observes galaxies | [the, virsa, observes, galaxies] |
| china report | [china, report] |

4️⃣ **Suppression des stopwords et lemmatisation**  
- Stopwords : mots fréquents mais peu informatifs (`the`, `a`, `has`, etc.)  
- Lemmatisation : réduire les mots à leur racine (`observes → observe`, `galaxies → galaxy`)  

| Tokens | Après suppression des stopwords & lemmatisation |
|--------|-----------------------------------------------|
| [the, virsa, observes, galaxies] | [virsa, observe, galaxy] |
| [china, has, released, report] | [china, release, report] |

5️⃣ **Reconstruction de la phrase nettoyée**  
Assembler les tokens filtrés en une phrase pour le modèle.  

| Tokens lemmatisés | clean_text |
|------------------|------------|
| [virsa, observe, galaxy] | virsa observe galaxy |
| [china, release, report] | china release report |

---

## 🏗 Exemple final dans `df_dataset`

| Original Text | clean_text | tokens | label |
|---------------|------------|--------|-------|
| The VIRSA observes galaxies. | virsa observe galaxy | [virsa, observe, galaxy] | 1 |
| China has released a report. | china release report | [china, release, report] | 0 |
| The project aims to achieve an accuracy level of 95%. | project aim achieve accuracy level | [project, aim, achieve, accuracy, level] | 1 |
| China\nThe goal of this project involves achieving better coverage. | china goal project involve achieve better coverage | [china, goal, project, involve, achieve, better, coverage] | 0 |
| Scientists can learn about how galaxies form and evolve. | scientist learn galaxy form evolve | [scientist, learn, galaxy, form, evolve] | 1 |

---

## 📊 Résultats du dataset d’entraînement

| text | cible | clean_text | tokens |
|------|-------|------------|--------|
| The VIRSA (Visible Infrared Survey Telescope Array) observes galaxies. | 1 | virsa visible infrared survey telescope array observe galaxy | [virsa, visible, infrared, survey, telescope, array, observe, galaxy] |
| The China relay network has released a significant amount of data. | 0 | china relay network released significant amount data | [china, relay, network, released, significant, amount, data] |
| The project aims to achieve an accuracy level of 95%. | 1 | project aim achieve accuracy level dex analyzing | [project, aim, achieve, accuracy, level, dex, analyzing] |
| China\nThe goal of this project involves achieving better coverage. | 0 | china goal project involves achieving accuracy coverage | [china, goal, project, involves, achieving, accuracy, coverage] |
| Scientists can learn about how galaxies form and evolve. | 1 | scientist learn galaxy form evolve two methods | [scientist, learn, galaxy, form, evolve, two, methods] |

---

## 🏗 Structure du projet

```plaintext
kaggle_competition_fake_or_real/
│
├── notebooks/             # Notebooks Jupyter pour l'entraînement et l'évaluation
│   └── main_notebook.ipynb
│
├── glove/                 # Embeddings GloVe préentraînés (300d)
│   └── glove.6B.300d.txt
│
├── README.md              # Documentation du projet           
