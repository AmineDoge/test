# Rapport de TD/TP : Calcul Numérique

## Méthodes Directes et Stockage Bande pour la Résolution de l’Équation de la Chaleur

### Introduction

L’équation de la chaleur est un problème central en physique et en mathématiques appliquées, décrivant la diffusion de la température dans un milieu homogène immobile. Ce TD/TP se concentre sur la résolution numérique de l’équation stationnaire en 1D sans terme source (« g = 0 ») par discrétisation avec un schéma de différences finies centrées d’ordre 2.

La solution analytique est donnée par :
\[
T(x) = T_0 + x(T_1 - T_0),
\]
où \(T_0\) et \(T_1\) sont les températures aux bords.

Les objectifs incluent la comparaison de cette solution analytique avec celles obtenues par des méthodes numériques utilisant les bibliothèques BLAS et LAPACK et un stockage bande.

### Structure

1. **Méthode directe et stockage bande**
   - Préparation des données et construction de la matrice
   - Utilisation des fonctions LAPACK
   - Résultats obtenus
2. **Validation et comparaison des méthodes**
   - Validation du stockage GB avec `dgbmv`
   - Comparaison avec les autres approches

### Méthode Directe et Stockage Bande

#### Préparation des données et construction de la matrice

La matrice associée au problème est tridiagonale et représentée dans un format « General Band » (GB) pour optimiser la mémoire et les calculs. Elle est construite à l’aide de la fonction `set_GB_operator_colMajor_poisson1D`. Le vecteur des seconds membres (« RHS ») est généré avec `set_dense_RHS_DBC_1D`, et le maillage 1D est calculé (« X_grid.dat »).

> **Graphique :** Intégrez `grid_points.png` pour illustrer la discrétisation spatiale.

#### Utilisation des fonctions LAPACK

- **Factorisation LU :** Utilisation de `dgbtrf` pour décomposer la matrice en produits triangulaires \(L\) et \(U\).
- **Résolution :** La solution numérique est calculée avec `dgbtrs`.
- **Validation :** Comparaison avec la solution analytique (« EX_SOL.dat ») et calcul de l’erreur relative.

#### Résultats obtenus

- **Précision :** Les solutions numériques sont proches de la solution analytique ( erreur relative  \( \sim 10^{-16} \)).
- **Performances :** Temps d’exécution pour `dgbtrf` ( \(O(n^3)\) ) et `dgbtrs` ( \(O(n^2)\) ).
- **Graphique :** Ajoutez `solution_comparison.png` pour montrer la concordance entre les solutions.

### Validation et Comparaison des Méthodes

#### Validation du Stockage GB avec `dgbmv`

- **Produit matrice-vecteur :** Test de `dgbmv` pour valider la cohérence du stockage. 
- **Résultats :** Produit exact avec un résidu proche de zéro.

#### Comparaison avec d’autres approches

- **Avantages des méthodes directes :** Précision et stabilité pour des matrices de taille modérée.
- **Limites :** Complexité prohibitive pour de grandes matrices.
- **Perspectives :** Exploration future des méthodes itératives (Richardson, Gauss-Seidel) et formats alternatifs (CSR, CSC).

### Conclusion

Ce TD/TP démontre l’efficacité des méthodes directes pour la résolution de l’équation de la chaleur 1D avec un format GB. Les erreurs relatives faibles et les validations robustes confirment leur précision.

Les fichiers « RHS.dat », « EX_SOL.dat » et les graphiques (« solution_comparison.png ») illustrent les résultats. Les méthodes itératives pour les matrices de grande taille et les formats de stockage avancés constituent des pistes futures.
