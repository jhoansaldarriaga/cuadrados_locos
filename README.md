# Cuadrados Locos

Este es un simple juego creado con Pygame en el que el jugador controla un cuadrado rojo y debe evitar colisionar con cuadrados azules que aparecen en la pantalla. El objetivo es sobrevivir a la aparición de cinco cuadrados azules para ganar el juego.

## Instrucciones

1. **Controles del Jugador:**
   - Usa las teclas direccionales (`↑`, `↓`, `←`, `→`) para mover el cuadrado rojo hacia arriba, abajo, izquierda o derecha, respectivamente.

2. **Mecánica del Juego:**
   - El jugador debe evitar colisionar con los cuadrados azules.
   - El juego finaliza si el cuadrado rojo colisiona con uno de los cuadrados azules.
   - El jugador gana al sobrevivir a la aparición de cinco cuadrados azules.

3. **Reiniciar el Juego:**
   - En caso de perder o ganar, puedes reiniciar el juego haciendo clic en el botón "Reiniciar".

## Notas Adicionales:

El juego tiene un límite de cinco cuadrados azules que pueden aparecer en pantalla.
- Se muestra un mensaje de "Perdiste" si el jugador colisiona con un cuadrado azul.
- Se muestra un mensaje de "Ganaste" si el jugador sobrevive a la aparición de cinco cuadrados azules.

## Requisitos:
Asegúrate de tener la biblioteca Pygame instalada para ejecutar el código. 
¡Diviértete jugando a Cuadrados Locos!

## Código Fuente

```python
import pygame
import sys
import random

# Inicialización de Pygame
pygame.init()

# Definición de colores
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# Configuración de la pantalla
WIDTH, HEIGHT = 600, 400
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Cuadrados locos")

# Definición de variables del juego
player_size = 30
player_x = 30
player_y = HEIGHT // 2 - player_size // 2

enemy_size = 30
enemy_speed = 2  # Ajusta la velocidad según sea necesario
enemies = [(WIDTH - 30, random.randint(0, HEIGHT - enemy_size))]

font = pygame.font.Font(None, 36)  # Fuente y tamaño del texto

clock = pygame.time.Clock()

# Control del tiempo para agregar enemigos
enemy_spawn_time = pygame.time.get_ticks()
enemy_spawn_interval = 20000  # 20 segundos en milisegundos

# Función principal del juego
def game():

    global player_x, player_y, enemies, enemy_spawn_time

    game_over_text = None  # Inicializamos el texto de "Lo siento, perdiste"
    you_win_text = None  # Inicializamos el texto de "¡Qué bien!¡Ganaste!"
    bye_bye_text = None  # Inicializamos el texto de "Ten bonito día"
    restart_button = pygame.Rect(WIDTH // 2-75, HEIGHT // 2 + 50, 150, 40)
    restart_text = font.render("Reiniciar", True, WHITE)

    max_enemies = 5 # cantidad maxima de enemigos
    enemies_number = 1 #cantidad de enemigos iniciales

    while True:

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()

            elif (event.type == pygame.MOUSEBUTTONDOWN and 
                ((game_over_text is not None) or (you_win_text is not None))):
                
                mouse_pos = pygame.mouse.get_pos()

                if restart_button.collidepoint(mouse_pos):
                    reset_game()
                    game_over_text = None  # Limpiamos el texto de "Perder"
                    you_win_text = None # Limpiamos el texto de "Ganar"
                    enemies_number = 1 #se reestablece el numero incial de enemigos

        #Detectar entrada de teclado
        keys = pygame.key.get_pressed()
        player_speed = 5 #velocidad del jugador

        #MECANICA DEL JUEGO DISPONIBLE MIENTRAS enemigos< max_enemies
        if enemies_number <= max_enemies:
            # Movimiento del jugador
            if keys[pygame.K_LEFT] and player_x > 0:
                player_x -= player_speed
            if keys[pygame.K_RIGHT] and player_x < WIDTH - player_size:
                player_x += player_speed
            if keys[pygame.K_UP] and player_y > 0:
                player_y -= player_speed
            if keys[pygame.K_DOWN] and player_y < HEIGHT - player_size:
                player_y += player_speed

            # Verificar el tiempo y agregar un nuevo enemigo si ha pasado el intervalo
            current_time = pygame.time.get_ticks()

            if current_time - enemy_spawn_time > enemy_spawn_interval:
                enemies.append((WIDTH - 30, random.randint(0, HEIGHT - enemy_size)))
                enemy_spawn_time = current_time
                enemies_number += 1 #contar enemigos presentes

            # Movimiento de los enemigos (azules) hacia el jugador (rojo)
            for i in range(len(enemies)):
                if enemies[i][0] > player_x:
                    enemies[i] = (enemies[i][0] - enemy_speed, enemies[i][1])
                elif enemies[i][0] < player_x:
                    enemies[i] = (enemies[i][0] + enemy_speed, enemies[i][1])

                if enemies[i][1] > player_y:
                    enemies[i] = (enemies[i][0], enemies[i][1] - enemy_speed)
                elif enemies[i][1] < player_y:
                    enemies[i] = (enemies[i][0], enemies[i][1] + enemy_speed)

                # Verificar colisión con el jugador
                if (player_x < enemies[i][0] + enemy_size
                    and player_x + player_size > enemies[i][0]
                    and player_y < enemies[i][1] + enemy_size
                    and player_y + player_size > enemies[i][1]):

                    if game_over_text is None:
                        game_over_text = font.render("Lo siento, perdiste", True, RED)
                        enemies_number = max_enemies + 10

        # Dibujar el fondo y el jugador
        screen.fill(WHITE)
        pygame.draw.rect(screen, RED, (player_x, player_y, player_size, player_size))

        # Dibujar los enemigos
        for enemy in enemies:
            pygame.draw.rect(screen, BLUE, (*enemy, enemy_size, enemy_size))

        # Mostrar el texto "Perdiste" y el botón "Reiniciar" en la pantalla si ocurrió una colisión
        if game_over_text is not None:
            pygame.draw.rect(screen, BLACK, restart_button)
            screen.blit(game_over_text, (WIDTH // 2 - game_over_text.get_width() // 2, HEIGHT // 2 - game_over_text.get_height() // 2))
            screen.blit(restart_text, (WIDTH // 2 - restart_text.get_width() // 2, HEIGHT // 2 + 60))

        # Verificar si el jugador sobrevivio a los max_enemies cuadrados azules
        if (enemies_number > max_enemies) and (enemies_number > max_enemies + 10):
            you_win_text = font.render("¡Qué bien! Sobreviviete a 5 enemigos ¡Ganaste!", True, BLUE)
        
        #Mostar el texto de "Ganaste"
        if you_win_text is not None :
            pygame.draw.rect(screen, BLACK, restart_button)
            screen.blit(you_win_text, (WIDTH // 2 - you_win_text.get_width() // 2, HEIGHT // 2 - you_win_text.get_width() // 2))
            screen.blit(restart_text, (WIDTH // 2 - restart_text.get_width() // 2, HEIGHT // 2 + 60))
        
        pygame.display.flip()
        clock.tick(60)

#reseto del juego
def reset_game():
    global player_x, player_y, enemies, enemy_spawn_time
    player_x = 30
    player_y = HEIGHT // 2 - player_size // 2
    enemies = [(WIDTH - 30, random.randint(0, HEIGHT - enemy_size))]
    enemy_spawn_time = pygame.time.get_ticks()  # Reiniciamos el tiempo

# Llamada al juego
game()

