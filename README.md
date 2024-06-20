# Hangman Game

## Python-based Hangman Game Project

### How to Play

The objective of the Hangman game is to guess the hidden word one letter at a time. You start with 6 lives. Each incorrect guess results in losing a life and drawing a part of the hangman. If you guess the word before your lives run out, you win. If you run out of lives, the game is over, and the word is revealed.

### Installation and Setup

**Clone the repository:**
```bash
git clone https://github.com/yourusername/Hangman-Game.git

Ensure you have Python installed:
You can download it from python.org.

Install the required libraries:

bash
Copy code
pip install pygame
Running the Game
Navigate to the directory where the game files are located and run the following command:

bash
Copy code
python hangman.py
Code Explanation
The Hangman game is built using Python's Tkinter for the GUI and Pygame for the sound effects. Below is a detailed explanation of the code structure.

Imports and Setup
python
Copy code
import tkinter as tk
from tkinter import messagebox, font
import random
import pygame
import os
These lines import the necessary libraries for the GUI (tkinter), random word selection (random), sound effects (pygame), and file path handling (os).

File Path Helper Function
python
Copy code
script_dir = r"C:\Users\alexo\OneDrive\Documents\GitHub\Python Hangman"

def get_path(filename):
    return os.path.join(script_dir, filename)
This function helps in constructing the correct file paths for the sound files.

HangmanGame Class
The HangmanGame class contains all the logic for the game.

Initialization
python
Copy code
class HangmanGame:
    def __init__(self, master):
        self.master = master
        self.master.title("Hangman Game")
        self.master.geometry("900x700")
        self.master.configure(bg="#2C3E50")
        
        self.words = ['python', 'programming', 'computer', 'science', 'algorithm']
        self.word = random.choice(self.words)
        self.word_letters = set(self.word)
        self.used_letters = set()
        self.lives = 6
        
        self.setup_gui()
        self.setup_sound()
        self.reset_game()
The constructor initializes the game, sets up the GUI and sound, and resets the game state.

GUI Setup
python
Copy code
def setup_gui(self):
    title_font = font.Font(family="Helvetica", size=36, weight="bold")
    title = tk.Label(self.master, text="Hangman", font=title_font, bg="#2C3E50", fg="#ECF0F1")
    title.pack(pady=20)
    
    self.canvas = tk.Canvas(self.master, width=400, height=400, bg="#34495E", highlightthickness=0)
    self.canvas.pack(pady=20)
    
    word_font = font.Font(family="Courier", size=24, weight="bold")
    self.word_display = tk.Label(self.master, text="", font=word_font, bg="#2C3E50", fg="#ECF0F1")
    self.word_display.pack(pady=20)
    
    self.letter_frame = tk.Frame(self.master, bg="#2C3E50")
    self.letter_frame.pack(pady=20)
    
    for char in 'ABCDEFGHIJKLMNOPQRSTUVWXYZ':
        btn = tk.Button(self.letter_frame, text=char, width=4, height=2,
                        command=lambda x=char: self.guess_letter(x),
                        font=("Helvetica", 12, "bold"),
                        bg="#3498DB", fg="white", activebackground="#2980B9")
        btn.pack(side=tk.LEFT, padx=2, pady=2)
    
    self.message_label = tk.Label(self.master, text="", font=("Helvetica", 18), bg="#2C3E50", fg="#ECF0F1")
    self.message_label.pack(pady=20)
The setup_gui method creates the game interface, including the title, canvas for drawing, word display, letter buttons, and message label.

Sound Setup
python
Copy code
def setup_sound(self):
    pygame.mixer.init()
    self.correct_sound = pygame.mixer.Sound(get_path("correct.wav"))
    self.wrong_sound = pygame.mixer.Sound(get_path("wrong.wav"))
    self.hanging_sound = pygame.mixer.Sound(get_path("hanging.mp3"))
    pygame.mixer.music.load(get_path("background.mp3"))
    pygame.mixer.music.set_volume(0.3)
    pygame.mixer.music.play(-1)
The setup_sound method initializes the sound effects and background music using Pygame.

Game Reset
python
Copy code
def reset_game(self):
    self.word = random.choice(self.words)
    self.word_letters = set(self.word)
    self.used_letters = set()
    self.lives = 6
    self.canvas.delete("all")
    self.draw_gallows()
    self.update_word_display()
    self.message_label.config(text="")
The reset_game method resets the game state, chooses a new word, and updates the display.

Update Word Display
python
Copy code
def update_word_display(self):
    display = ' '.join([letter if letter in self.used_letters else "_" for letter in self.word])
    self.word_display.config(text=display)
The update_word_display method updates the displayed word with guessed letters and underscores.

Drawing Functions
python
Copy code
def draw_hangman(self):
    stages = [
        self.draw_head,
        self.draw_body,
        self.draw_left_arm,
        self.draw_right_arm,
        self.draw_left_leg,
        self.draw_right_leg
    ]
    errors = 6 - self.lives
    for i in range(min(errors, len(stages))):
        stages[i]()

def draw_gallows(self):
    self.canvas.create_line(50, 350, 200, 350, width=3, fill="#ECF0F1")
    self.canvas.create_line(125, 350, 125, 50, width=3, fill="#ECF0F1")
    self.canvas.create_line(125, 50, 275, 50, width=3, fill="#ECF0F1")
    self.canvas.create_line(275, 50, 275, 100, width=3, fill="#ECF0F1")

def draw_head(self):
    self.canvas.create_oval(250, 100, 300, 150, width=3, outline="#ECF0F1")

def draw_body(self):
    self.canvas.create_line(275, 150, 275, 250, width=3, fill="#ECF0F1")

def draw_left_arm(self):
    self.canvas.create_line(275, 170, 225, 200, width=3, fill="#ECF0F1")

def draw_right_arm(self):
    self.canvas.create_line(275, 170, 325, 200, width=3, fill="#ECF0F1")

def draw_left_leg(self):
    self.canvas.create_line(275, 250, 225, 300, width=3, fill="#ECF0F1")

def draw_right_leg(self):
    self.canvas.create_line(275, 250, 325, 300, width=3, fill="#ECF0F1")
These methods handle drawing the gallows and hangman parts on the canvas.

Guessing Letters
python
Copy code
def guess_letter(self, letter):
    if self.lives <= 0:
        return

    letter = letter.lower()
    if letter in self.used_letters:
        self.message_label.config(text="You already guessed that letter!")
        return
    
    self.used_letters.add(letter)
    
    if letter in self.word_letters:
        self.word_letters.remove(letter)
        self.play_sound(self.correct_sound)
        self.message_label.config(text="Good guess!", fg="#2ECC71")
    else:
        self.lives -= 1
        self.play_sound(self.wrong_sound)
        self.message_label.config(text="Oops! Wrong guess.", fg="#E74C3C")
        self.draw_hangman()
    
    self.update_word_display()
    
    if self.lives <= 0:
        self.play_sound(self.hanging_sound)
        self.message_label.config(text="Game Over! You lost.", fg="#E74C3C")
        self.master.after(2000, lambda: messagebox.showinfo("Game Over", f"You lost! The word was {self.word}"))
        self.master.after(2000, self.reset_game)
    elif len(self.word_letters) == 0:
        self.message_label.config(text="Congratulations! You won!", fg="#2ECC71")
        self.master.after(2000, lambda: messagebox.showinfo("Congratulations", "You won!"))
        self.master.after(2000, self.reset_game)
The guess_letter method handles the logic for guessing letters, updating the game state, and playing sound effects.

Main Loop
python
Copy code
root = tk.Tk()
game = HangmanGame(root)
root.mainloop()
These lines create the main application window and start the game loop.

This README provides a comprehensive overview of the Hangman game, including instructions on how to play, install, and run the game, as well as detailed notes on the code.
