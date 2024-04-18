import pygame
import random

# Define some colors
BLUE = (0, 0, 255)
RED = (255, 0, 0)
YELLOW = (255, 255, 0)
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

# Define screen dimensions
WIDTH = 800
HEIGHT = 600

# Initialize Pygame
pygame.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Blue Cube vs. Red Menace")
clock = pygame.time.Clock()

# Define player and bullet classes
class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((30, 30))
        self.image.fill(BLUE)
        self.rect = self.image.get_rect()
        self.rect.center = (WIDTH // 2, HEIGHT // 2)
        self.speed = 5

    def update(self):
        keys = pygame.key.get_pressed()
        dx = 0
        dy = 0

        if keys[pygame.K_w] or keys[pygame.K_UP]:
            dy = -self.speed
        if keys[pygame.K_s] or keys[pygame.K_DOWN]:
            dy = self.speed
        if keys[pygame.K_a] or keys[pygame.K_LEFT]:
            dx = -self.speed
        if keys[pygame.K_d] or keys[pygame.K_RIGHT]:
            dx = self.speed

        self.rect.x += dx
        self.rect.y += dy

        # Keep player within screen boundaries
        self.rect.clamp_ip(screen.get_rect())

class Bullet(pygame.sprite.Sprite):
    def __init__(self, posx, posy, dirx, diry):
        super().__init__()
        self.image = pygame.Surface((10, 10))
        self.image.fill(YELLOW)
        self.rect = self.image.get_rect(center=(posx, posy))
        self.speed = 10
        self.dirx = dirx
        self.diry = diry

    def update(self):
        self.rect.x += self.dirx * self.speed
        self.rect.y += self.diry * self.speed

        # Remove bullet if it goes off screen
        if self.rect.x < 0 or self.rect.x > WIDTH or self.rect.y < 0 or self.rect.y > HEIGHT:
            self.kill()

# Create player and bullet group
player = Player()
bullet_group = pygame.sprite.Group()

# Create red enemy cubes
enemy_group = pygame.sprite.Group()
num_enemies = 10  # Adjust this to change number of enemies
for _ in range(num_enemies):
    enemy = pygame.sprite.Sprite()
    enemy.image = pygame.Surface((20, 20))
    enemy.image.fill(RED)
    enemy.rect = enemy.image.get_rect()
    enemy.rect.x = random.randrange(WIDTH - enemy.rect.width)
    enemy.rect.y = random.randrange(HEIGHT - enemy.rect.height)
    enemy_group.add(enemy)
    enemy.speed = random.uniform(1, 3)  # Random speed for each enemy

# Game variables
font = pygame.font.SysFont(None, 32)
lives = 3
game_over = False

# Main game loop
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and not game_over:
                # Get mouse position and calculate direction vector
                mouse_pos = pygame.mouse.get_pos()
                dirx = (mouse_pos[0] - player.rect.centerx) / ((mouse_pos[0] - player.rect.centerx) ** 2 + (mouse_pos

