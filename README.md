# 🎮 Jeu 1vs2 - Projet de Première Année

Bienvenue dans le dépôt GitHub de **1vs2 Map**, un jeu interactif développé dans le cadre de mon projet de première année. Ce projet utilise **SDL2** et **SDL_ttf** pour offrir un rendu graphique simple mais efficace. 🕹️

---

## 📝 Description du Projet

**1vs2 Map** est un jeu où trois joueurs évoluent sur une grille de 10x10 cases. Ils peuvent interagir de différentes manières :  
- **Se déplacer**
- **Attaquer des adversaires**
- **Ramasser des potions** pour augmenter leur chance de survie.

Le projet vise à combiner une expérience de jeu interactive avec une gestion stratégique des actions, le tout dans un environnement graphique minimaliste.

---

## 🎮 Fonctionnalités Principales

### 🔄 Déplacement des joueurs
- Les joueurs peuvent avancer, reculer, monter ou descendre dans les limites de la carte.  
- Les collisions entre joueurs et les déplacements hors des limites de la carte sont empêchés.  

### ⚔️ Attaque
- Chaque joueur peut attaquer un adversaire avec une arme : **couteau** ou **hache**.  
- Chaque arme inflige des dégâts spécifiques.  
- Si les points de vie (HP) d'un joueur tombent à **0**, il est retiré du jeu.  

### 💊 Gestion des potions
- Une potion de santé (type 'H') est placée **aléatoirement** sur la carte.  
- Lorsqu'un joueur ramasse une potion, ses points de vie augmentent, offrant un avantage stratégique.  

### 🖥️ Rendu graphique
- Le jeu affiche les joueurs et les objets sur une grille de **10x10 cases**.  
- Les couleurs permettent de distinguer les éléments :  
  - 🟦 **Bleu** : Joueurs  
  - 🟩 **Vert** : Potions  
  - ⚪ **Blanc** : Cases vides  

### 🔀 Interactions dynamiques
- Après un certain nombre d'actions, des joueurs peuvent se déplacer aléatoirement pour ajouter un élément d’imprévisibilité.  
- Les joueurs peuvent **quitter la partie à tout moment**.  

---

## 🛠️ Fonctionnement Technique

### Structures utilisées
- **`player_s`** : Contient les informations des joueurs (nom, coordonnées, HP, etc.).  
- **`potion`** : Représente les objets de type potion.  

### Boucle principale
- Gère les événements via **SDL_Event**.  
- Rafraîchit l’affichage en temps réel.  
- Attend les actions des joueurs.  

### SDL_ttf
- Utilisé pour afficher les **noms des joueurs** et d’autres informations textuelles directement sur la carte.  

---

## 🚀 Améliorations Futures

Voici quelques idées pour rendre le jeu encore plus captivant :  
- Ajouter des **animations** pour les attaques et les déplacements.  
- Intégrer différents types de potions avec des effets variés (exemple : **bouclier** ou **augmentation de vitesse**).  
- Proposer un **mode multijoueur** en ligne ou en local.  
- Améliorer l’interface utilisateur avec des menus plus intuitifs et une meilleure esthétique.  

---

## 📸 Aperçu

*(Ajoutez ici des captures d'écran ou des GIFs du jeu en action pour donner un aperçu visuel aux lecteurs.)*

---

## 📦 Installation et Exécution

1. Clonez ce dépôt :  
   ```bash
   git clone https://github.com/votre-utilisateur/jeu-1vs2.git
   cd jeu-1vs2

## Code ##
