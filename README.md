# EXP_10-Mini_project
## AIM:
To create a complete Battleship game with an AI opponent using Monte Carlo Simulation (MCS) for decision-making in Pygame is quite involved. Below is a simplified version of the game, where the AI uses Monte Carlo Simulation to make guesses about the player’s ship locations.
## Description of game:
### Game Titel: Battleship with Top Gun
The player places their ships on a grid.
The AI tries to guess where the ships are using Monte Carlo Simulation, which means it runs several random simulations to determine the most likely spots for the ships.
The game runs using Pygame for the GUI and controls.
## Algorithm:
Step 1: Random Sampling is done inorder to run simulations using random inputs to model possible outcomes.

Step 2: Multiple Iterations is repeated many times (e.g., 1000+ iterations) to explore a wide range of scenarios.

Step 3: The results of each iteration are collected and analyzed.

Step 4: Probability Estimation will calculate the likelihood of different outcomes by comparing the frequency of results across iterations.

Step 5: Decision making is done based on the most frequent or highest-scoring outcomes, decisions or predictions are made.

Step 6: The more simulations run, the more accurate the probability estimates become.
## Program: 
```
import pygame
import random
import time

# Pygame Initialization
pygame.init()

# Constants
WIDTH, HEIGHT = 600, 400
GRID_SIZE = 10
TILE_SIZE = 40
MARGIN = 5
WHITE = (255, 255, 255)
BLUE = (0, 0, 255)
RED = (255, 0, 0)
BLACK = (0, 0, 0)
GRAY = (169, 169, 169)

# Ship Information
SHIPS = [5, 4, 3, 3, 2]  # Carrier, Battleship, Cruiser, Submarine, Destroyer

# Set up the display
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Battleship with Monte Carlo AI")

# Create the board
player_grid = [[0] * GRID_SIZE for _ in range(GRID_SIZE)]
ai_grid = [[0] * GRID_SIZE for _ in range(GRID_SIZE)]

# Tracks AI guesses
ai_guesses = [[0] * GRID_SIZE for _ in range(GRID_SIZE)]

# Function to draw the grids
def draw_grid(grid, offset_x, offset_y):
    for row in range(GRID_SIZE):
        for col in range(GRID_SIZE):
            color = WHITE
            if grid[row][col] == 1:
                color = RED  # Hit
            elif grid[row][col] == 2:
                color = BLUE  # Miss
            pygame.draw.rect(screen, color, 
                             [(MARGIN + TILE_SIZE) * col + offset_x,
                              (MARGIN + TILE_SIZE) * row + offset_y, 
                              TILE_SIZE, TILE_SIZE])
            pygame.draw.rect(screen, BLACK, 
                             [(MARGIN + TILE_SIZE) * col + offset_x,
                              (MARGIN + TILE_SIZE) * row + offset_y, 
                              TILE_SIZE, TILE_SIZE], 1)

# Place ships randomly on the board
def place_ships_randomly(grid):
    for ship in SHIPS:
        placed = False
        while not placed:
            direction = random.choice(['H', 'V'])  # Horizontal or Vertical
            if direction == 'H':
                row = random.randint(0, GRID_SIZE - 1)
                col = random.randint(0, GRID_SIZE - ship)
                if all(grid[row][c] == 0 for c in range(col, col + ship)):
                    for c in range(col, col + ship):
                        grid[row][c] = 3  # Ship marker
                    placed = True
            else:
                row = random.randint(0, GRID_SIZE - ship)
                col = random.randint(0, GRID_SIZE - 1)
                if all(grid[r][col] == 0 for r in range(row, row + ship)):
                    for r in range(row, row + ship):
                        grid[r][col] = 3  # Ship marker
                    placed = True

# Monte Carlo Simulation to guess the best move
def monte_carlo_simulation(ai_guesses, num_simulations=1000):
    score_grid = [[0] * GRID_SIZE for _ in range(GRID_SIZE)]

    for _ in range(num_simulations):
        temp_grid = [[0] * GRID_SIZE for _ in range(GRID_SIZE)]
        place_ships_randomly(temp_grid)

        for row in range(GRID_SIZE):
            for col in range(GRID_SIZE):
                if ai_guesses[row][col] == 0 and temp_grid[row][col] == 3:
                    score_grid[row][col] += 1

    # Find the max score cell
    max_score = 0
    best_move = (0, 0)
    for row in range(GRID_SIZE):
        for col in range(GRID_SIZE):
            if ai_guesses[row][col] == 0 and score_grid[row][col] > max_score:
                max_score = score_grid[row][col]
                best_move = (row, col)

    return best_move

# Check if all ships have been hit
def check_victory(grid):
    for row in grid:
        if 3 in row:  # Ship is still on the board
            return False
    return True

# Main Game Loop
def game_loop():
    place_ships_randomly(player_grid)
    place_ships_randomly(ai_grid)
    
    running = True
    player_turn = True
    game_over = False
    
    while running:
        screen.fill(GRAY)
        draw_grid(player_grid, 50, 50)
        draw_grid(ai_guesses, WIDTH // 2 + 50, 50)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

            if player_turn and not game_over:
                if event.type == pygame.MOUSEBUTTONDOWN:
                    pos = pygame.mouse.get_pos()
                    column = (pos[0] - WIDTH // 2 - 50) // (TILE_SIZE + MARGIN)
                    row = (pos[1] - 50) // (TILE_SIZE + MARGIN)
                    
                    if 0 <= row < GRID_SIZE and 0 <= column < GRID_SIZE and ai_guesses[row][column] == 0:
                        if ai_grid[row][column] == 3:
                            ai_guesses[row][column] = 1  # Hit
                        else:
                            ai_guesses[row][column] = 2  # Miss
                        
                        if check_victory(ai_grid):
                            game_over = True
                            print("Player wins!")
                        
                        player_turn = False

        if not player_turn and not game_over:
            row, col = monte_carlo_simulation(ai_guesses)
            time.sleep(1)  # Simulate AI thinking

            if player_grid[row][col] == 3:
                player_grid[row][col] = 1  # Hit
            else:
                player_grid[row][col] = 2  # Miss

            if check_victory(player_grid):
                game_over = True
                print("AI wins!")
            
            player_turn = True

        pygame.display.flip()
    
    pygame.quit()

game_loop()

```
## Output:
![{3ABC399C-9597-425B-9FC3-6BD99673CF1A}](https://github.com/user-attachments/assets/c1fc2718-be2c-4231-8676-42ec9de5e010)

## Result:
Thus we have created a small battleship game using pygame.
