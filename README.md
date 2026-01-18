import tkinter as tk
import random

# Game settings
GAME_WIDTH = 400
GAME_HEIGHT = 400
SPEED = 100
SPACE_SIZE = 20
BODY_PARTS = 3
SNAKE_COLOR = "#00cc00"
FOOD_COLOR = "#ff3333"
BACKGROUND_COLOR = "#f5f5f5"  # Light background

class Snake:
    def __init__(self):
        self.body_size = BODY_PARTS
        self.coordinates = []
        self.squares = []

        for i in range(0, BODY_PARTS):
            self.coordinates.append([0, 0])

        for x, y in self.coordinates:
            square = canvas.create_rectangle(x, y, x + SPACE_SIZE, y + SPACE_SIZE, fill=SNAKE_COLOR, tag="snake")
            self.squares.append(square)

class Food:
    def __init__(self):
        x = random.randint(0, (GAME_WIDTH // SPACE_SIZE) - 1) * SPACE_SIZE
        y = random.randint(0, (GAME_HEIGHT // SPACE_SIZE) - 1) * SPACE_SIZE
        self.coordinates = [x, y]
        canvas.create_oval(x, y, x + SPACE_SIZE, y + SPACE_SIZE, fill=FOOD_COLOR, tag="food")

def next_turn(snake, food):
    x, y = snake.coordinates[0]

    if direction == "up":
        y -= SPACE_SIZE
    elif direction == "down":
        y += SPACE_SIZE
    elif direction == "left":
        x -= SPACE_SIZE
    elif direction == "right":
        x += SPACE_SIZE

    snake.coordinates.insert(0, (x, y))

    square = canvas.create_rectangle(x, y, x + SPACE_SIZE, y + SPACE_SIZE, fill=SNAKE_COLOR)
    snake.squares.insert(0, square)

    if x == food.coordinates[0] and y == food.coordinates[1]:
        global score
        score += 1
        label.config(text="Score:{}".format(score))
        canvas.delete("food")
        food = Food()
    else:
        del snake.coordinates[-1]
        canvas.delete(snake.squares[-1])
        del snake.squares[-1]

    if check_collisions(snake):
        game_over()
    else:
        window.after(SPEED, next_turn, snake, food)

def change_direction(new_direction):
    global direction

    if new_direction == "left":
        if direction != "right":
            direction = new_direction
    elif new_direction == "right":
        if direction != "left":
            direction = new_direction
    elif new_direction == "up":
        if direction != "down":
            direction = new_direction
    elif new_direction == "down":
        if direction != "up":
            direction = new_direction

def check_collisions(snake):
    x, y = snake.coordinates[0]

    if x < 0 or x >= GAME_WIDTH:
        return True
    elif y < 0 or y >= GAME_HEIGHT:
        return True

    for body_part in snake.coordinates[1:]:
        if x == body_part[0] and y == body_part[1]:
            return True

    return False

def game_over():
    canvas.delete(tk.ALL)
    canvas.create_text(canvas.winfo_width() / 2, canvas.winfo_height() / 2,
                       font=('consolas', 40), text="GAME OVER", fill="red", tag="gameover")

window = tk.Tk()
window.title("🐍 Snake Game Light Mode")
window.resizable(False, False)

score = 0
direction = "down"

label = tk.Label(window, text="Score:{}".format(score), font=('consolas', 20))
label.pack()

canvas = tk.Canvas(window, bg=BACKGROUND_COLOR, height=GAME_HEIGHT, width=GAME_WIDTH)
canvas.pack()

frame = tk.Frame(window)
frame.pack(pady=10)

# Control buttons (like keypad)
btn_up = tk.Button(frame, text="↑", width=5, height=2, command=lambda: change_direction("up"))
btn_left = tk.Button(frame, text="←", width=5, height=2, command=lambda: change_direction("left"))
btn_down = tk.Button(frame, text="↓", width=5, height=2, command=lambda: change_direction("down"))
btn_right = tk.Button(frame, text="→", width=5, height=2, command=lambda: change_direction("right"))

btn_up.grid(row=0, column=1)
btn_left.grid(row=1, column=0)
btn_down.grid(row=1, column=1)
btn_right.grid(row=1, column=2)

snake = Snake()
food = Food()

next_turn(snake, food)

window.mainloop()