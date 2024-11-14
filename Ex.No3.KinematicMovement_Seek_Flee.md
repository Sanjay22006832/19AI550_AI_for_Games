# Ex.No: 3  Implementation of Kinematic movement seek and flee behaviors 
### DATE: 16-08-2024                                                                           
### REGISTER NUMBER : 212222240090
### AIM: 
To write a python program to simulate the process of seek and flee behaviors using mouse movements.
### Algorithm:
1. Import the necessary modules pygame, math, random when necessary.
2. Initiate the pygame engine
3. Create a window with size (400,300)
4. Create a function to simulate the seek behavior - to move towards the target 
5. Create a function to simulate the flee behavior - to move away from the target 
6. Update the position of object
7. Create a game loop to display the update behavior
8. Call the seek function when left mouse button is pressed
9. Call the flee function when right mouse button is pressed
10. Close the pygame window when quit icon is clicked.
11. Stop the program
    
### Program:

```python
import pygame
import math
import sys
pygame.init()
WIDTH, HEIGHT = 800, 600
window = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Kinematic Movement Example")
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
RED = (255, 0, 0)
CHAR_SIZE = 20
MAX_SPEED = 5
class Character:
    def __init__(self, x, y, color):
        self.position = pygame.Vector2(x, y)
        self.velocity = pygame.Vector2(0, 0)
        self.color = color
    def seek(self, target):
        desired_velocity = (target - self.position).normalize() * MAX_SPEED
        self.velocity = desired_velocity
    def flee(self, target):
        desired_velocity = (self.position - target).normalize() * MAX_SPEED
        self.velocity = desired_velocity
    def update(self):
        self.position += self.velocity
    def draw(self, surface):
        pygame.draw.circle(surface, self.color, (int(self.position.x), int(self.position.y)), CHAR_SIZE)

def main():
    clock = pygame.time.Clock()
    player = Character(WIDTH // 2, HEIGHT // 2, WHITE)
    target = Character(WIDTH // 4, HEIGHT // 4, RED)
    running = True
    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
        mouse_pos = pygame.Vector2(pygame.mouse.get_pos())
        if pygame.mouse.get_pressed()[0]:  # Left button - Seek
            player.seek(mouse_pos)
        elif pygame.mouse.get_pressed()[2]:  # Right button - Flee
            player.flee(mouse_pos)
        else:
            player.velocity = pygame.Vector2(0, 0)  # Stop if no button is pressed
        player.update()
        window.fill(BLACK)
        player.draw(window)
        target.draw(window)
        pygame.display.flip()
        clock.tick(60)
    pygame.quit()
    sys.exit()
if __name__ == "__main__":
    main()
```

### Output:
<img src="https://github.com/user-attachments/assets/b27666d7-8ed1-45ea-ad66-013054733050" width=30%>

### Result:
Thus the simple seek and flee behavior was implemented successfully.
