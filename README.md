# Snake-Game-With-PYTHON
# SNAKE GAME WITH PYTHON

# pip install pygame
import pygame

import random

import os

pygame.mixer.init()

pygame.init()

# Colors that we will use in game.
white = (255, 255, 255)

blue = (0, 0, 255)

red = (255, 0, 0)

black = (0, 0, 0)

violet = (138, 43, 226)

lime = (0, 255, 0)

slate_blue = (123, 104, 238)

coral = (255, 127, 80)

sandy_brown = (244, 164, 96)

# Creating game window.

screen_width = 450

screen_hight = 500

gameWindow = pygame.display.set_mode((screen_width, screen_hight))

pygame.display.set_caption("snake game")

bgimg = pygame.image.load("background.jpg")

bgimg = pygame.transform.scale(

bgimg, (screen_width, screen_hight)).convert_alpha()

pygame.display.update()

clock = pygame.time.Clock()

font = pygame.font.SysFont(None, 35)

# Function to display text on screen.
def text_screen(text, color, x, y):

    screen_text = font.render(text, True, color)
    gameWindow.blit(screen_text, [x, y])

# Function to plot snake on screen.
def plot_snake(gameWindow, color, snk_list, snake_size):
    
    for x, y in snk_list:
        pygame.draw.rect(gameWindow, color, [x, y, snake_size, snake_size])


# WELCOME SCREEN
def welcome():
    
    exit_game = False
    while not exit_game:
        gameWindow.fill(violet)
        text_screen("Welcome To Snake Game", lime, 70, 180)
        text_screen("Press Spacebar To Play", lime, 80, 230)
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                exit_game = True
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:
                    quit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    gameloop()
        pygame.display.update()
        clock.tick(60)

# Main game function
def gameloop():
    
    # Game Specific Variables
    exit_game = False
    game_over = False
    snake_size = 16
    fps = 90
    snake_x = 45
    snake_y = 55
    food_x = random.randint(20, screen_width)
    food_y = random.randint(20, screen_hight)
    score = 0
    velocity_x = 0
    velocity_y = 0
    init_velocity = 2
    snk_list = []
    snk_length = 1

    # Check if highest score file exist.
    if (not os.path.exists("highest_score.txt")):
        with open("highest_score.txt", "w") as f:
            f.write("0")

    with open("highest_score.txt", "r") as f:
        highest_score = f.read()

    while not exit_game:
        if game_over:
            with open("highest_score.txt", "w") as f:
                f.write(str(highest_score))
            gameWindow.fill(slate_blue)
            text_screen("Game Over! Press Enter To Restart", coral, 15, 200)

            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    exit_game = True

                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_RETURN:
                        welcome()
        else:
            # Exit event
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    exit_game = True

            # Movement event
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_RIGHT:
                        velocity_x = init_velocity
                        velocity_y = 0

                    if event.key == pygame.K_LEFT:
                        velocity_x = -init_velocity
                        velocity_y = 0

                    if event.key == pygame.K_UP:
                        velocity_y = -init_velocity
                        velocity_x = 0

                    if event.key == pygame.K_DOWN:
                        velocity_y = init_velocity
                        velocity_x = 0

                    if event.key == pygame.K_r: #To reset highest score
                        highest_score = 0

                    if event.key == pygame.K_ESCAPE:
                        quit()

            # Snake velocity
            snake_x = snake_x + velocity_x
            snake_y = snake_y + velocity_y

            # Adding score, Replotting food and Highest score.
            if abs(snake_x-food_x) < 12 and abs(snake_y-food_y) < 12:
 
                score += 10
                food_x = random.randint(20, screen_width)
                food_y = random.randint(20, screen_hight)
                snk_length += 5
                if score > int(highest_score):
 
                    highest_score = score

            # Window color
            gameWindow.fill(white)
            gameWindow.blit(bgimg, (0, 0))

            # Score display
            text_screen("Score: " + str(score) +
                        "   Highest Score :" + str(highest_score), blue, 5, 5)

            # Snake food
            pygame.draw.rect(gameWindow, red, [
                food_x, food_y, snake_size, snake_size])

            # Snake length increase
            head = []
            head.append(snake_x)
            head.append(snake_y)
            snk_list.append(head)

            if len(snk_list) > snk_length:
                del snk_list[0]

            if head in snk_list[:-1]:
                game_over = True
                pygame.mixer.music.load('game over.mp3')
                pygame.mixer.music.play()

            if snake_x < 0 or snake_x > screen_width or snake_y < 0 or snake_y > screen_hight:
                game_over = True
                pygame.mixer.music.load('game over.mp3')
                pygame.mixer.music.play()

            # Snake head
            plot_snake(gameWindow, black, snk_list, snake_size)
        pygame.display.update()
        clock.tick(fps)

    # To quit 
    pygame.quit()
    quit()
    
welcome()

gameloop()
