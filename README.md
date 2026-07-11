# VashyScan

**Le criblage complet de la physique dimensionnelle.**

VashyScan énumère de façon **exhaustive** toutes les relations dimensionnellement
cohérentes que l'on peut former à partir d'un panel de 60 grandeurs physiques
couvrant tous les domaines de la physique classique, à partir du **théorème de
Vaschy–Buckingham (théorème π)**. Chaque relation est un nombre sans dimension
irréductible — par exemple `F = k₀·m·a` (2ᵉ loi de Newton), `ℓ = k₀·v⁻¹·ρ⁻¹·μ`
(nombre de Reynolds) ou `m = k₀·E·c₀⁻²` (équivalence masse-énergie) — où `k₀`
désigne la constante sans dimension que l'analyse dimensionnelle ne fixe pas.

👉 **Explorateur en ligne : https://l0d0v1c.github.io/vashyscan**

> `k₀` est la constante d'ordre 1 indéterminée par l'analyse dimensionnelle
> (le ½ de l'énergie cinétique, le 6π de la traînée de Stokes…). C'est une
> limite intrinsèque de la méthode, pas du jeu de données.

---

## Le principe

Chaque grandeur est un vecteur d'exposants sur les **7 dimensions de base SI**
(M, L, T, Θ, I, N, J). Une relation sans dimension est un vecteur du **noyau** de
la matrice dimensionnelle. VashyScan énumère les **circuits** du matroïde
dimensionnel : les ensembles *minimaux* de grandeurs (chacune indispensable, pas
un produit de relations plus petites). Chaque circuit = un nombre sans dimension
irréductible, et le catalogue les liste **tous, sans doublon**.

Fait notable : avec 7 dimensions de base, **aucune relation irréductible ne peut
dépasser 8 grandeurs** (une relation de `k` grandeurs a un rang `k−1 ≤ 7`). La
taille 8 est donc un **plafond mathématique dur** — le catalogue est complet.

---

## Dataset d'origine — le catalogue complet

Le criblage brut de tout l'espace dimensionnellement possible.

| Caractéristique | Valeur |
|---|---|
| Grandeurs physiques | **60** (6 domaines : mécanique, thermo, électromagnétisme, chimie, photométrie, constantes) |
| Dimensions de base | 7 (M, L, T, Θ, I, N, J) |
| **Relations (circuits)** | **2 325 320** |
| Tailles | 2 à 8 (plafond dur = 8) |
| Sous-ensemble « plausible » | 46 720 (filtre de physicalité) |
| Format | Parquet, ~69 Mo (zstd), 18 colonnes |

Répartition par taille :

| taille | circuits | | taille | circuits |
|---|---|---|---|---|
| 2 | 22 | | 6 | 813 919 |
| 3 | 593 | | 7 | 1 140 445 |
| 4 | 17 325 | | 8 | 72 370 |
| 5 | 280 646 | | **Σ** | **2 325 320** |

Chaque ligne porte : le nombre sans dimension (`pi`), l'équation résolue
(`equation`), les grandeurs (`symbols`/`keys`/`exponents`), les domaines, et des
**métriques de pertinence** (`n_const`, `n_domains`, `max_exp`, `exp_l1`,
`complexity`, `score`, `plausible`) permettant de trier les vraies lois hors du
bruit dimensionnel.

⚠️ **Cohérence dimensionnelle ≠ vérité physique.** La grande majorité de ces
2,3 M relations sont des coïncidences dimensionnelles. Les colonnes `plausible`
et `score` servent précisément à isoler le signal.

---

## Dataset identifié — celui affiché ici

Le sous-ensemble **annoté physiquement**, exposé par l'explorateur.

Le dataset complet est stocké ici: https://huggingface.co/datasets/RANDMEDIATION/PhysicsBabel (2 millions+ équations)

Le signal physique se concentre dans les petites relations. On a donc retenu la
tranche **plausible de taille ≤ 4**, dédupliquée par **signature dimensionnelle**
(deux grandeurs de mêmes dimensions sont interchangeables → même relation), ce
qui donne **3 570 formes distinctes**. Chacune a été **identifiée par un LLM**
(Claude Opus 4.8, via l'API Batch) : loi/nombre connu, ou coïncidence.

| Caractéristique | Valeur |
|---|---|
| Formes distinctes identifiées | **3 570** (taille ≤ 4, dédupliquées) |
| **Connues** (loi/relation physique réelle) | **432** |
| **Inconnues** (coïncidence dimensionnelle) | **3 138** |
| Étiquettes propagées au catalogue complet | 9 105 lignes |
| Modèle d'identification | Claude Opus 4.8 (Batch API) |

Colonnes ajoutées : `statut` (connue/inconnue), `nom` (nom de la loi si connue),
`commentaire` (explication en français), `identification` (id en base64).

Parmi les lois retrouvées automatiquement : Newton, énergie cinétique, Reynolds,
Ohm local, loi de Joule, relation de Planck (E=hf), E=mc², 3ᵉ loi de Kepler,
densités d'énergie électrique/magnétique, pression de Maxwell, quantum de
résistance (von Klitzing / effet Hall quantique)…

---

## Contenu de ce dossier

| Fichier | Rôle |
|---|---|
| `index.html` | explorateur web autonome (recherche, filtres, tri, colonnes redimensionnables, détail au clic) |
| `data.js` | les 3 570 formes identifiées (chargées par la page) |
| `equations.csv` | même extraction, format CSV |
| `build_web.py` | régénère `data.js` et `equations.csv` depuis le catalogue Parquet |

L'explorateur est **100 % statique et sans dépendance externe** : ouvrir
`index.html` (double-clic) suffit.

Les fichiers vashyscan_report.md et .pdf contiennent les équations sous la forme d'un rapport.

Le moteur de génération complet (énumération des circuits, métriques,
identification LLM) vit dans le projet parent VashyScan ; ce dossier n'en est
que la vitrine web.

---

## Déploiement (GitHub Pages)

Ce dossier est le site publié. Configurer GitHub Pages pour le servir (ou copier
son contenu à la racine du site) → https://l0d0v1c.github.io/vashyscan

---

## Licence & citation

Données sous **CC-BY-4.0** (conséquences mathématiques des définitions
dimensionnelles SI). Les étiquettes `statut`/`nom`/`commentaire` sont une
annotation LLM de haute qualité mais **non faisant autorité**.

```bibtex
@misc{vashyscan,
  title  = {VashyScan: criblage complet des équations dimensionnellement cohérentes de la physique},
  note   = {Circuits du matroïde dimensionnel (théorème de Vaschy--Buckingham), identifiés par LLM},
  url    = {https://l0d0v1c.github.io/vashyscan},
  year   = {2026},
  doi    = {10.5281/zenodo.21306738}
}
```
[![DOI](https://zenodo.org/badge/1296862264.svg)](https://doi.org/10.5281/zenodo.21306738)
