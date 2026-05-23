# python-final-project
python final project coquette snake

import pygame
import random

pygame.init()

info = pygame.display.Info()
WIDTH, HEIGHT = info.current_w, info.current_h

screen = pygame.display.set_mode((0, 0), pygame.FULLSCREEN)
pygame.display.set_caption("Coquette Snake")

clock = pygame.time.Clock()

BG = (255, 240, 245)
GRID = (255, 220, 230)
SNAKE = (255, 105, 180)
HEAD = (255, 20, 147)
TEXT = (199, 21, 133)
RIBBON = (214, 0, 110)

CELL = 30

snake = [[WIDTH // 2, HEIGHT // 2]]
dx = CELL
dy = 0

def new_food():
    margin_top = 180
    x = random.randrange(0, WIDTH // CELL) * CELL
    y = random.randrange(margin_top // CELL, HEIGHT // CELL) * CELL
    return x, y

food_x, food_y = new_food()

score = 0

font = pygame.font.SysFont("Arial", 50)
title_font = pygame.font.SysFont("Arial", 35)

touch_start = None
running = True

while running:
    clock.tick(12)

    for event in pygame.event.get():

        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:

            if event.key == pygame.K_ESCAPE:
                running = False

            if event.key == pygame.K_UP and dy == 0:
                dx, dy = 0, -CELL

            if event.key == pygame.K_DOWN and dy == 0:
                dx, dy = 0, CELL

            if event.key == pygame.K_LEFT and dx == 0:
                dx, dy = -CELL, 0

            if event.key == pygame.K_RIGHT and dx == 0:
                dx, dy = CELL, 0

        if event.type == pygame.FINGERDOWN:
            touch_start = (event.x * WIDTH, event.y * HEIGHT)

        if event.type == pygame.FINGERUP and touch_start:

            end_x = event.x * WIDTH
            end_y = event.y * HEIGHT

            start_x, start_y = touch_start

            diff_x = end_x - start_x
            diff_y = end_y - start_y

            if abs(diff_x) > abs(diff_y):

                if diff_x > 0 and dx == 0:
                    dx, dy = CELL, 0

                elif diff_x < 0 and dx == 0:
                    dx, dy = -CELL, 0

            else:

                if diff_y > 0 and dy == 0:
                    dx, dy = 0, CELL

                elif diff_y < 0 and dy == 0:
                    dx, dy = 0, -CELL

    head_x = snake[0][0] + dx
    head_y = snake[0][1] + dy

    new_head = [head_x, head_y]

    snake.insert(0, new_head)

    snake_rect = pygame.Rect(head_x, head_y, CELL, CELL)
    food_rect = pygame.Rect(food_x, food_y, CELL, CELL)

    if snake_rect.colliderect(food_rect):
        score += 1
        food_x, food_y = new_food()
    else:
        snake.pop()

    if (
        head_x < 0
        or head_x >= WIDTH
        or head_y < 0
        or head_y >= HEIGHT
    ):
        running = False

    for segment in snake[1:]:
        if segment == new_head:
            running = False

    screen.fill(BG)

    for x in range(0, WIDTH, CELL):
        pygame.draw.line(screen, GRID, (x, 0), (x, HEIGHT))

    for y in range(0, HEIGHT, CELL):
        pygame.draw.line(screen, GRID, (0, y), (WIDTH, y))

    pygame.draw.circle(screen, RIBBON, (food_x + 10, food_y + 10), 10)
    pygame.draw.circle(screen, RIBBON, (food_x + 20, food_y + 10), 10)

    pygame.draw.polygon(
        screen,
        RIBBON,
        [
            (food_x + 15, food_y + 15),
            (food_x + 8, food_y + 28),
            (food_x + 15, food_y + 22)
        ]
    )

    pygame.draw.polygon(
        screen,
        RIBBON,
        [
            (food_x + 15, food_y + 15),
            (food_x + 22, food_y + 28),
            (food_x + 15, food_y + 22)
        ]
    )

    for i, segment in enumerate(snake):

        color = HEAD if i == 0 else SNAKE

        pygame.draw.rect(
            screen,
            color,
            (segment[0], segment[1], CELL, CELL),
            border_radius=10
        )

    score_text = font.render(f"Score: {score}", True, TEXT)
    screen.blit(score_text, (30, 30))

    title = title_font.render("Coquette Snake", True, TEXT)
    screen.blit(title, (30, 95))

    pygame.display.update()

screen.fill(BG)

game_over_font = pygame.font.SysFont("Arial", 80)

game_over = game_over_font.render("Game Over", True, TEXT)
final_score = font.render(f"Final Score: {score}", True, TEXT)

screen.blit(
    game_over,
    (WIDTH // 2 - game_over.get_width() // 2, HEIGHT // 2 - 100)
)

screen.blit(
    final_score,
    (WIDTH // 2 - final_score.get_width() // 2, HEIGHT // 2 + 20)
)

pygame.display.update()

pygame.time.wait(3000)

pygame.quit()

