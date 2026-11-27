# Do deep neural networks learn low-frequency components first? (*)

## Structure du dépôt

```
low_frequencies_hddl/
├── 📁 results/     # figures, métriques, logs
├── 📁 notebooks/     # exploration et visualisations
└── 📁 code/
    ├── 📄 main.py    # point d'entrée (entraînement / éval)
    └── 📁 models/    # architectures des réseaux
        └── ...

```
## Trame de notre étude :

### 1) Spectral bias
Etude fondée sur la notion de "Spectral Bias", décrite dans l'article "On the Spectral Bias of Neural Networks" (https://arxiv.org/abs/1806.08734)
-> les MLP apprennent d'abord très bien les basses fréquences puis les hautes fréquences (qui restent souvent mal reproduites).
Les auteurs s'intéressent à la représentation implicite d'une fonction apprise par un MLP, type Coordinate-based MLP ou NeRF
-> Première réponse à la question (*) : oui, d'après résultats empiriques et tentatives d'explications théoriques

### 2) Technique d'atténuation du Spectral bias
Néanmoins, le phénomène de "spectral bias" peut être considérablement atténué par deux techniques :
a) Fourier Feature encoding : encodage des entrées sous forme de paires de cosinus et sinus à différentes fréquences
(décrit dans "Fourier Features Let Networks Learn High Frequency Functions in Low Dimensional Domains", https://arxiv.org/pdf/2006.10739)

b) réseau SIREN : remplacement des fonctions d'activations du réseau (ReLU) par des fonctions périodiques (sinus)
(décrit "Implicit Neural Representations with Periodic Activation Functions", https://arxiv.org/abs/2006.09661)

-> Nuance apportée à notre réponse formulée au 1) : il est possible d'atténuer la tendance des réseaux à apprendre les basses fréquences en premier


## Plan de travail : 
1. a) Paul :  étudier les explications théoriques du "spectral bias", les raisons pourquoi le "fourier feature encoding" et "SIREN" atténuent le spectral bias
   b) Ana et Brice : faire un notebook dans lequel on compare les performances et les dynamiques d'apprentissage (basses fréquences puis hautes fréquences ?) de 3 réseaux pour une fonction simple 1D:
   - un MLP simple entrainé sur N1 epochs
   - ce même MLP entrainé plus longtemps sur N2 > N1 epochs
   - un MLP avec entrées encodées selon fourier feature entrainé sur N1 epochs
   - un MLP type SIREN entrainé sur N1 epochs
   Les 3 réseaux ont le mêmes nombre de poids, et une architecture presque identique.

2. Réaliser une deuxième expérience, plus complexe qu'une fonction 1D, sur, à définir :
   - soit un PINN (EDP avec fonction à valeurs dans R2 pour observer la reproduction du champs de la solution)
   - soit un NeRF (reproduction d'une scène en 3D)
  Réseau plus profond, et réalisation plus technique (construire le dataset, entrainement sur GPU, plusieurs modules .py pour le code...)

3. Conclure en fonction des résultats observés, des résultats attendus selon la littérature

