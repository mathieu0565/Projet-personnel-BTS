# jeu de A à Z

  #include <SDL2/SDL.h>
#include <SDL2/SDL_ttf.h>
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

// Définition de la structure représentant un joueur
typedef struct player {
    char *nom;               // Nom du joueur
    int x;                   // Coordonnée x du joueur sur la carte
    int y;                   // Coordonnée y du joueur sur la carte
    struct player *next;     // Pointeur vers le joueur suivant dans la liste
    int ID;                  // Identifiant unique du joueur
    int HP;                  // Points de vie du joueur
} player_s;

// Définition de la structure représentant une potion
typedef struct potion {
    int x;                   // Coordonnée x de la potion sur la carte
    int y;                   // Coordonnée y de la potion sur la carte
    int ID;                  // Identifiant unique de la potion
    char type;               // Type de potion (par exemple 'H' pour santé)
    struct potion *next;     // Pointeur vers la potion suivante dans la liste
} potion;

// Prototypes des fonctions pour la gestion des joueurs
player_s *ajout_player(char *nom, int x, int y, int ID);
void supprime_tous(player_s *first_player);

// Prototypes des fonctions pour les mouvements des joueurs
void avancer_reculer(player_s *first_player, int direction);
void descendre_avancer(player_s *first_player, int direction);
char demande_deplacement(player_s *first_player, potion *first_p_HP);

// Prototypes des fonctions pour l'attaque
void demande_attaque(player_s *first_player, player_s *current_player);
void check_player_HP(player_s **first_player);

// Prototypes des fonctions pour la gestion des potions
potion *creation_potion(char type, int x, int y, int ID);
void check_potion(potion *first_p_HP, player_s *current_player);
void deplacer_potion_aleatoirement(potion *p);

// Prototypes des fonctions auxiliaires
void affiche_joueur(player_s *first_player, potion *first_p_HP, SDL_Renderer *renderer, TTF_Font *font);
char find_player_from_x_y(player_s *first_player, int x, int y);
void render_text(SDL_Renderer *renderer, TTF_Font *font, const char *text, SDL_Color color, int x, int y);
void check_coordonnee(player_s *current_player, player_s *first_player, int nb_pas, int direction);
void deplacer_joueurs_aleatoirement(player_s *first_player);

// Variable globale pour compter les actions consécutives
int action_count = 0;

// Dimensions de la carte
const int MAP_WIDTH = 10;
const int MAP_HEIGHT = 10;
const int TILE_SIZE = 64;

int main() {
    srand(time(NULL)); // Initialiser la graine aléatoire

    // Initialiser SDL
    if (SDL_Init(SDL_INIT_VIDEO) < 0) {
        printf("Erreur d'initialisation de SDL: %s\n", SDL_GetError());
        return 1;
    }

    if (TTF_Init() == -1) {
        printf("Erreur d'initialisation de SDL_ttf: %s\n", TTF_GetError());
        SDL_Quit();
        return 1;
    }

    SDL_Window *window = SDL_CreateWindow("1vs2 map", SDL_WINDOWPOS_UNDEFINED, SDL_WINDOWPOS_UNDEFINED, MAP_WIDTH * TILE_SIZE, MAP_HEIGHT * TILE_SIZE, SDL_WINDOW_SHOWN);
    if (window == NULL) {
        printf("Erreur de création de la fenêtre: %s\n", SDL_GetError());
        TTF_Quit();
        SDL_Quit();
        return 1;
    }

    SDL_Renderer *renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_ACCELERATED);
    if (renderer == NULL) {
        printf("Erreur de création du renderer: %s\n", SDL_GetError());
        SDL_DestroyWindow(window);
        TTF_Quit();
        SDL_Quit();
        return 1;
    }

    // Remplacez ce chemin par le chemin correct vers une police TTF sur votre système
    TTF_Font *font = TTF_OpenFont("/usr/share/fonts/truetype/dejavu/DejaVuSans.ttf", 28);
    if (font == NULL) {
        printf("Erreur de chargement de la police: %s\n", TTF_GetError());
        SDL_DestroyRenderer(renderer);
        SDL_DestroyWindow(window);
        TTF_Quit();
        SDL_Quit();
        return 1;
    }

    // Créer les joueurs
    player_s *first_player = ajout_player("toto", 3, 3, 1);
    player_s *second_player = ajout_player("mama", 5, 5, 2);
    player_s *trois_player = ajout_player("yoyo", 1, 1, 3);
    first_player->next = second_player;
    second_player->next = trois_player;

    // Créer une potion
    potion *first_p_HP = creation_potion('H', 4, 6, 4);

    // Boucle principale du jeu
    int quit = 0;
    SDL_Event e;
    while (!quit) {
        // Gestion des événements
        while (SDL_PollEvent(&e) != 0) {
            if (e.type == SDL_QUIT) {
                quit = 1;
            }
        }

        // Afficher la carte avec les joueurs et la potion
        SDL_SetRenderDrawColor(renderer, 0xFF, 0xFF, 0xFF, 0xFF);
        SDL_RenderClear(renderer);
        affiche_joueur(first_player, first_p_HP, renderer, font);
        SDL_RenderPresent(renderer);

        // Demander le déplacement ou une action du joueur
        if (demande_deplacement(first_player, first_p_HP) == 1) {
            supprime_tous(first_player);
            break;
        }

        SDL_Delay(100); // Ajouter un délai pour contrôler la vitesse de la boucle
    }

    // Nettoyer SDL
    TTF_CloseFont(font);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    TTF_Quit();
    SDL_Quit();

    return 0;
}

// Fonction pour créer une potion
potion *creation_potion(char type, int x, int y, int ID) {
    potion *p = malloc(sizeof(potion)); // Allouer de la mémoire pour une nouvelle potion
    p->x = x;                           // Initialiser les coordonnées x de la potion
    p->y = y;                           // Initialiser les coordonnées y de la potion
    p->ID = ID;                         // Initialiser l'ID de la potion
    p->type = type;                     // Initialiser le type de la potion
    p->next = NULL;                     // Initialiser le pointeur vers la potion suivante à NULL

    return p;                           // Retourner le pointeur vers la nouvelle potion
}

// Fonction pour ajouter un joueur
player_s *ajout_player(char *nom, int x, int y, int ID) {
    player_s *new_player = malloc(sizeof(player_s)); // Allouer de la mémoire pour un nouveau joueur
    new_player->x = x;                               // Initialiser les coordonnées x du joueur
    new_player->y = y;                               // Initialiser les coordonnées y du joueur
    new_player->nom = nom;                           // Initialiser le nom du joueur
    new_player->next = NULL;                         // Initialiser le pointeur vers le joueur suivant à NULL
    new_player->ID = ID;                             // Initialiser l'ID du joueur
    new_player->HP = 100;                            // Initialiser les points de vie du joueur

    return new_player;                               // Retourner le pointeur vers le nouveau joueur
}

// Fonction pour afficher les joueurs
void affiche_joueur(player_s *first_player, potion *first_p_HP, SDL_Renderer *renderer, TTF_Font *font) {
    int y = 0;
    while (y != MAP_HEIGHT) {
        int x = 0;

        while (x != MAP_WIDTH) {
            char c = find_player_from_x_y(first_player, x, y); // Trouver un joueur aux coordonnées (x, y)

            SDL_Rect fillRect = { x * TILE_SIZE, y * TILE_SIZE, TILE_SIZE, TILE_SIZE };

            if (c == 0) {
                if (first_p_HP->x == x && first_p_HP->y == y) {
                    SDL_SetRenderDrawColor(renderer, 0x00, 0x80, 0x00, 0xFF); // vert pour la potion
                    SDL_RenderFillRect(renderer, &fillRect);
                } else {
                    SDL_SetRenderDrawColor(renderer, 0xFF, 0xFF, 0xFF, 0xFF); // Blanc pour une case vide
                    SDL_RenderFillRect(renderer, &fillRect);
                    SDL_SetRenderDrawColor(renderer, 0x00, 0x00, 0x00, 0xFF); // Noir pour les bordures
                    SDL_RenderDrawRect(renderer, &fillRect);
                }
            } else {
                SDL_Color textColor;
                if (c == 't') {
                    SDL_SetRenderDrawColor(renderer, 0x00, 0x00, 0xFF, 0xFF); // Bleu pour "toto"
                    textColor = (SDL_Color){0xFF, 0xFF, 0xFF, 0xFF}; // Blanc pour le texte
                } else {
                    SDL_SetRenderDrawColor(renderer, 0x00, 0x00, 0x00, 0xFF); // Noir pour "mama" et "yoyo"
                    textColor = (SDL_Color){0xFF, 0xFF, 0xFF, 0xFF}; // Blanc pour le texte des players "mama" et "yoyo"
                }
                SDL_RenderFillRect(renderer, &fillRect);
                render_text(renderer, font, &c, textColor, x * TILE_SIZE, y * TILE_SIZE);
                SDL_SetRenderDrawColor(renderer, 0x00, 0x00, 0x00, 0xFF); // Noir pour les bordures
                SDL_RenderDrawRect(renderer, &fillRect);
            }

            x++;
        }
        y++;
    }
}

// Fonction pour trouver un joueur à une position spécifique
char find_player_from_x_y(player_s *first_player, int x, int y) {
    while (first_player != NULL) {
        if (x == first_player->x && y == first_player->y) {
            return first_player->nom[0]; // Retourner l'initiale du nom du joueur
        }
        first_player = first_player->next;
    }
    return 0; // Retourner 0 si aucun joueur n'est trouvé
}

// Fonction pour rendre du texte à l'écran
void render_text(SDL_Renderer *renderer, TTF_Font *font, const char *text, SDL_Color color, int x, int y) {
    SDL_Surface *textSurface = TTF_RenderText_Solid(font, text, color);
    SDL_Texture *textTexture = SDL_CreateTextureFromSurface(renderer, textSurface);
    SDL_Rect renderQuad = { x, y, textSurface->w, textSurface->h };
    SDL_RenderCopy(renderer, textTexture, NULL, &renderQuad);
    SDL_DestroyTexture(textTexture);
    SDL_FreeSurface(textSurface);
}

// Fonction pour avancer ou reculer le joueur
void avancer_reculer(player_s *first_player, int direction) {
    int nb_pas;
    printf("\nVous voulez avancer de combien de pas ? (max : 10)\n");
    scanf("%d", &nb_pas);

    // Vérifier si le nombre de pas est valide
    if (nb_pas >= 0 && nb_pas <= 10) {
        int new_x = first_player->x + (nb_pas * direction); // Calculer la nouvelle coordonnée x
        if (new_x >= 0 && new_x < MAP_WIDTH) { // Vérifier si le déplacement est dans les limites de la carte
            first_player->x = new_x; // Mettre à jour la coordonnée x du joueur
            printf("Coordonnée : x = %d, y = %d\n", first_player->x, first_player->y);
            player_s *current_player = first_player;
            first_player = first_player->next;
            check_coordonnee(current_player, first_player, nb_pas, -1); // Vérifier les coordonnées pour éviter les collisions
        } else {
            printf("Déplacement hors des limites de la carte, réessayez.\n");
        }
    } else {
        printf("Pas le bon chiffre, réessayez !\n");
    }
}

// Fonction pour monter ou descendre le joueur
void descendre_avancer(player_s *first_player, int direction) {
    int nb_pas;
    printf("\nVous voulez avancer de combien de pas ? (max : 10)\n");
    scanf("%d", &nb_pas);

    // Vérifier si le nombre de pas est valide
    if (nb_pas >= 0 && nb_pas <= 10) {
        int new_y = first_player->y + (nb_pas * direction); // Calculer la nouvelle coordonnée y
        if (new_y >= 0 && new_y < MAP_HEIGHT) { // Vérifier si le déplacement est dans les limites de la carte
            first_player->y = new_y; // Mettre à jour la coordonnée y du joueur
            printf("Coordonnée : x = %d, y = %d\n", first_player->x, first_player->y);
            player_s *current_player = first_player;
            first_player = first_player->next;
            check_coordonnee(current_player, first_player, nb_pas, -1); // Vérifier les coordonnées pour éviter les collisions
        } else {
            printf("Déplacement hors des limites de la carte, réessayez.\n");
        }
    } else {
        printf("Pas le bon chiffre, réessayez !\n");
    }
}

// Fonction pour demander et exécuter un déplacement ou une action
char demande_deplacement(player_s *first_player, potion *first_p_HP) {
    int choix;
    printf("\nActions de mouvement:");
    printf("\n1. Avancer");
    printf("\n2. Reculer");
    printf("\n4. Monter");
    printf("\n5. Descendre\n");

    printf("\nActions d'attaque:");
    printf("\n3. Attaquer");
    printf("\n9. Arrêter le jeu\n");
    scanf("%d", &choix);

    player_s *current_player = first_player;

    // Catégorie mouvement
    if (choix == 1 || choix == 2 || choix == 4 || choix == 5) {
        int direction_g_d = (choix == 1) ? 1 : ((choix == 2) ? -1 : 0);
        int direction_h_b = (choix == 5) ? 1 : ((choix == 4) ? -1 : 0);

        if (direction_g_d != 0) {
            avancer_reculer(first_player, direction_g_d); // Déplacer le joueur horizontalement
        } else if (direction_h_b != 0) {
            descendre_avancer(first_player, direction_h_b); // Déplacer le joueur verticalement
        }

        check_potion(first_p_HP, current_player); // Vérifier si le joueur a ramassé une potion
    }
    // Catégorie attaque
    else if (choix == 3) {
        first_player = first_player->next;
        demande_attaque(first_player, current_player); // Demander au joueur d'attaquer

        check_player_HP(&first_player); // Vérifier les points de vie des joueurs et supprimer ceux qui sont à 0
    }
    // Arrêter le jeu
    else if (choix == 9) {
        return 1; // Arrêter le jeu
    }

    // Incrémenter le compteur d'actions
    action_count++;
    if (action_count >= 4) {
        deplacer_joueurs_aleatoirement(first_player); // Déplacer certains joueurs aléatoirement après un certain nombre d'actions
        action_count = 0;
    }

    return 0;
}

// Fonction pour demander une attaque
void demande_attaque(player_s *first_player, player_s *current_player) {
    int direction;
    int choix_attaque;
    
    printf("\nChoisissez la direction de l'attaque:");
    printf("\n1. Devant");
    printf("\n2. Derrière");
    printf("\n4. Haut");
    printf("\n5. Bas\n");
    scanf("%d", &direction);

    printf("Choisissez le type d'attaque:");
    printf("\n1. Attaque au couteau (-20HP)");
    printf("\n2. Attaque à la hache (-50HP)\n");
    scanf("%d", &choix_attaque);

    // Déterminer la direction de l'attaque en fonction du choix de l'utilisateur
    int direction_g_d = (direction == 1) ? 1 : ((direction == 2) ? -1 : 0);
    int direction_h_b = (direction == 4) ? -1 : ((direction == 5) ? 1 : 0);

    player_s *temp_player = first_player;
    
    // Parcourir tous les joueurs pour vérifier s'ils se trouvent dans la direction de l'attaque
    while (temp_player != NULL) {
        if (temp_player->ID != current_player->ID) {
            if (direction_g_d != 0) {
                if (current_player->x + direction_g_d == temp_player->x && current_player->y == temp_player->y) {
                    int attaque = (choix_attaque == 1) ? 20 : 50; // Déterminer les dégâts de l'attaque
                    temp_player->HP -= attaque; // Réduire les points de vie du joueur attaqué
                    printf("Le joueur %s attaque le joueur %s\n", current_player->nom, temp_player->nom);
                    printf("Il reste %d HP au joueur %s\n", temp_player->HP, temp_player->nom);
                    return;
                }
            } else if (direction_h_b != 0) {
                if (current_player->y + direction_h_b == temp_player->y && current_player->x == temp_player->x) {
                    int attaque = (choix_attaque == 1) ? 20 : 50; // Déterminer les dégâts de l'attaque
                    temp_player->HP -= attaque; // Réduire les points de vie du joueur attaqué
                    printf("Le joueur %s attaque le joueur %s\n", current_player->nom, temp_player->nom);
                    printf("Il reste %d HP au joueur %s\n", temp_player->HP, temp_player->nom);
                    return;
                }
            }
        }
        temp_player = temp_player->next;
    }
}

// Fonction pour vérifier et ajuster les coordonnées en cas de collision
void check_coordonnee(player_s *current_player, player_s *first_player, int nb_pas, int direction) {
    while (first_player != NULL) {
        if (current_player->x == first_player->x && current_player->y == first_player->y) {
            current_player->x += nb_pas * direction; // Ajuster la coordonnée x pour éviter la collision
            current_player->y += nb_pas * direction; // Ajuster la coordonnée y pour éviter la collision
            printf("Vous êtes sur les mêmes coordonnées\n");
            break;
        }
        first_player = first_player->next;
    }
}

// Fonction pour vérifier si un joueur ramasse une potion
void check_potion(potion *first_p_HP, player_s *current_player) {
    if (current_player->x == first_p_HP->x && current_player->y == first_p_HP->y) {
        current_player->HP += 50; // Augmenter les points de vie du joueur
        printf("Le joueur %s a maintenant %d HP\n", current_player->nom, current_player->HP);
        deplacer_potion_aleatoirement(first_p_HP); // Déplacer la potion à une nouvelle position aléatoire
    }
}

// Fonction pour vérifier les HP des joueurs et supprimer ceux qui sont à 0
void check_player_HP(player_s **first_player) {
    player_s *prev = NULL;
    player_s *current = *first_player;

    while (current != NULL) {
        if (current->HP <= 0) {
            if (prev == NULL) { // Si le joueur à supprimer est le premier de la liste
                *first_player = current->next;
            } else {
                prev->next = current->next;
            }
            free(current); // Libérer la mémoire du joueur supprimé
            current = (prev == NULL) ? *first_player : prev->next;
        } else {
            prev = current;
            current = current->next;
        }
    }
}

// Fonction pour supprimer tous les joueurs (libérer la mémoire)
void supprime_tous(player_s *first_player) {
    while (first_player != NULL) {
        player_s *next = first_player->next;
        free(first_player); // Libérer la mémoire du joueur
        first_player = next;
    }
}

// Fonction pour déplacer les joueurs "mama" et "yoyo" aléatoirement sur la carte
void deplacer_joueurs_aleatoirement(player_s *first_player) {
    player_s *current = first_player;
    while (current != NULL) {
        // Comparer les noms manuellement sans utiliser string.h
        if ((current->nom[0] == 'm' && current->nom[1] == 'a' && current->nom[2] == 'm' && current->nom[3] == 'a') ||
            (current->nom[0] == 'y' && current->nom[1] == 'o' && current->nom[2] == 'y' && current->nom[3] == 'o')) {
            current->x = rand() % MAP_WIDTH; // Déplacer le joueur à une nouvelle position x aléatoire
            current->y = rand() % MAP_HEIGHT; // Déplacer le joueur à une nouvelle position y aléatoire
            printf("Le joueur %s a été déplacé à la position (%d, %d)\n", current->nom, current->x, current->y);
        }
        current = current->next;
    }
}

// Fonction pour déplacer une potion aléatoirement sur la carte
void deplacer_potion_aleatoirement(potion *p) {
    p->x = rand() % MAP_WIDTH;
    p->y = rand() % MAP_HEIGHT;
    printf("La potion a été déplacée à la position (%d, %d)\n", p->x, p->y);
}

