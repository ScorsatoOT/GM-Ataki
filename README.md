import pygame
import random

# Inicializar o Pygame
pygame.init()

# Configurações da tela
screen_width = 800
screen_height = 600
screen = pygame.display.set_mode((screen_width, screen_height))
pygame.display.set_caption("Jogo de Tiro Viciante")

# Cores
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)

# Definindo o jogador
player_width = 50
player_height = 50
player_x = screen_width // 2 - player_width // 2
player_y = screen_height - player_height - 10
player_speed = 5

# Definindo os inimigos
enemy_width = 50
enemy_height = 50
enemy_speed = 2
enemies = []

# Definindo as balas
bullet_width = 5
bullet_height = 10
bullet_speed = 7
bullets = []

# Configurações do relógio (FPS)
clock = pygame.time.Clock()
score = 0

# Função para desenhar o jogador
def draw_player(x, y):
    pygame.draw.rect(screen, WHITE, (x, y, player_width, player_height))

# Função para desenhar o inimigo
def draw_enemy(x, y):
    pygame.draw.rect(screen, RED, (x, y, enemy_width, enemy_height))

# Função para desenhar a bala
def draw_bullet(x, y):
    pygame.draw.rect(screen, WHITE, (x, y, bullet_width, bullet_height))

# Função para gerar inimigos aleatoriamente
def create_enemy():
    x = random.randint(0, screen_width - enemy_width)
    y = random.randint(-150, -50)
    enemies.append([x, y])

# Função para mover os inimigos
def move_enemies():
    global score
    for enemy in enemies[:]:
        enemy[1] += enemy_speed
        if enemy[1] > screen_height:
            enemies.remove(enemy)
            score -= 1  # Perde ponto quando o inimigo passa

# Função para mover as balas
def move_bullets():
    global score
    for bullet in bullets[:]:
        bullet[1] -= bullet_speed
        if bullet[1] < 0:
            bullets.remove(bullet)
        else:
            # Verificar colisão entre bala e inimigo
            for enemy in enemies[:]:
                if (bullet[0] > enemy[0] and bullet[0] < enemy[0] + enemy_width) and \
                   (bullet[1] > enemy[1] and bullet[1] < enemy[1] + enemy_height):
                    enemies.remove(enemy)
                    bullets.remove(bullet)
                    score += 1  # Ganha ponto quando acerta um inimigo

# Função para desenhar o placar
def draw_score():
    font = pygame.font.Font(None, 36)
    score_text = font.render(f"Score: {score}", True, WHITE)
    screen.blit(score_text, (10, 10))

# Função principal do jogo
def game_loop():
    global player_x, player_y, bullets

    # Variáveis de controle do jogo
    running = True
    while running:
        screen.fill(BLACK)

        # Checar eventos (teclado, fechar janela, etc)
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    player_x -= player_speed
                if event.key == pygame.K_RIGHT:
                    player_x += player_speed
                if event.key == pygame.K_SPACE:
                    bullets.append([player_x + player_width // 2 - bullet_width // 2, player_y])

        # Movimentação do jogador (limitar à tela)
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and player_x > 0:
            player_x -= player_speed
        if keys[pygame.K_RIGHT] and player_x < screen_width - player_width:
            player_x += player_speed

        # Criar inimigos aleatórios
        if random.random() < 0.02:  # A chance de criar um inimigo
            create_enemy()

        # Atualizar posições de inimigos e balas
        move_enemies()
        move_bullets()

        # Desenhar tudo na tela
        draw_player(player_x, player_y)
        for enemy in enemies:
            draw_enemy(enemy[0], enemy[1])
        for bullet in bullets:
            draw_bullet(bullet[0], bullet[1])

        draw_score()

        # Atualizar a tela
        pygame.display.flip()

        # Controlar o FPS
        clock.tick(60)

    pygame.quit()

# Iniciar o jogo
game_loop()
# GM-Ataki
