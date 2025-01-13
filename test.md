# Rapport de TD/TP : Calcul Numérique

## Introduction

La résolution numérique de l’équation de la chaleur en 1D constitue une étape fondamentale pour comprendre la diffusion thermique dans des systèmes physiques. Ce rapport présente les étapes de résolution à travers des méthodes directes et itératives, en utilisant des bibliothèques de calcul BLAS et LAPACK. Le travail s’est déroulé en trois parties : mise en place de l’environnement, méthodes directes et méthodes itératives.

---

## Partie 1 : Préparation de l'environnement

### Mise en place

L'environnement de travail est configuré via un conteneur Docker permettant l'utilisation des bibliothèques CBLAS et LAPACK. Une série de tests a permis de valider leur bonne installation. Voici les résultats principaux :

- Valeur exponentielle (« e ») : 2.718282.
- Valeurs maximales en simple et double précision :
  - flt\_max = 3.402823e+38
  - dbl\_max = 1.797693e+308
- Précision relative :
  - flt\_epsilon = 1.192093e-07
  - dbl\_epsilon = 2.220446e-16

Les fonctions BLAS comme `DCOPY` ont été testées avec succès, confirmant la présence d'un environnement fonctionnel.

---

## Partie 2 : Méthodes Directes

### Préparation des Données

La matrice associée à l'équation de Poisson en 1D est une matrice tridiagonale, ce qui permet un stockage efficace en format bande général (GB). Ce stockage est conçu pour minimiser l'utilisation mémoire tout en préservant les éléments nécessaires aux calculs. La matrice est construite à l'aide de la fonction `set_GB_operator_colMajor_poisson1D`, et le vecteur des seconds membres (RHS) est généré par la fonction `set_dense_RHS_DBC_1D`, qui intègre les conditions aux limites imposées.

#### Détails Techniques

- **Stockage Bande :** Une matrice tridiagonale de taille \( n \times n \) avec \( kl \) sous-diagonales et \( ku \) sur-diagonales est compactée dans un tableau à deux dimensions de taille \( (kl + ku + 1) \times n \). Ce format permet de réduire significativement l'occupation mémoire.
- **Méthodes Utilisées :** Les fonctions `dgbmv` (produit matrice-vecteur) et `dgbtrf` (factorisation LU) ont été employées pour optimiser les opérations sur ce type de matrice.

> **Image à insérer ici :** `matrix_AB.png`

### Résolution avec LAPACK

La factorisation LU, réalisée avec la fonction `dgbtrf`, décompose la matrice en deux matrices triangulaires \( L \) (inférieure) et \( U \) (supérieure). La solution est ensuite obtenue à l'aide de `dgbtrs`. Une validation des résultats a été effectuée en comparant les solutions numériques aux solutions analytiques.

#### Validation et Précision

- **Solution Analytique :**
  \[
  T(x) = T_0 + x(T_1 - T_0)
  \]
  où \( T_0 \) et \( T_1 \) sont les conditions aux limites. La solution numérique obtenue est très proche de cette solution exacte, avec une erreur relative typique de l'ordre de \( 10^{-16} \).

> **Image à insérer ici :** `matrix_LU.png`

### Comparaison des Solutions

Un graphique compare les solutions analytiques et numériques, mettant en évidence leur concordance et validant la précision de la méthode directe.

> **Image à insérer ici :** `solution_comparison.png`

### Performances et Complexité

- **Temps d’Exécution :** Le temps d'exécution de la factorisation LU (via `dgbtrf`) suit une complexité en \( O(n^3) \), tandis que la résolution du système (via `dgbtrs`) est plus rapide, avec une complexité en \( O(n^2) \).
- **Stockage :** Le format bande réduit significativement l'occupation mémoire, le rendant idéal pour des matrices de taille modérée.

### Maillage et Discrétisation

Le domaine est divisé en \( n+2 \) points de grille, avec un pas constant \( h \). Le maillage permet une discrétisation précise et est à la base de tous les calculs numériques réalisés.

> **Image à insérer ici :** `grid_points.png`

---

## Partie 3 : Méthodes Itératives

### Algorithme de Richardson

L’algorithme de Richardson est une méthode itérative simple qui consiste à mettre à jour la solution à chaque itération en utilisant un facteur de relaxation \( \alpha \). Ce paramètre est calculé de manière optimale pour garantir une convergence rapide.

#### Fonctionnement

1. Initialisation :
   - La solution initiale est à zéro.
   - Le résidu est calculé comme \( r = b - Ax \).
2. Mise à jour :
   - \( x_{k+1} = x_k + \alpha r_k \)
3. Convergence :
   - L'algorithme s'arrête lorsque le résidu atteint un seuil prédéfini.

#### Résultats

- L’erreur relative par rapport à la solution analytique est \( 5.093853 \times 10^{-3} \).
- La convergence est rapide, comme illustré dans le graphique des résidus :

> **Image à insérer ici :** `convergence.png`

### Comparaison avec Jacobi et Gauss-Seidel

Bien que seules les itérations de Richardson aient été implémentées ici, une étude plus large pourrait inclure Jacobi et Gauss-Seidel, qui présentent des caractéristiques de convergence différentes.

---

## Analyse Comparative

| Critère            | Méthode Directe           | Méthodes Itératives       |
|--------------------|---------------------------|---------------------------|
| **Précision**      | Très élevée (écart faible)  | Modérée                  |
| **Complexité**     | \( O(n^3) \) pour `dgbtrf`  | Linéaire (à chaque itération) |
| **Utilisation**    | Systèmes de taille modérée | Grandes matrices          |

---

## Conclusion

Ce rapport met en avant deux approches complémentaires pour la résolution de l’équation de Poisson en 1D :

1. **Méthodes Directes :** Idéales pour les problèmes de taille modérée, avec une précision très élevée.
2. **Méthodes Itératives :** Plus adaptées aux grands systèmes grâce à leur complexité linéaire par itération.

Les travaux futurs pourraient inclure l’exploration d’autres formats de stockage comme CSR ou CSC, ainsi qu’une étude sur la parallélisation des algorithmes itératifs.
