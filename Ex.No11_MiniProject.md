# Ex.No: 11  Mini Project 
### DATE: 18/10/24                                                                            
### REGISTER NUMBER : 212222240090
### AIM: 
To write a python program to simulate the game using AI Techniques
### Algorithm:
1. Initialize: Set up the game window, load images, and initialize player, enemies, and bullets.
2. Player Input: Move the player and fire bullets based on keyboard input.
3. Enemy Behavior: Spawn and move enemies toward the player, adding basic AI behavior.
4. Collision Detection: Check for collisions between bullets and enemies, updating the score.
5. Game Loop: Continuously update and draw all elements, handling game over and restarting.
### Program:
import pygame
import random

# Initialize Pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Space Shooter")

# Load images
background = pygame.image.load('galaxy2.jpg')
player_image = pygame.image.load('character.png')
bullet_image = pygame.image.load('bullet2.png')
enemy_image = pygame.image.load('monster.png')
enemy_bullet_image = pygame.image.load('bomb.png')

# Colors
WHITE = (255, 255, 255)

# Clock
clock = pygame.time.Clock()

# Player class
class Player:
    def __init__(self):
        self.image = player_image
        self.rect = self.image.get_rect(center=(WIDTH // 2, HEIGHT - 50))

    def move(self, dx):
        self.rect.x += dx
        # Keep player on screen
        if self.rect.left < 0:
            self.rect.left = 0
        if self.rect.right > WIDTH:
            self.rect.right = WIDTH

# Bullet class
class Bullet:
    def __init__(self, x, y, speed):
        self.image = bullet_image
        self.rect = self.image.get_rect(center=(x, y))
        self.speed = speed

    def update(self):
        self.rect.y -= self.speed
        if self.rect.bottom < 0:
            return False  # Bullet goes off screen
        return True

# Enemy class with basic pathfinding-like logic and state machine
class Enemy:
    def __init__(self):
        self.image = enemy_image
        self.rect = self.image.get_rect(center=(random.randint(50, WIDTH - 50), 50))
        self.speed = 2
        self.cooldown = random.randint(40, 120)  # Randomize shooting cooldown
        self.shoot_delay = 0
        self.state = "Chase"  # Initial state

    def move(self, player_rect, bullets):
        if self.state == "Chase":
            # Move toward player
            if self.rect.centerx < player_rect.centerx:
                self.rect.x += self.speed
            elif self.rect.centerx > player_rect.centerx:
                self.rect.x -= self.speed
            
            # Check for nearby bullets to switch to "Flee"
            for bullet in bullets:
                if self.rect.colliderect(bullet.rect.inflate(20, 20)):  # Check for near bullets
                    self.state = "Flee"
                    break
        elif self.state == "Flee":
            # Move away from player
            if self.rect.centerx < player_rect.centerx:
                self.rect.x -= self.speed
            else:
                self.rect.x += self.speed
            
            # Return to chase state if not near bullets
            if all(not self.rect.colliderect(bullet.rect.inflate(20, 20)) for bullet in bullets):
                self.state = "Chase"

    def shoot(self):
        if self.shoot_delay <= 0:
            self.shoot_delay = random.randint(40, 80)  # Reduced cooldown for shooting
            return EnemyBullet(self.rect.centerx, self.rect.bottom, 5)  # Create a new bullet
        else:
            self.shoot_delay -= 1  # Decrease cooldown

# Enemy bullet class
class EnemyBullet:
    def __init__(self, x, y, speed):
        self.image = enemy_bullet_image
        self.rect = self.image.get_rect(center=(x, y))
        self.speed = speed

    def update(self):
        self.rect.y += self.speed
        if self.rect.top > HEIGHT:
            return False  # Bullet goes off screen
        return True

def game_over(score):
    # Display final score and restart option
    font = pygame.font.Font(None, 74)
    text = font.render("Game Over", True, WHITE)
    score_text = font.render(f"Final Score: {score}", True, WHITE)
    restart_text = font.render("Press R to Restart", True, WHITE)

    screen.blit(background, (0, 0))  # Draw background
    screen.blit(text, (WIDTH // 2 - text.get_width() // 2, HEIGHT // 2 - 50))
    screen.blit(score_text, (WIDTH // 2 - score_text.get_width() // 2, HEIGHT // 2))
    screen.blit(restart_text, (WIDTH // 2 - restart_text.get_width() // 2, HEIGHT // 2 + 50))
    pygame.display.flip()

    # Wait for restart or quit
    waiting = True
    while waiting:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_r:
                    waiting = False  # Restart the game

# Game loop
def main():
    running = True
    player = Player()
    bullets = []
    enemies = []  # Infinite enemies list
    enemy_bullets = []
    score = 0
    spawn_timer = 0  # Timer for spawning enemies

    while running:
        spawn_timer += 1  # Increment spawn timer

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

        keys = pygame.key.get_pressed()
        dx = 0
        if keys[pygame.K_LEFT]:
            dx = -5
        if keys[pygame.K_RIGHT]:
            dx = 5
        player.move(dx)

        # Shooting bullets
        if keys[pygame.K_SPACE] and len(bullets) < 4:  # Limit the number of bullets to 4
            bullets.append(Bullet(player.rect.centerx, player.rect.top, 10))

        # Spawn enemies periodically
        if spawn_timer > 60:  # Spawn an enemy every 60 frames
            enemies.append(Enemy())
            spawn_timer = 0  # Reset the spawn timer

        # Update bullets
        bullets = [bullet for bullet in bullets if bullet.update()]

        # Move enemies with pathfinding-like behavior and manage shooting
        for enemy in enemies:
            enemy.move(player.rect, bullets)
            enemy_bullet = enemy.shoot()
            if enemy_bullet:
                enemy_bullets.append(enemy_bullet)

        # Update enemy bullets
        enemy_bullets = [bullet for bullet in enemy_bullets if bullet.update()]

        # Check for collisions
        for bullet in bullets:
            for enemy in enemies[:]:
                if bullet.rect.colliderect(enemy.rect):
                    enemies.remove(enemy)
                    bullets.remove(bullet)
                    score += 1
                    print(f"Score: {score}")
                    break

        # Check for player hit by enemy bullets
        for enemy_bullet in enemy_bullets:
            if enemy_bullet.rect.colliderect(player.rect):
                game_over(score)  # Game over on player hit
                # Reset game variables
                player = Player()
                bullets.clear()
                enemies.clear()  # Reset enemies
                enemy_bullets.clear()
                score = 0

        # Draw everything
        screen.blit(background, (0, 0))
        screen.blit(player.image, player.rect)
        # Display current score
        font = pygame.font.Font(None, 36)
        score_text = font.render(f"Score: {score}", True, WHITE)
        screen.blit(score_text, (10, 10))  # Draw score in the top left corner
        for bullet in bullets:
            screen.blit(bullet.image, bullet.rect)
        for enemy in enemies:
            screen.blit(enemy.image, enemy.rect)
        for enemy_bullet in enemy_bullets:
            screen.blit(enemy_bullet.image, enemy_bullet.rect)

        pygame.display.flip()
        clock.tick(60)

    pygame.quit()

if __name__ == "__main__":
    main()
### Output:

![Screenshot 2024-10-18 142028](https://github.com/user-attachments/assets/f91ae8f7-15da-4000-b62a-a8ac0ac9e642)


### Result:
Thus the simple  game was implemented using AI Techniques
