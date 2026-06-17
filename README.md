# analyse-sentiment-hassaniya-pulaar
# Analyse de sentiment des avis citoyens en hassaniya et en pulaar

Projet de Master — Traitement Automatique des Langues
**Université de Nouakchott Al-Aasriya (UNA)**

Étude comparative de méthodes classiques et de Transformers multilingues pour la classification de sentiment (positif / neutre / négatif) sur deux langues mauritaniennes peu dotées : le **hassaniya** (écriture arabe) et le **pulaar** (écriture latine).

---

## Objectif

Classer automatiquement des avis de citoyens selon la polarité exprimée, afin d'aider une collectivité à mesurer rapidement la satisfaction dans les commentaires reçus. Le projet compare quatre approches sur un protocole identique pour déterminer laquelle est la plus adaptée à un corpus de petite taille.

## Données

- Corpus **parallèle** : chaque énoncé existe en hassaniya, souvent en pulaar, avec une étiquette de sentiment.
- **549 énoncés annotés** (sur 793 lignes au total), dont 335 disposent d'une version pulaar.
- Répartition des classes : neutre 227, négatif 189, positif 133 (léger déséquilibre, ratio 1,7).

## Structure du dépôt

```
.
├── data/
│   └── dataset_master.csv                  # Corpus annoté (parallèle)
├── notebooks/
│   ├── projet_sentiment_mbert.ipynb        # Approche mono-modèle (mBERT) + validation croisée
│   └── projet_sentiment_2modeles.ipynb     # Deux modèles spécialisés (MARBERT + AfroXLMR)
├── rapport/
│   └── rapport_projet.pdf                   # Rapport complet
├── presentation/
│   └── presentation_soutenance.pdf         # Support de soutenance
└── README.md
```

> Les modèles entraînés (fichiers volumineux) ne sont pas versionnés : les notebooks les régénèrent.

## Comment exécuter

1. Ouvrir un notebook dans **Google Colab**.
2. Activer le GPU : *Exécution → Modifier le type d'exécution → GPU*.
3. Déposer `dataset_master.csv` dans la session (ou adapter la variable `CSV_PATH`).
4. Exécuter les cellules dans l'ordre.

Les dépendances s'installent dans la première cellule :

```bash
pip install "transformers[torch]" datasets evaluate accelerate scikit-learn matplotlib seaborn gradio sentencepiece -U
```

## Méthodologie

- **Empilement vertical** du corpus parallèle : un exemple = un énoncé dans une seule langue.
- **Découpage 80/10/10 au niveau de l'énoncé** (avant dédoublement par langue), stratifié, pour éviter toute fuite d'information entre les langues.
- Gestion du déséquilibre par **poids de classe**, **arrêt anticipé** contre le surapprentissage, et **F1-macro** comme métrique principale.
- Évaluation **globale** et **par langue**.

## Résultats

F1-macro sur l'ensemble de test combiné (82 exemples), protocole identique pour toutes les approches :

| Approche | F1-macro |
|----------|:--------:|
| **Baseline TF-IDF + Régression logistique** | **0,51** |
| Système à deux modèles (MARBERT + AfroXLMR) | 0,43 |
| mBERT (mono-modèle) | 0,40 |

Détail par langue :

| Langue | Baseline | Transformer spécialisé | n (test) |
|--------|:--------:|:----------------------:|:--------:|
| Hassaniya | 0,36 | MARBERT : 0,34 | 55 |
| Pulaar | 0,72 | AfroXLMR : 0,60 | 27 |

**Résultat principal :** sur ce corpus restreint, la méthode classique surpasse les Transformers, qui surapprennent faute de données suffisantes. La spécialisation par langue améliore le système neuronal (0,43 contre 0,40) mais ne dépasse pas la référence.

## Limites et perspectives

- Corpus de petite taille et classe « positif » sous-représentée (133 exemples).
- Pas de mesure d'accord inter-annotateurs.
- Pistes : annoter les 244 énoncés restants, renforcer la classe positif, appliquer une validation croisée, explorer l'augmentation de données.

## Auteur

**Khadijetou Sow** — Matricule C17879
Université de Nouakchott Al-Aasriya (UNA) — Année universitaire 2025-2026
