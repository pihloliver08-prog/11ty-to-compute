import pygame
import random
import sys

pygame.init()

WIDTH, HEIGHT = 360, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Neon Dodge")

clock = pygame.time.Clock()
font = pygame.font.SysFont(None, 28)

# Colors
BLACK = (0, 0, 0)
CYAN = (0, 255, 255)
PINK = (255, 50, 120)
WHITE = (255, 255, 255)
GRAY = (80, 80, 80)

# Player
player = pygame.Rect(160, 500, 40, 40)
speed = 6

# Buttons
left_btn = pygame.Rect(20, 540, 120, 40)
right_btn = pygame.Rect(220, 540, 120, 40)

move_left = False
move_right = False

# Obstacles
obstacles = []
score = 0

while True:
    screen.fill(BLACK)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

        if event.type == pygame.MOUSEBUTTONDOWN:
            if left_btn.collidepoint(event.pos):
                move_left = True
            if right_btn.collidepoint(event.pos):
                move_right = True

        if event.type == pygame.MOUSEBUTTONUP:
            move_left = False
            move_right = False

    # Move player
    if move_left:
        player.x -= speed
    if move_right:
        player.x += speed

    player.x = max(0, min(WIDTH - player.width, player.x))

    # Spawn obstacles
    if random.randint(1, 25) == 1:
        obstacles.append(pygame.Rect(random.randint(0, WIDTH - 30), -30, 30, 30))

    for o in obstacles[:]:
        o.y += 6
        if o.y > HEIGHT:
            obstacles.remove(o)
            score += 1
        if o.colliderect(player):
            pygame.quit()
            sys.exit()

    # Draw obstacles
    for o in obstacles:
        pygame.draw.rect(screen, PINK, o)

    # Draw player
    pygame.draw.rect(screen, CYAN, player)

    # YOU label
    label = font.render("YOU", True, WHITE)
    screen.blit(label, (player.x + 5, player.y + 45))

    # Draw buttons
    pygame.draw.rect(screen, GRAY, left_btn, border_radius=10)
    pygame.draw.rect(screen, GRAY, right_btn, border_radius=10)

    screen.blit(font.render("LEFT", True, WHITE), (left_btn.x + 35, left_btn.y + 10))
    screen.blit(font.render("RIGHT", True, WHITE), (right_btn.x + 30, right_btn.y + 10))

    # Score
    screen.blit(font.render(f"Score: {score}", True, WHITE), (10, 10))

    pygame.display.flip()
    clock.tick(60)
