# 📰 Fake or Real : L’imposteur – Compétition Kaggle

Ce dépôt contient mon approche pour la compétition Kaggle  
**[Fake or Real: The Impostor Hunt](https://www.kaggle.com/competitions/fake-or-real-the-impostor-hunt)**.

Le but est de développer un **modèle de classification de texte** permettant de différencier un **texte généré par une IA** d’un **texte écrit par un humain**.

---

##  Approche

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

##  Prétraitement du texte – Étapes expliquées

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

##  Exemple final dans `df_dataset`

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

##  Structure du projet

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

## 📚 Chargement des embeddings GloVe (300d) pour NLP

### Idée principale
Le code charge des vecteurs de mots pré-entraînés depuis GloVe et les stocke dans un dictionnaire Python.  
Chaque mot est associé à un vecteur numérique représentant son sens.  
Ces vecteurs sont ensuite utilisés dans des modèles NLP (classification, traduction, résumé, etc.).

### Qu’est-ce que GloVe ?
GloVe (Global Vectors for Word Representation) transforme les mots en vecteurs de nombres réels.  
Exemple : `vec("king") - vec("man") + vec("woman") ≈ vec("queen")`.  
Ces vecteurs sont entraînés sur de grands corpus pour capturer les relations entre les mots.

### Utilité
- Permet aux modèles NLP de comprendre le sens des mots.  
- Évite d’entraîner vos propres embeddings.  
- Économise du temps et des ressources computationnelles.

---

## 📝 Création de vecteurs de documents avec GloVe

### Idée principale
- Si un mot n’est pas trouvé dans GloVe, il est remplacé par un vecteur nul.  
- Sinon, la moyenne des vecteurs des mots du document est calculée pour représenter le document.  
- La matrice résultante `X_glove` contient une ligne par document.  

### Utilité
- Transformer du texte en vecteurs numériques est essentiel pour les tâches NLP.  
- Ces vecteurs de documents peuvent être utilisés pour :
  - Classification de texte
  - Analyse de sentiment
  - Clustering ou recherche de similarité

### Comment ça fonctionne
La fonction `document_vector` prend :
- `tokens` : liste des mots du document  
- `embeddings_dict` : embeddings GloVe  
- `dim` : dimension des vecteurs (ici 300)  

Elle récupère le vecteur de chaque mot trouvé dans GloVe.  
**Forme des vecteurs documents GloVe :** `(190, 300)`  

### Ajout des vecteurs GloVe au DataFrame
- Chaque article est représenté par un vecteur GloVe de 300 dimensions.  
- Ces vecteurs deviennent des colonnes supplémentaires dans le DataFrame (`glove_0`, `glove_1`, ..., `glove_299`).  
- **Nouvelle forme du DataFrame :** `(190, 304)`  

---

## 🏷️ Features Named Entity Recognition (NER) pour les articles

### Vue d’ensemble
- Extraction des entités nommées (Person, Organization, Location, Date, etc.) via SpaCy pour chaque mot.  
- Résultat stocké dans une colonne `ner_tokens` du DataFrame.

### Encodage des entités en valeurs numériques
- Chaque type d’entité est assigné à un numéro unique.  
- Résultat stocké dans la colonne `ner_tokens_numeric`.  
- Permet aux modèles ML d’utiliser l’information sémantique pour améliorer la classification.

---

## ✍️ Calcul de nouvelles features textuelles

Pour chaque ligne de `df_dataset`, les nouvelles features calculées sont :  
- `avg_word_length` → longueur moyenne des mots  
- `num_stopwords` → nombre de stopwords  
- `num_uppercase` → nombre de mots en majuscules  
- `num_punct` → nombre de signes de ponctuation  
- `num_sentences` → nombre de phrases  
- `unique_words_ratio` → ratio mots uniques / total mots  
- `contains_numbers` → présence de chiffres  
- `readability_score` → score approximatif Flesch Reading Ease  

**Exemple de DataFrame avec nouvelles features :**  
- **Nombre de colonnes total après ajout :** 316  

---

## ⚙️ Traitement du jeu de test
- Les mêmes features textuelles sont calculées pour `df_test`.  
- Vérification et traitement des outliers pour les colonnes numériques (`num_tokens`, `num_chars`, `avg_word_length`, `num_stopwords`, `readability_score`) via **capping IQR**.

### Outliers détectés dans df_test
| Colonne | Nombre d’outliers |
|---------|-----------------|
| num_tokens | 159 |
| num_chars | 161 |
| avg_word_length | 67 |
| num_stopwords | 25 |
| num_uppercase | 101 |
| num_punct | 185 |
| num_sentences | 19 |
| unique_words_ratio | 30 |
| readability_score | 122 |

- Après capping, les valeurs extrêmes sont ajustées aux bornes IQR.  

---

## 🔗 Analyse de corrélation des features numériques

- Calcul de la matrice de corrélation pour les features numériques de `df_dataset` et visualisation avec **heatmap** (Seaborn).  
- Identification des paires de colonnes fortement corrélées (|corr| > 0.8) :

| Feature 1 | Feature 2 | Corrélation |
|-----------|-----------|------------|
| num_tokens | num_chars | 0.993985 |
| num_uppercase | num_punct | 0.889471 |

---

## ⚡ Analyse de l’importance des features fortement corrélées par rapport à la cible

| Feature | Correlated With | Correlation | Importance | Décision | Justification |
|---------|----------------|------------|------------|----------|---------------|
| num_chars | num_tokens | 0.99 | 0.278 | Supprimée | Fortement corrélée et peu importante pour la cible |
| num_uppercase | num_punct | 0.89 | 0.171 | Supprimée | Fortement corrélée et peu importante |
| num_tokens | - | - | 0.283 | Conservée | Plus importante pour la prédiction |
| num_punct | - | - | 0.269 | Conservée | Plus importante pour la prédiction |

**Importance finale des features fortement corrélées :**

| Feature | Importance |
|---------|-----------|
| num_tokens | 0.282703 |
| num_chars | 0.278086 |
| num_punct | 0.268681 |
| num_uppercase | 0.170530 |

### Analyse corrélation du jeu test
- Corrélation forte détectée entre :  
| Feature 1 | Feature 2 | Corrélation |
|-----------|-----------|------------|
| num_tokens | num_chars | 0.992736 |
| num_tokens | num_punct | 0.831494 |
| num_chars | num_punct | 0.813295 |

---

Ce README documente donc **la suite du pipeline**, depuis le chargement des embeddings GloVe, l’extraction NER, la création de nouvelles features textuelles, le traitement des outliers, jusqu’à l’analyse de corrélation et importance des features numériques.
         
