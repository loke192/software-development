import pygame
import random
import math

# Initialize Pygame
pygame.init()

# Set up the game window
screen_width = 800
screen_height = 600
screen = pygame.display.set_mode((screen_width, screen_height))
pygame.display.set_caption("Alien Spaceship Battles")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)

# Player setup
player_img = pygame.Surface((50, 40))
player_img.fill(WHITE)
player_rect = player_img.get_rect()
player_rect.center = (screen_width // 2, screen_height - 50)
player_speed = 5

# Bullet setup
bullet_img = pygame.Surface((5, 20))
bullet_img.fill(RED)
bullets = []
bullet_speed = -10

# Alien setup
alien_img = pygame.Surface((40, 40))
alien_img.fill((0, 255, 0))  # Green aliens
aliens = []
alien_speed = 2
num_aliens = 6
for i in range(num_aliens):
    alien_rect = alien_img.get_rect()
    alien_rect.x = random.randint(0, screen_width - alien_rect.width)
    alien_rect.y = random.randint(50, 150)
    aliens.append(alien_rect)

# Score
score = 0
font = pygame.font.Font(None, 36)

# Game loop
running = True
clock = pygame.time.Clock()

while running:
    # Event handling
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                bullet_rect = bullet_img.get_rect()
                bullet_rect.center = player_rect.center
                bullets.append(bullet_rect)

    # Player movement
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player_rect.left > 0:
        player_rect.x -= player_speed
    if keys[pygame.K_RIGHT] and player_rect.right < screen_width:
        player_rect.x += player_speed

    # Update bullets
    for bullet in bullets[:]:
        bullet.y += bullet_speed
        if bullet.bottom < 0:
            bullets.remove(bullet)

    # Update aliens
    for alien in aliens[:]:
        alien.y += alien_speed
        if alien.top > screen_height:
            alien.x = random.randint(0, screen_width - alien_rect.width)
            alien.y = random.randint(50, 150)

        # Collision detection (bullet hits alien)
        for bullet in bullets[:]:
            if alien.colliderect(bullet):
                aliens.remove(alien)
                bullets.remove(bullet)
                score += 1
                # Spawn new alien
                new_alien = alien_img.get_rect()
                new_alien.x = random.randint(0, screen_width - new_alien.width)
                new_alien.y = random.randint(50, 150)
                aliens.append(new_alien)
                break

        # Collision detection (alien hits player)
        if alien.colliderect(player_rect):
            running = False  # Game over

    # Draw everything
    screen.fill(BLACK)
    screen.blit(player_img, player_rect)
    for bullet in bullets:
        screen.blit(bullet_img, bullet)
    for alien in aliens:
        screen.blit(alien_img, alien)
    
    # Draw score
    score_text = font.render(f"Score: {score}", True, WHITE)
    screen.blit(score_text, (10, 10))

    # Update display
    pygame.display.flip()
    clock.tick(60)

# Game over
game_over_text = font.render("GAME OVER", True, WHITE)
screen.blit(game_over_text, (screen_width // 2 - 80, screen_height // 2))
pygame.display.flip()
pygame.time.wait(2000)  # Wait 2 seconds before closing

pygame.quit()
