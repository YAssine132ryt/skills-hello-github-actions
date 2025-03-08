import pygame
import random

# Inizializza Pygame
pygame.init()

# Impostazioni schermo
LARGHEZZA, ALTEZZA = 800, 600
schermo = pygame.display.set_mode((LARGHEZZA, ALTEZZA))
pygame.display.set_caption("Mini Battle Royale")

# Colori
BIANCO = (255, 255, 255)
NERO = (0, 0, 0)
ROSSO = (255, 0, 0)
VERDE = (0, 255, 0)

# Caratteristiche del giocatore
player_size = (40, 60)
player_x = 100
player_y = ALTEZZA - player_size[1] - 50
velocità = 5
gravità = 0.5
salto = -10
vel_y = 0

# Lista proiettili
proiettili = []

# Nemico
nemico_size = (40, 60)
nemico_x = random.randint(400, 700)
nemico_y = ALTEZZA - nemico_size[1] - 50
nemico_vx = random.choice([-2, 2])

# Piattaforme
piattaforme = [(0, ALTEZZA - 40, LARGHEZZA, 40), (300, 450, 200, 10), (500, 350, 150, 10)]

# Clock per il framerate
clock = pygame.time.Clock()

# Gioco in esecuzione
running = True
a_terra = False

while running:
    clock.tick(60)
    schermo.fill(BIANCO)

    # Eventi
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
    
    # Controlli giocatore
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player_x -= velocità
    if keys[pygame.K_RIGHT]:
        player_x += velocità
    if keys[pygame.K_SPACE] and a_terra:
        vel_y = salto

    # Gravità
    vel_y += gravità
    player_y += vel_y

    # Collisione con piattaforme
    a_terra = False
    for piattaforma in piattaforme:
        px, py, pw, ph = piattaforma
        if player_y + player_size[1] >= py and player_y + player_size[1] <= py + 10 and player_x + player_size[0] > px and player_x < px + pw:
            player_y = py - player_size[1]
            vel_y = 0
            a_terra = True

    # Sparare proiettili
    if keys[pygame.K_f]:  
        proiettili.append([player_x + player_size[0] // 2, player_y + player_size[1] // 2])

    # Movimento proiettili
    for p in proiettili:
        p[0] += 7

    # Rimuovere proiettili fuori dallo schermo
    
    proiettili = [p for p in proiettili if p[0] < LARGHEZZA]

    # Movimento nemico
    nemico_x += nemico_vx
    if nemico_x < 0 or nemico_x > LARGHEZZA - nemico_size[0]:
        nemico_vx = -nemico_vx

    # Disegna piattaforme
    for piattaforma in piattaforme:
        pygame.draw.rect(schermo, VERDE, piattaforma)

    # Disegna giocatore, nemico e proiettili
    pygame.draw.rect(schermo, NERO, (player_x, player_y, *player_size))
    pygame.draw.rect(schermo, ROSSO, (nemico_x, nemico_y, *nemico_size))
    for p in proiettili:
        pygame.draw.circle(schermo, ROSSO, (p[0], p[1]), 5)

    # Aggiorna lo schermo
    pygame.display.flip()

pygame.quit()
