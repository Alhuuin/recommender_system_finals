# Projet Final - Recommender System Course

Ce repo contient, comme demandé, un recommender system permettant de recommander un ou des films pour un couple d'utilisateurs, sous la forme d'un jupyter python, accompagné d'un rapport - ce présent README.md.

# Préparation

Pour le bon fonctionnement du code, les données devront au préalable être téléchargées. Les deux datasets utilisés sont [movielens](https://grouplens.org/datasets/movielens/) et [imdb](https://datasets.imdbws.com/).

En ce qui concerne les données utilisées, j'utilise tous les fichiers du dataset movielens (movies, ratings et users), et seulement 2 fichiers du dataset imdb : title.basics, contenant des informations sur le film lui-même, et title.crew, contenant des informations sur les directeurs et/ou auteurs du film.

N'hésitez pas à modifier le path du dossier contenant les données pour le bon fonctionnement du code.

Ce dossier devra avoir 2 sous-dossier :
- movielens_complete/ml-1m : pour les données du dataset movielens
- imdb : pour les données du dataset IMdb.

Si vous ne possédez pas déjà les données de ces deux datasets, des cellules sont présentes dans le jupyter pour permettre de les télécharger.

# Déroulement du projet

## Récupération des données

La récupération des données a déjà évoquée. Passons maintenant au merge des deux datasets :

Les features qui pouvaient m'être utile, et que j'ai donc récupérées, sont:
- les ratings
- le nom du film concerné, contenant l'année de production, c'est-à-dire le format qu'ont les titres de films dans le dataset movielens. Cela me permet de merge les deux datasets en fonction des titres, tout en permettant de distinguer les rééditions de films ou possibles différentes version, sans pour autant avoir besoin d'effectuer de vérifications en observant le contenu d'une autre colonne.
- le genre du film
- le nom du ou des directeur(s) du film
- le nom de l'auteur du film

Je n'ai pas utilisé toutes ces features dans toutes mes tentatives, mais ce sont les features que j'ai envisagé d'utiliser.

Les autres informations, telles que le ZipCode des utilisateurs, les titre originaux ou la  durée du film, ont été supprimé car non utilisés.

Le dataset imdb ne contenant pas que des films, je n'ai concervé que les entrées "movies" et "tvmovies", que j'ai traité de la même façon. La feature "titleType" ne m'était donc plus utile non plus.

## Feature Engineering

La première version de mon recommender se basait sur la popularité, comme dans l'exemple que nous avons vu en cours. 

Elle prenait donc en compte les notates qu'avaient reçues les films, et le nombre de notations, mais pas les informations propres aux utilisateurs ou au film lui-même.

Cette méthode n'ayant pas des résultats suffisants à mes yeux, j'ai décidé de me tourner vers une méthode utilisant plus de features.

Pour ce deuxième essai, j'ai donc essayé de faire entrer en compte plus d'informations sur les films et les utilisateurs. N'ayant pas trouvé de solution pour ce que je voulais réaliser dans le cours, j'ai fait mes recherches et ai choisi d'utiliser la librairie "surprise". 

C'est une librairie permettant de créer et d'évaluer des systèmes de recommendation basé sur un filtrage collaboratif, en utilisant une factorisation matricielle. Cela me paraissait donc adapté. C'est la solution qui m'a permit d'obtenir les résultats les plus intéressants, tout en étant adaptable et efficace.

En ce qui concerne la combinaison des données utilisateurs, j'ai préféré le faire à la fin, après avoir analysé les préférence de chacun. 

En effet, les prédictions de film prenant en compte de nombreux paramètres (préférence en terme de genre, de directeur, et information utilisateurs), il ne me semblait pas pratique de fusionner les données des deux utilisateurs avant la prédiction de film.

## Développement du modèle de recommandation

Comme précédemment évoqué, j'ai utilisé la bibliothèque "surprise" pour gérer cela. J'ai utiliser un modèle SVD, que j'ai ensuite entraîné sur le trainset, et testé sur le testset extrait auparavant. 

Pour faire cela, j'ai utilisé les outils tels que Reader() et Dataset(), qui sont nécessaire à la prédiction avec cette bibliothèque. Le choix de SVD est justifiable par le fait que c'est une méthode qui se révèle robuste efficace lorsqu'il s'agit de traiter la prédiction basée sur des features telles que les préférences utilisateurs.

## Evaluation

Après quelques recherches, j'ai décidé d'évaluer mon système de recommendation à l'aide de la valeur de RMSE (Root Mean Squared Error). En effet, c'est une valeur sur laquelle les erreurs importantes ont un gros impact, ces dernières étant mises au carré avant qu'une moyenne soit faite. 

Dans notre cas, ce sont les cas qu'ils faut à tout prix éviter. En effet, recommander un film que les utilisateurs n'apprécieront pas beaucoup (3*), est acceptable si cela ne se reproduit pas trop souvent. 

En revanche, leur recommander un film qu'ils n'apprécieront pas du tout (1-2*), est inadmissible. L'évaluation de notre système de recommendation doit donc refléter cette priorité.

De plus, c'est une valeur qui est interprétable très facilement. Sur notre échelle de 1 à 5 étoiles, mon résultat, 0.87, est interprété comme une erreur de prédiction de 0.87 étoiles. 

On peut en conclure que si l'on conseille des films que les utilisateurs devraient aimer, et dont on prévoit donc la note à 5 étoiles, la note réelle devrait rarement descendre en dessous de 4 étoiles. 

On peut donc considérer que ce système est plutôt efficace.

