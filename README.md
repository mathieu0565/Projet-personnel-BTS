# jeu de A à Z

## Introduction à mon projet de première année : Jeu 1vs2 ##

Dans le cadre de mon projet de première année, j'ai créé un jeu interactif en utilisant SDL2 et SDL_ttf pour la partie graphique. Le but principal du projet est de concevoir une carte où plusieurs joueurs peuvent interagir, se déplacer, attaquer et utiliser des objets comme des potions.

Concept du jeu
Le jeu, intitulé 1vs2 Map, propose un environnement simple où trois joueurs évoluent sur une grille de 10x10 cases :

Chaque joueur possède un nom, des points de vie (HP) initiaux, et des coordonnées (x, y) définissant leur position sur la carte.
Les joueurs peuvent effectuer plusieurs actions, comme se déplacer, attaquer d'autres joueurs ou ramasser des potions.
Fonctionnalités principales
Déplacement des joueurs :
Les joueurs peuvent avancer, reculer, monter ou descendre dans les limites de la carte. Un contrôle a été ajouté pour éviter les collisions avec d'autres joueurs ou les déplacements hors des limites de la carte.

Attaque :

Les joueurs peuvent choisir de frapper un adversaire avec une arme (couteau ou hache), chaque arme infligeant des dégâts spécifiques.
La direction de l'attaque est définie par le joueur.
Si les points de vie (HP) d'un joueur tombent à 0, il est automatiquement retiré de la partie.
Gestion des potions :
Une potion de santé (type 'H') est placée aléatoirement sur la carte. Lorsqu'un joueur ramasse une potion, ses points de vie augmentent. Cela encourage les déplacements stratégiques pour survivre plus longtemps.

Rendu graphique :

Le jeu utilise SDL2 pour afficher les joueurs et les objets sur une carte de 10x10 cases.
Chaque case est visualisée avec des couleurs spécifiques : bleu pour un joueur, vert pour une potion, et blanc pour une case vide.
Interactions dynamiques :

Après un certain nombre d'actions consécutives, des joueurs peuvent se déplacer aléatoirement pour rendre le jeu plus imprévisible.
Le joueur peut choisir de quitter le jeu à tout moment.
Fonctionnement technique
Le jeu repose sur :

Une structure player_s pour gérer les informations des joueurs : nom, coordonnées, HP, etc.
Une structure potion pour représenter les objets de type potion.
Une boucle principale qui gère les événements via SDL_Event, rafraîchit l'affichage, et attend les actions des joueurs.
L'utilisation de SDL_ttf pour afficher les noms des joueurs et d'autres informations textuelles directement sur la carte.

## Code ##
