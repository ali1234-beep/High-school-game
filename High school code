#FIRST THING TO DO IS INSTALL EVERYTHING, "pip install pygame, pip install colorama" - DO THIS IN THE CONSOLE PLEASE!!!!!!!!!
########## - READ THE FIRST LINE
####### - READ THE FIRST LINE
######## - READ THE FIRST LINE

import time 
import sys
import random
import os
from colorama import Fore, Style, init
import tkinter as tk
import pygame
import select  
from enum import Enum

# Initialize colorama for colored text
init(autoreset=True)

# Fix the Difficulty class definition and initialization
class Difficulty:
    def __init__(self, iq_gain, iq_loss, starting_iq, questions):
        self.iq_gain = iq_gain
        self.iq_loss = iq_loss
        self.starting_iq = starting_iq
        self.questions = questions

# Create difficulty levels as class attributes
Difficulty.EASY = Difficulty(15, 3, 120, "easy")
Difficulty.NORMAL = Difficulty(10, 5, 100, "normal")
Difficulty.HARD = Difficulty(5, 10, 80, "hard")

# Fix the completed_paths dictionary in GameState class
class GameState:
    def __init__(self):
        global inventory
        self.inventory = inventory
        self.iq = 100  # Starting IQ
        self.completed_paths = {
            "darlingtonpark": {  # Changed from "darlington" to match location key
                "completed": False,
                "areas": {
                    "hiking_trail": False,
                    "waterfront": False,
                    "visitor_center": False,
                    "campground": False
                }
            },
            "foodland": {
                "completed": False,
                "areas": {
                    "produce_section": False,
                    "bakery": False,
                    "deli_counter": False,
                    "freezer_section": False
                }
            },
            "timhortons": {
                "completed": False,
                "areas": {
                    "front_counter": False,
                    "dining_area": False,
                    "drive_thru": False,
                    "back_room": False
                }
            },
            "community": {
                "completed": False,
                "areas": {
                    "pool_area": False,
                    "gym": False,
                    "courts": False,
                    "locker_room": False
                }
            }
        }
        self.current_thief = None
        self.difficulty = Difficulty.NORMAL
        self.high_scores = self.load_high_scores()
        self.achievements = set()
        self.player_name = ""
        
    def load_high_scores(self):
        try:
            with open("highscores.txt", "r") as f:
                return [line.strip().split(",") for line in f.readlines()]
        except (FileNotFoundError, IOError, ValueError):
            return []
            
    def save_high_score(self, name, score):
        try:
            self.high_scores.append([name, str(score)])
            self.high_scores.sort(key=lambda x: int(x[1]), reverse=True)
            self.high_scores = self.high_scores[:10]
            with open("highscores.txt", "w") as f:
                for name, score in self.high_scores:
                    f.write(f"{name},{score}\n")
        except Exception as e:
            type_writter(f"Error saving high score: {e}", color=Fore.RED)

# Modify SettingsWindow class
class SettingsWindow:
    def __init__(self, game_state):
        self.game_state = game_state
        self.root = tk.Tk()
        self.root.title("Game Settings")
        
        # Inventory display
        self.inv_label = tk.Label(self.root, text="Inventory:")
        self.inv_label.pack()
        self.inv_listbox = tk.Listbox(self.root, width=30, height=10)  # Make listbox bigger
        self.inv_listbox.pack(padx=10, pady=5)  # Add padding
        
        # Update inventory immediately
        self.update_inventory()
        
    def update_inventory(self):
        """Update inventory list periodically using Tkinter's after method"""
        try:
            self.inv_listbox.delete(0, tk.END)
            # Use global inventory instead of game_state.inventory
            for item in inventory:  # Use the global inventory variable
                self.inv_listbox.insert(tk.END, item)
        except tk.TclError:
            return  # Window was closed
            
        # Schedule next update
        self.root.after(1000, self.update_inventory)

# Inventory system and special items
inventory = []
special_items = {
    "Map Piece": {"desc": "A fragment of the thief's hideout map", "use": "Reveals secret paths when combined"},
    "Coffee Beans": {"desc": "Might help wake someone up", "use": "Can be traded for information or wake up sleeping NPCs"},
    "Flashlight": {"desc": "Useful in dark areas", "use": "Allows exploration of dark areas without IQ loss"},
    "Master Key": {"desc": "Opens locked doors", "use": "Bypasses certain challenges"},
    "Energy Drink": {"desc": "Restores 20 IQ points", "use": "One-time IQ boost"},
    "Compass": {"desc": "Helps with navigation", "use": "Prevents getting lost penalties"},
    "Wildlife Guide": {"desc": "Contains animal information", "use": "Helps with nature questions"},
    "Lucky Lure": {"desc": "Brings good fortune", "use": "Extra chance on difficult questions"},
    "Maple Seeds": {"desc": "Native plant specimen", "use": "Trade for valuable information"},
    "Whistle": {"desc": "Makes a loud noise", "use": "Can call for help or distract enemies"},
    "Sandwich": {"desc": "Fresh and delicious", "use": "Restore 10 IQ points"},
    "Espresso": {"desc": "Strong coffee", "use": "Temporary IQ boost for one question"},
    "Discount Card": {"desc": "Store savings card", "use": "Get better trades with merchants"}
}

# Characters
characters = {
    "Principal": "Your school principal, looking worried",
    "Mysterious Stranger": "Someone offering questionable help",
    "Janitor": "Knows all the school's secrets",
    "Librarian": "Surprisingly tough trivia master" 
}

# Function to display inventory
def show_inventory():
    if inventory:
        type_writter("\nYour Inventory:", speed=0.02, color=Fore.CYAN)
        for item in inventory:
            type_writter(f"- {item}", speed=0.02, color=Fore.CYAN)
        
        type_writter("\nUse an item? (Y/N)", speed=0.02)
        if input(Fore.YELLOW + "> ").lower() == 'y':
            type_writter("\nWhich item? (Type the name or 'cancel')", speed=0.02)
            item = input(Fore.YELLOW + "> ").lower()
            if item == 'cancel':
                return False, None
            # Match item regardless of case
            for inv_item in inventory:
                if inv_item.lower() == item:
                    return True, inv_item
            type_writter("Item not found!", color=Fore.RED)
    else:
        type_writter("Your inventory is empty.", speed=0.02, color=Fore.CYAN)
    return False, None

# Timer-based mechanic
def countdown(seconds, message=""):
    type_writter(message, speed=0.02, color=Fore.YELLOW)
    for i in range(seconds, 0, -1):
        print(Fore.RED + f"{i}...", end=" ", flush=True)
        time.sleep(1)
    print("\n")

# Function to simulate typewriter effect
def type_writter(text, speed=0.05, color=Fore.WHITE):
    for char in text:
        sys.stdout.write(color + char)
        sys.stdout.flush()
        time.sleep(speed)
    print(Style.RESET_ALL)  # Reset after printing

def pause_line():
    print("\n")
    input(Fore.YELLOW + "Press Enter to continue...")

def clear_system():
    os.system("cls" if os.name == "nt" else "clear")

def space():
    print("\n")

# Humorous fail messages
def get_fail_message():
    fail_messages = [
        "Nope. Not even close.",
        "Wow, that was... bad.",
        "Yikes. Try again.",
        "Oof. Wrong answer.",
        "Nah, that's not it.",
        "Seriously? That's your answer?",
        "Bzzzt! Wrong.",
        "Nope. Not your day, is it?",
        "Wrong. Like, really wrong.",
        "Nah. Maybe next time."
    ]
    return random.choice(fail_messages)

# Pass message
pass_message = "Correct! Your IQ has increased."

# Adjust IQ based on the answer
def adjust_iq(correct, iq, game_state):
    """Adjust IQ based on difficulty and correctness"""
    if correct:
        iq += game_state.difficulty.iq_gain
    else:
        iq -= game_state.difficulty.iq_loss
    return iq

# Expanded question banks
geography_questions = [
    ("What is the capital of France?", "Paris"),
    ("What is the capital of Japan?", "Tokyo"),
    ("What is the largest country by area?", "Russia"),
    ("What is the capital of Australia?", "Canberra"),
    ("Which continent is Egypt in?", "Africa"),
    ("What is the longest river in the world?", "Nile"),
    ("What ocean is between North America and Europe?", "Atlantic"),
    ("What is the smallest country in the world?", "Vatican City"),
    ("What is the capital of Brazil?", "Brasilia"),
    ("What mountain range runs through South America?", "Andes")
]

math_questions = [
    ("What is 15 + 27?", "42"),
    ("What is 144 ÷ 12?", "12"),
    ("What is 13 × 13?", "169"),
    ("What is the square root of 144?", "12"),
    ("What is 500 - 127?", "373"),
    ("What is 7 × 8?", "56"),
    ("What is 1000 ÷ 25?", "40"),
    ("What is 17 + 38?", "55"),
    ("What is 256 ÷ 16?", "16"),
    ("What is 11 × 12?", "132")
]

science_questions = [
    ("What is H2O?", "water"),
    ("What planet is closest to the sun?", "mercury"),
    ("What is the hardest natural substance?", "diamond"),
    ("What is the largest planet?", "jupiter"),
    ("What is the chemical symbol for gold?", "au"),
    ("What gas do plants absorb?", "carbon dioxide"),
    ("What is the speed of light?", "299792458"),
    ("What is the closest star to Earth?", "sun"),
    ("What is the study of fossils called?", "paleontology"),
    ("What is the most abundant gas in Earth's atmosphere?", "nitrogen")
]

history_questions = [
    ("Who was the first president of the United States?", "george washington"),
    ("In what year did World War II end?", "1945"),
    ("Who painted the Mona Lisa?", "leonardo da vinci"),
    ("Who was the first person to walk on the moon?", "neil armstrong"),
    ("What year did the Titanic sink?", "1912"),
    ("Who was the first female Prime Minister of the UK?", "margaret thatcher"),
    ("In what year did World War I begin?", "1914"),
    ("Who wrote Romeo and Juliet?", "william shakespeare"),
    ("What ancient wonder still stands in Egypt?", "pyramids"),
    ("Who was the first Emperor of China?", "qin shi huang")
]

riddles_questions = [
    ("I speak without a mouth and hear without ears. What am I?", "echo"),
    ("What has keys but no locks?", "piano"),
    ("What has cities but no houses?", "map"),
    ("What goes up but never comes down?", "age"),
    ("What has hands but cannot clap?", "clock"),
    ("What gets wetter as it dries?", "towel"),
    ("What has a face and two hands but no arms or legs?", "clock"),
    ("What is always in front of you but can't be seen?", "future"),
    ("What gets bigger when you take away from it?", "hole"),
    ("What has teeth but cannot bite?", "comb")
]

tim_questions = [
    ("What is the most popular donut at Tim Hortons?", "honey cruller"),
    ("What year was Tim Hortons founded?", "1964"),
    ("What is the name of Tim Hortons' loyalty program?", "rewards"),
    ("What is the signature coffee blend of Tim Hortons?", "original blend"),
    ("What is the name of Tim Hortons' breakfast sandwich?", "breakfast sandwich"),
    ("What is the name of Tim Hortons' iced coffee?", "iced capp"),
    ("What is the name of Tim Hortons' donut holes?", "timbits"),
    ("What is the name of Tim Hortons' hot chocolate?", "hot chocolate"),
    ("What is the name of Tim Hortons' tea?", "steeped tea"),
    ("What is the name of Tim Hortons' soup?", "chicken noodle")
]

sports_questions = [
    ("What sport is known as the 'king of sports'?", "soccer"),
    ("What sport is played at Wimbledon?", "tennis"),
    ("What sport uses a puck?", "hockey"),
    ("What sport is known as 'America's pastime'?", "baseball"),
    ("What sport is played with a round ball and a hoop?", "basketball"),
    ("What sport is played on a diamond?", "baseball"),
    ("What sport is played with a shuttlecock?", "badminton"),
    ("What sport is played on a pitch?", "cricket"),
    ("What sport is played with a bat and a ball?", "cricket"),
    ("What sport is played with a racket and a ball?", "tennis")
]

# Add these new questions to the existing question banks

# Add to tim_questions
tim_questions.extend([
    ("What is Tim Hortons' most popular coffee size?", "medium"),
    ("What are Tim Hortons' potato wedges called?", "wedges"),
    ("What color is Tim Hortons' logo?", "red"),
    ("What year did Tim Hortons merge with Burger King?", "2014"),
    ("What is Tim Hortons' rewards program called?", "tims rewards"),
    ("What country was Tim Horton born in?", "canada"),
    ("What sport did Tim Horton play?", "hockey"),
    ("What type of pastry is a 'Double Double'?", "coffee"),
    ("What is Tim Hortons' frozen coffee drink called?", "iced capp"),
    ("What was Tim Hortons' original donut price?", "69 cents")
])

# Add to sports_questions
sports_questions.extend([
    ("What is the diameter of a basketball hoop?", "18 inches"),
    ("How many players are on a soccer team?", "11"),
    ("What country invented basketball?", "canada"),
    ("How many periods are in a hockey game?", "3"),
    ("What is the length of an Olympic swimming pool?", "50 meters"),
    ("How many points is a touchdown worth?", "6"),
    ("What color is a tennis ball?", "yellow"),
    ("How many innings in a baseball game?", "9"),
    ("What sport uses the term 'love'?", "tennis"),
    ("What sport is played at Madison Square Garden?", "basketball")
])

# Example modification for fight() function
def fight(enemy_name, iq, game_state):
    player_health = 100
    enemy_health = 100

    type_writter(f"You engage in a fight with {enemy_name}!", speed=0.02, color=Fore.RED)
    pause_line()

    while player_health > 0 and enemy_health > 0:
        # Display health bars
        type_writter(f"Your Health: {player_health} HP", speed=0.02, color=Fore.GREEN)
        type_writter(f"{enemy_name}'s Health: {enemy_health} HP", speed=0.02, color=Fore.RED)
        space()

        # Get random question from any category
        all_questions = geography_questions + math_questions + science_questions + history_questions + riddles_questions
        question, answer = get_question_for_difficulty(all_questions, game_state.difficulty)
        type_writter(question, speed=0.02, color=Fore.CYAN)
        space()

        # Get player's answer
        player_answer = input(Fore.YELLOW + "Your answer: ").strip()

        # Check answer
        if player_answer.lower() == answer.lower():
            type_writter("Correct! You deal 20 damage to the enemy.", speed=0.02, color=Fore.GREEN)
            enemy_health -= 20
        else:
            type_writter(get_fail_message(), speed=0.02, color=Fore.RED)
            type_writter(f"The correct answer was: {answer}", speed=0.02, color=Fore.RED)
            type_writter("You take 20 damage.", speed=0.02, color=Fore.RED)
            player_health -= 20

        pause_line()
        clear_system()

    # Determine fight outcome
    if player_health <= 0:
        type_writter("💀 You have been defeated. The thief escapes with your diploma.", speed=0.02, color=Fore.RED)
        return False, iq
    else:
        type_writter(f"🌟 You defeated {enemy_name} and recover a clue!", speed=0.02, color=Fore.GREEN)
        return True, iq

# Intro with ASCII art
def intro(name, game_state):
    # Add skip option
    type_writter("Press ENTER to watch intro or type 'skip' to begin:", speed=0.02)
    if input(Fore.YELLOW + "> ").lower() != 'skip':
        type_writter(r"""
                     
░▒▓███████▓▒░░▒▓█▓▒░▒▓███████▓▒░░▒▓█▓▒░      ░▒▓██████▓▒░░▒▓██████████████▓▒░ ░▒▓██████▓▒░       ░▒▓█▓▒░░▒▓█▓▒░▒▓████████▓▒░▒▓█▓▒░░▒▓███████▓▒░▒▓████████▓▒░ 
░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░     ░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░░▒▓█▓▒░      ░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░      ░▒▓█▓▒░▒▓█▓▒░         ░▒▓█▓▒░     
░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░     ░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░░▒▓█▓▒░      ░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░      ░▒▓█▓▒░▒▓█▓▒░         ░▒▓█▓▒░     
░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░▒▓███████▓▒░░▒▓█▓▒░     ░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░░▒▓█▓▒░░▒▓█▓▒░▒▓████████▓▒░      ░▒▓████████▓▒░▒▓██████▓▒░ ░▒▓█▓▒░░▒▓██████▓▒░   ░▒▓█▓▒░     
░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░▒▓█▓▒░      ░▒▓█▓▒░     ░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░░▒▓█▓▒░      ░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░      ░▒▓█▓▒░      ░▒▓█▓▒░  ░▒▓█▓▒░     
░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░▒▓█▓▒░      ░▒▓█▓▒░     ░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░░▒▓█▓▒░      ░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░      ░▒▓█▓▒░      ░▒▓█▓▒░  ░▒▓█▓▒░     
░▒▓███████▓▒░░▒▓█▓▒░▒▓█▓▒░      ░▒▓████████▓▒░▒▓██████▓▒░░▒▓█▓▒░░▒▓█▓▒░░▒▓█▓▒░▒▓█▓▒░░▒▓█▓▒░      ░▒▓█▓▒░░▒▓█▓▒░▒▓████████▓▒░▒▓█▓▒░▒▓███████▓▒░   ░▒▓█▓▒░     
    """, speed=0.01)
        space()
        type_writter(f"Welcome, {name}, to the Courtice Diploma Heist!", speed=0.02)
        type_writter("It's graduation day at Courtice Secondary School, but your diploma has been stolen!", speed=0.02)
        type_writter("You must track down the thief and recover your diploma before the ceremony ends.", speed=0.02)
        pause_line()
    
    # Add difficulty selection
    type_writter("\nSelect Difficulty:", speed=0.02)
    type_writter("1. Easy (More IQ gain, less loss)", speed=0.02, color=Fore.GREEN)
    type_writter("2. Normal (Standard gameplay)", speed=0.02, color=Fore.YELLOW)
    type_writter("3. Hard (Less IQ gain, more loss)", speed=0.02, color=Fore.RED)
    
    while True:
        choice = input(Fore.YELLOW + "Choose difficulty (1-3): ")
        if choice == '1':
            game_state.difficulty = Difficulty.EASY
            break
        elif choice == '2':
            game_state.difficulty = Difficulty.NORMAL
            break
        elif choice == '3':
            game_state.difficulty = Difficulty.HARD
            break
        else:
            type_writter("Invalid choice!", color=Fore.RED)
    
    clear_system()
    start_adventure(game_state)

# Add at the top with other global variables
completed_paths = {
    "darlingtonpark": False,
    "foodland": False,
    "timhortons": False,
    "community": False
}

# Add thief types with their stories
thieves = {
    "Jordan": {
        "name": "Jordan the Academic",
        "motive": "Jealous of your grades and wants to be valedictorian",
        "location": "School Library",
        "weakness": "Coffee",
        "strength": "Math questions",
        "final_item": "Academic Records"
    },
    "Alex": {
        "name": "Alex the Athlete",
        "motive": "Wants revenge for losing team captain position",
        "location": "School Gym",
        "weakness": "Energy Drink",
        "strength": "Sports questions",
        "final_item": "Team Trophy"
    },
    "Morgan": {
        "name": "Morgan the Hacker",
        "motive": "Planning to change everyone's grades",
        "location": "Computer Lab",
        "weakness": "Master Key",
        "strength": "Science questions",
        "final_item": "USB Drive"
    }
}

# Add to global variables
current_thief = None

# Add difficulty-based question selection
def get_question_for_difficulty(question_set, difficulty):
    if difficulty.questions == "easy":
        # Use simpler questions or provide hints
        question, answer = random.choice(question_set)
        return question + " (Hint: First letter is " + answer[0] + ")", answer
    elif difficulty.questions == "hard":
        # Use harder questions or remove hints
        question, answer = random.choice(question_set)
        return question.replace("?", "? (No hints available)"), answer
    else:
        # Normal difficulty - standard questions
        return random.choice(question_set)

# Start the adventure
def start_adventure(game_state):
    iq = game_state.iq
    game_state.current_thief = random.choice(list(thieves.keys()))
    
    countdown(5, "BREAKING NEWS: Your diploma has been stolen! Investigation begins in...")
    type_writter(f"{thieves[game_state.current_thief]['name']} has stolen your diploma!", speed=0.02)
    type_writter(f"Motive: {thieves[game_state.current_thief]['motive']}", speed=0.02, color=Fore.YELLOW)

    while not all(loc["completed"] for loc in game_state.completed_paths.values()):
        # Show available locations
        type_writter("Available locations:", speed=0.02)
        available_locations = []
        for i, (loc, data) in enumerate(game_state.completed_paths.items(), 1):
            if not data["completed"]:
                available_locations.append(loc)
                type_writter(f"{len(available_locations)}. {loc.title()}", speed=0.02, color=Fore.CYAN)
                
        # Always show status check as the next number after locations
        status_option = len(available_locations) + 1
        type_writter(f"{status_option}. Check Status", speed=0.02, color=Fore.YELLOW)
        type_writter("(Type 'minigame' to play a random minigame)", speed=0.02, color=Fore.YELLOW)
        
        choice = input(Fore.YELLOW + "\nChoose location (or type 'inventory'/'minigame'): ").lower()
        
        if choice == "minigame":
            type_writter("\nStarting random minigame!", speed=0.02, color=Fore.YELLOW)
            bonus_iq = play_random_minigame()
            if bonus_iq > 0:
                type_writter(f"You gained {bonus_iq} bonus IQ points!", speed=0.02, color=Fore.GREEN)
                game_state.iq += bonus_iq
            pause_line()
            clear_system()
            continue

        if choice.lower() == "inventory":
            show_inventory()
            continue
            
        # Update game_state.iq after each location
        if iq != game_state.iq:
            game_state.iq = iq

        try:
            choice_num = int(choice)
            if choice_num == status_option:
                show_iq_status(iq)
                continue
            elif 1 <= choice_num <= len(available_locations):
                loc_name = available_locations[choice_num - 1]
                if loc_name == "darlingtonpark" and not game_state.completed_paths["darlingtonpark"]["completed"]:
                    iq = darlington_park(iq, game_state)
                    game_state.completed_paths["darlingtonpark"]["completed"] = True
                elif loc_name == "foodland" and not game_state.completed_paths["foodland"]["completed"]:
                    iq = foodland(iq, game_state)
                    game_state.completed_paths["foodland"]["completed"] = True
                elif loc_name == "timhortons" and not game_state.completed_paths["timhortons"]["completed"]:
                    iq = tim_hortons(iq, game_state)
                    game_state.completed_paths["timhortons"]["completed"] = True
                elif loc_name == "community" and not game_state.completed_paths["community"]["completed"]:
                    iq = community_complex(iq, game_state)
                    game_state.completed_paths["community"]["completed"] = True
            else:
                print(Fore.RED + "Invalid choice!")
        except ValueError:
            if choice not in ["inventory", "minigame"]:
                print(Fore.RED + "Invalid choice!")
            continue

        if iq < 50:
            type_writter("💀 Your IQ is too low to continue. The thief takes your diploma, and you fail to graduate.", 
                        speed=0.02, color=Fore.RED)
            return
        
        # Show progress after each completed path
        remaining = sum(not x["completed"] for x in game_state.completed_paths.values())
        if remaining > 0:
            type_writter(f"\nYou must investigate {remaining} more location(s) before confronting the thief!", 
                        speed=0.02, color=Fore.YELLOW)
            pause_line()
            clear_system()

    type_writter("You've investigated all locations! Time to confront the thief!", speed=0.02)
    pause_line()
    iq = confront_thief(game_state)

    if iq < 50:
        type_writter("💀 Your IQ is too low to continue. The thief takes your diploma, and you fail to graduate.", 
                    speed=0.02, color=Fore.RED)
        return

    determine_ending(game_state)

def random_event(iq, inventory):
    events = [
        {"desc": "A mysterious stranger offers help...", 
         "success": "They give you an energy drink!",
         "fail": "They steal some IQ points!",
         "item": "Energy Drink",
         "iq_change": (-10, 0)},
        {"desc": "You find a hidden cache...",
         "success": "Contains a useful item!",
         "fail": "It was a trap!",
         "item": "Lucky Lure",
         "iq_change": (0, -5)},
        {"desc": "A pop quiz appears!",
         "success": "You ace it!",
         "fail": "You fail it!",
         "item": None,
         "iq_change": (15, -10)}
    ]
    
    if random.random() < 0.3:  # 30% chance of random event
        event = random.choice(events)
        type_writter(event["desc"], speed=0.02, color=Fore.YELLOW)
        pause_line()
        
        if random.random() < 0.5:  # 50% success rate
            type_writter(event["success"], speed=0.02, color=Fore.GREEN)
            if event["item"]:
                inventory.append(event["item"])
            iq += event["iq_change"][0]
        else:
            type_writter(event["fail"], speed=0.02, color=Fore.RED)
            iq += event["iq_change"][1]
            
    return iq

# Add this structure for each major location
def darlington_park(iq, game_state):
    locations = {
        "hiking_trail": {
            "desc": "A winding trail through dense forest...",
            "questions": geography_questions,
            "rewards": ["Compass", "Map Piece", "Wildlife Guide"]
        },
        "waterfront": {
            "desc": "A peaceful lake shore with fishing spots...",
            "questions": science_questions,
            "rewards": ["Lucky Lure", "Flashlight", "Map Piece"]
        },
        "visitor_center": {
            "desc": "An informative center with park history...",
            "questions": history_questions,
            "rewards": ["Park Map", "Energy Drink", "Wildlife Guide"]
        },
        "campground": {
            "desc": "A cozy camping area with friendly faces...",
            "questions": riddles_questions,
            "rewards": ["Maple Seeds", "Sandwich", "Compass"]
        }
    }
    return explore_location(iq, game_state, locations, "Darlington Park")

def foodland(iq, game_state):
    locations = {
        "produce_section": {
            "desc": "Fresh fruits and vegetables line the aisles...",
            "questions": science_questions,
            "rewards": ["Energy Drink", "Map Piece", "Sandwich"]
        },
        "bakery": {
            "desc": "The smell of fresh bread fills the air...",
            "questions": math_questions,
            "rewards": ["Sandwich", "Coffee Beans", "Lucky Lure"]
        },
        "deli_counter": {
            "desc": "Various meats and cheeses on display...",
            "questions": history_questions,
            "rewards": ["Sandwich", "Map Piece", "Energy Drink"]
        },
        "freezer_section": {
            "desc": "A chilly area with frozen goods...",
            "questions": riddles_questions,
            "rewards": ["Ice Pack", "Frozen Clue", "Map Piece"]
        }
    }
    return explore_location(iq, game_state, locations, "Foodland")

def tim_hortons(iq, game_state):
    locations = {
        "front_counter": {
            "desc": "The busy counter where orders are taken...",
            "questions": tim_questions,
            "rewards": ["Coffee Beans", "Espresso", "Map Piece"]
        },
        "dining_area": {
            "desc": "Tables filled with customers enjoying their drinks...",
            "questions": riddles_questions,
            "rewards": ["Napkin Note", "Map Piece", "Energy Drink"]
        },
        "drive_thru": {
            "desc": "A line of cars waiting for their orders...",
            "questions": math_questions,
            "rewards": ["Receipt Clue", "Coffee Beans", "Map Piece"]
        },
        "back_room": {
            "desc": "The employee area with supplies...",
            "questions": tim_questions,
            "rewards": ["Master Key", "Coffee Beans", "Employee Schedule"]
        }
    }
    return explore_location(iq, game_state, locations, "Tim Hortons")

def community_complex(iq, game_state):
    locations = {
        "pool_area": {
            "desc": "The chlorine-scented swimming pool...",
            "questions": sports_questions,
            "rewards": ["Locker Key", "Whistle", "Map Piece"]
        },
        "gym": {
            "desc": "Exercise equipment and determined athletes...",
            "questions": science_questions,
            "rewards": ["Energy Drink", "Protein Shake", "Map Piece"]
        },
        "courts": {
            "desc": "Basketball and volleyball courts...",
            "questions": sports_questions,
            "rewards": ["Game Schedule", "Whistle", "Map Piece"]
        },
        "locker_room": {
            "desc": "Rows of lockers and changing areas...",
            "questions": riddles_questions,
            "rewards": ["Master Key", "Hidden Note", "Map Piece"]
        }
    }
    return explore_location(iq, game_state, locations, "Community Complex")

# Fix the explore_location function's key handling
def explore_location(iq, game_state, locations, location_name):
    while iq >= 50:
        # Show available areas
        type_writter(f"\nAreas to explore in {location_name}:", speed=0.02)
        available = []
        
        # Simplify location key handling
        loc_key = location_name.lower().replace(" ", "").replace("complex", "")
        
        if loc_key not in game_state.completed_paths:
            type_writter("Error: Invalid location!", color=Fore.RED)
            return iq
            
        areas = game_state.completed_paths[loc_key]["areas"]
        
        # Fix the numbering of uncompleted areas
        counter = 1
        for area, completed in areas.items():
            if not completed:
                available.append(area)
                type_writter(f"{counter}. {area.replace('_', ' ').title()}", speed=0.02, color=Fore.CYAN)
                counter += 1
        
        if not available:
            type_writter("You've completed all areas here!", color=Fore.GREEN)
            game_state.completed_paths[loc_key]["completed"] = True
            break
            
        # Add option to leave with correct number
        type_writter(f"{counter}. Leave {location_name}", speed=0.02, color=Fore.YELLOW)
        
        choice = input(Fore.YELLOW + f"\nChoose 1-{counter} (or type 'minigame'): ").lower()
        
        # Add direct minigame access
        if choice == 'minigame':
            type_writter("\nStarting random minigame!", speed=0.02, color=Fore.YELLOW)
            bonus_iq = play_random_minigame()  # Remove game_state argument
            if bonus_iq > 0:
                type_writter(f"You gained {bonus_iq} bonus IQ points!", speed=0.02, color=Fore.GREEN)
                iq += bonus_iq
                game_state.iq = iq
            pause_line()
            clear_system()
            continue
        
        if choice == str(len(available) + 1):
            if not any(areas.values()):
                type_writter("You must complete at least one area before leaving!", color=Fore.RED)
                continue
            break
            
        try:
            current = available[int(choice) - 1]
        except:
            type_writter("Invalid choice!", color=Fore.RED)
            continue
            
        # Complete the chosen area
        clear_system()
        type_writter(f"\n{locations[current]['desc']}", speed=0.02)
        
        # Ask questions until area is complete
        questions_to_complete = 3  # Number of questions needed to complete an area
        correct_answers = 0
        
        while correct_answers < questions_to_complete:
            question, answer = get_question_for_difficulty(locations[current]['questions'], game_state.difficulty)
            type_writter("\n" + question, speed=0.02, color=Fore.CYAN)
            
            player_answer = input(Fore.YELLOW + "Your answer: ").lower()
            
            if player_answer == answer.lower():
                type_writter("Correct!", color=Fore.GREEN)
                reward = random.choice(locations[current]['rewards'])
                inventory.append(reward)
                type_writter(f"You found a {reward}!", color=Fore.GREEN)
                iq = adjust_iq(True, iq, game_state)
                correct_answers += 1
            else:
                type_writter(get_fail_message(), color=Fore.RED)
                iq = adjust_iq(False, iq, game_state)
            
            game_state.iq = iq  # Update game state IQ
            type_writter(f"\nCurrent IQ: {iq}", speed=0.02, color=Fore.YELLOW)
            
            if iq < 50:
                return iq
                
            if correct_answers < questions_to_complete:
                type_writter(f"\nQuestions remaining to complete area: {questions_to_complete - correct_answers}", 
                           speed=0.02, color=Fore.YELLOW)
            
            pause_line()
            clear_system()
        
        # Mark area as completed
        areas[current] = True
        type_writter(f"\nArea completed: {current.replace('_', ' ').title()}", color=Fore.GREEN)
        
        # Random chance for fight
        if random.random() < 0.4:  # 40% chance for fights
            type_writter("\nA suspicious person appears!", speed=0.02, color=Fore.RED)
            win, iq = fight("Mysterious Figure", iq, game_state)
            if not win:
                continue
        
        # Increase minigame chance to 50% if no fight occurs
        elif random.random() < 0.5:  # 50% chance for minigame if no fight (up from 30%)
            type_writter("\nA gust of wind scatters diplomas everywhere!", speed=0.02, color=Fore.YELLOW)
            bonus_iq = catch_diploma_game()
            if bonus_iq > 0:
                type_writter(f"You gained {bonus_iq} bonus IQ points!", speed=0.02, color=Fore.GREEN)
                iq += bonus_iq
                game_state.iq = iq
            pause_line()
            clear_system()

    return iq

# Confront the thief
def confront_thief(game_state):
    iq = game_state.iq
    current_thief = game_state.current_thief  # Get current thief from game state
    thief = thieves[current_thief]  # Get thief details
    
    countdown(3, "Preparing for final confrontation...")
    pause_line()

    # Different resolutions based on thief type
    if thief['weakness'] in inventory:
        type_writter(f"You use the {thief['weakness']} against {thief['name']}!", speed=0.02, color=Fore.GREEN)
        type_writter("They surrender immediately!", speed=0.02)
        iq += 30
    
    elif "Map Piece" in inventory and inventory.count("Map Piece") >= 2:
        type_writter("Using the map pieces, you find their secret hideout!", speed=0.02, color=Fore.GREEN)
        type_writter(f"{thief['name']} is caught off guard and surrenders.", speed=0.02)
        iq += 20
        
    elif "Whistle" in inventory:
        type_writter("You blow the whistle, alerting nearby teachers!", speed=0.02, color=Fore.CYAN)
        type_writter(f"{thief['name']} tries to run but gets caught.", speed=0.02)
        iq += 15
        
    else:
        # Challenge based on thief's strength
        type_writter(f"{thief['name']} challenges you to prove your worth!", speed=0.02)
        if thief['strength'] == "Math questions":
            question, answer = random.choice(math_questions)
        elif thief['strength'] == "Sports questions":
            question, answer = ("What sport is played at Wimbledon?", "tennis")
        else:
            question, answer = random.choice(science_questions)
            
        type_writter(question, speed=0.02, color=Fore.CYAN)
        player_answer = input(Fore.YELLOW + "Your answer: ").lower()
        
        if player_answer == answer.lower():
            type_writter("You win the challenge!", color=Fore.GREEN)
            iq += 25
        else:
            type_writter("You lose the challenge but recover your diploma anyway.", color=Fore.RED)
            iq -= 10

    inventory.append(thief['final_item'])
    type_writter("You recover your diploma and prepare to graduate!", speed=0.02, color=Fore.GREEN)
    return iq

# Restore previous ending function
def determine_ending(game_state):
    type_writter("\nGraduation Results:", speed=0.02, color=Fore.YELLOW)
    
    # Show inventory effects first
    if "Energy Drink" in inventory:
        type_writter("Energy drink boosts final score!", color=Fore.GREEN)
        game_state.iq += 20
    if "Lucky Lure" in inventory:
        type_writter("Lucky lure grants bonus points!", color=Fore.GREEN)
        game_state.iq += 10
        
    # Show single final score
    type_writter(f"\nFinal IQ: {game_state.iq}", speed=0.02, color=Fore.MAGENTA)
    
    # Show ending based on IQ
    if game_state.iq >= 150:
        type_writter("🌟 Valedictorian! You graduate with highest honors!", speed=0.02, color=Fore.GREEN)
    elif game_state.iq >= 100:
        type_writter("🎉 Graduate with honors!", speed=0.02, color=Fore.YELLOW)
    elif game_state.iq >= 50:
        type_writter("😐 Graduate normally", speed=0.02, color=Fore.RED)
    else:
        type_writter("💀 Failed graduation", speed=0.02, color=Fore.RED)
    
    # Save and show high scores
    game_state.save_high_score(game_state.player_name, game_state.iq)
    type_writter("\nHigh Scores:", speed=0.02, color=Fore.YELLOW)
    for i, (name, score) in enumerate(game_state.high_scores[:5], 1):
        type_writter(f"{i}. {name}: {score}", speed=0.02, color=Fore.CYAN)
    
    # Ask to play again
    type_writter("\nWould you like to play again? (Y/N)", speed=0.02)
    return input(Fore.YELLOW + "> ").lower() == 'y'

# Add use_item function
def use_item(item, iq, context=None):
    if item not in inventory:
        type_writter("You don't have that item!", color=Fore.RED)
        return iq
    
    effect = 0
    message = ""
    remove_item = True
    
    item_lower = item.lower()
    
    if "energy drink" in item_lower:
        effect = 20
        message = "You feel energized! (+20 IQ)"
    elif "sandwich" in item_lower:
        effect = 10
        message = "The sandwich restores your energy! (+10 IQ)"
    elif "espresso" in item_lower:
        effect = 5
        message = "You feel more alert! (+5 IQ)"
    elif "flashlight" in item_lower and context == "dark_area":
        message = "The flashlight illuminates your way!"
        remove_item = False
    elif "master key" in item_lower and context == "locked_door":
        message = "The master key opens the door!"
        remove_item = False
    elif "lucky lure" in item_lower:
        message = "The lure glows with fortune! (Next question has a hint)"
    elif "compass" in item_lower:
        message = "The compass prevents you from getting lost!"
        remove_item = False
    else:
        type_writter(f"Can't use {item} here!", color=Fore.RED)
        return iq

    if remove_item:
        inventory.remove(item)
    
    type_writter(message, color=Fore.GREEN)
    return iq + effect

def ask_question(answer, iq):
    # Give option to use items before answering
    type_writter("Would you like to use an item before answering? (Y/N)", speed=0.02)
    if input(Fore.YELLOW + "> ").lower() == 'y':
        used, item = show_inventory()
        if used:
            iq = use_item(item, iq, "question")
    
    player_answer = input(Fore.YELLOW + "Your answer: ").lower()
    return player_answer == answer.lower(), iq

# Add at top of file
game_state = None

# Example usage in a location function
def some_location(iq, game_state):
    # ...existing code...
    question, answer = get_question_for_difficulty(geography_questions, game_state.difficulty)
    type_writter(question, speed=0.02, color=Fore.CYAN)
    player_answer = input(Fore.YELLOW + "Your answer: ").lower()
    if player_answer == answer.lower():
        type_writter("Correct!", color=Fore.GREEN)
        iq = adjust_iq(True, iq, game_state)
    else:
        type_writter("Wrong!", color=Fore.RED)
        iq = adjust_iq(False, iq, game_state)
    return iq

# Add new minigame function
def catch_diploma_game():
    """A simple catch game where you move left/right to catch falling diplomas"""
    WIDTH = 20
    HEIGHT = 10
    player_pos = WIDTH // 2
    score = 0
    diplomas = []
    lives = 30
    
    def draw_game(player_pos, diplomas, score, lives):
        os.system('cls' if os.name == 'nt' else 'clear')
        print(f"Score: {score} Lives: {'❤️' * lives}")
        
        for y in range(HEIGHT):
            line = ""
            for x in range(WIDTH):
                if [x, y] in diplomas:
                    line += "📜"  # Diploma
                elif y == HEIGHT-1 and x == player_pos:
                    line += "🏃"  # Player
                else:
                    line += " "
            print(line)
        print("=" * WIDTH)  # Ground
        print("Enter 'a' for left, 'd' for right, 'q' to quit")

    type_writter("\nQuick! Catch the falling diplomas!", speed=0.02, color=Fore.YELLOW)
    type_writter("Each catch increases your IQ!", speed=0.02, color=Fore.GREEN)
    countdown(3, "Starting in...")

    start_time = time.time()
    while lives > 0:
        if random.random() < 0.2:
            diplomas.append([random.randint(0, WIDTH-1), 0])
            
        for diploma in diplomas[:]:
            diploma[1] += 1
            if diploma[1] >= HEIGHT:
                diplomas.remove(diploma)
                lives -= 1
        
        # Replace msvcrt with standard input
        draw_game(player_pos, diplomas, score, lives)
        try:
            import select
            if select.select([sys.stdin], [], [], 0.05)[0]:
                key = sys.stdin.read(1).lower()
                if key == 'a':  # Left
                    player_pos = max(0, player_pos - 2)
                elif key == 'd':  # Right
                    player_pos = min(WIDTH-1, player_pos + 2)
                elif key == 'q':  # Quit
                    break
        except:
            # Fallback for systems without select
            key = input().lower()
            if key == 'a':  # Left
                player_pos = max(0, player_pos - 2)
            elif key == 'd':  # Right
                player_pos = min(WIDTH-1, player_pos + 2)
            elif key == 'q':  # Quit
                break
                
        for diploma in diplomas[:]:
            if diploma[1] == HEIGHT-1 and diploma[0] == player_pos:
                diplomas.remove(diploma)
                score += 1
        
        if time.time() - start_time > 30:
            break

    type_writter(f"\nGame Over! You caught {score} diplomas!", speed=0.02, color=Fore.YELLOW)
    return score * 5

# Add new Minigame class
class Minigame(Enum):
    CATCH_DIPLOMA = "Catch the Diploma"
    # Add more minigames here as we create them

# Fix play_random_minigame function
def play_random_minigame():
    """Play a random minigame and return IQ points earned"""
    game = random.choice(list(Minigame))
    
    if game == Minigame.CATCH_DIPLOMA:
        return catch_diploma_game()
    # Add more minigames here as we create them
    
    return 0

# Fix explore_location function's minigame handling
def explore_location(iq, game_state, locations, location_name):
    while iq >= 50:
        # Show available areas
        type_writter(f"\nAreas to explore in {location_name}:", speed=0.02)
        available = []
        
        # Simplify location key handling
        loc_key = location_name.lower().replace(" ", "").replace("complex", "")
        
        if loc_key not in game_state.completed_paths:
            type_writter("Error: Invalid location!", color=Fore.RED)
            return iq
            
        areas = game_state.completed_paths[loc_key]["areas"]
        
        # Fix the numbering of uncompleted areas
        counter = 1
        for area, completed in areas.items():
            if not completed:
                available.append(area)
                type_writter(f"{counter}. {area.replace('_', ' ').title()}", speed=0.02, color=Fore.CYAN)
                counter += 1
        
        if not available:
            type_writter("You've completed all areas here!", color=Fore.GREEN)
            game_state.completed_paths[loc_key]["completed"] = True
            break
            
        # Add option to leave with correct number
        type_writter(f"{counter}. Leave {location_name}", speed=0.02, color=Fore.YELLOW)
        
        choice = input(Fore.YELLOW + f"\nChoose 1-{counter} (or type 'minigame'): ").lower()
        
        # Add direct minigame access
        if choice == 'minigame':
            type_writter("\nStarting random minigame!", speed=0.02, color=Fore.YELLOW)
            bonus_iq = play_random_minigame()  # Remove game_state argument
            if bonus_iq > 0:
                type_writter(f"You gained {bonus_iq} bonus IQ points!", speed=0.02, color=Fore.GREEN)
                iq += bonus_iq
                game_state.iq = iq
            pause_line()
            clear_system()
            continue
        
        if choice == str(len(available) + 1):
            if not any(areas.values()):
                type_writter("You must complete at least one area before leaving!", color=Fore.RED)
                continue
            break
            
        try:
            current = available[int(choice) - 1]
        except:
            type_writter("Invalid choice!", color=Fore.RED)
            continue
            
        # Complete the chosen area
        clear_system()
        type_writter(f"\n{locations[current]['desc']}", speed=0.02)
        
        # Ask questions until area is complete
        questions_to_complete = 3  # Number of questions needed to complete an area
        correct_answers = 0
        
        while correct_answers < questions_to_complete:
            question, answer = get_question_for_difficulty(locations[current]['questions'], game_state.difficulty)
            type_writter("\n" + question, speed=0.02, color=Fore.CYAN)
            
            player_answer = input(Fore.YELLOW + "Your answer: ").lower()
            
            if player_answer == answer.lower():
                type_writter("Correct!", color=Fore.GREEN)
                reward = random.choice(locations[current]['rewards'])
                inventory.append(reward)
                type_writter(f"You found a {reward}!", color=Fore.GREEN)
                iq = adjust_iq(True, iq, game_state)
                correct_answers += 1
            else:
                type_writter(get_fail_message(), color=Fore.RED)
                iq = adjust_iq(False, iq, game_state)
            
            game_state.iq = iq  # Update game state IQ
            type_writter(f"\nCurrent IQ: {iq}", speed=0.02, color=Fore.YELLOW)
            
            if iq < 50:
                return iq
                
            if correct_answers < questions_to_complete:
                type_writter(f"\nQuestions remaining to complete area: {questions_to_complete - correct_answers}", 
                           speed=0.02, color=Fore.YELLOW)
            
            pause_line()
            clear_system()
        
        # Mark area as completed
        areas[current] = True
        type_writter(f"\nArea completed: {current.replace('_', ' ').title()}", color=Fore.GREEN)
        
        # Random chance for fight
        if random.random() < 0.4:  # 40% chance for fights
            type_writter("\nA suspicious person appears!", speed=0.02, color=Fore.RED)
            win, iq = fight("Mysterious Figure", iq, game_state)
            if not win:
                continue
        
        # Increase minigame chance to 50% if no fight occurs
        elif random.random() < 0.5:  # 50% chance for minigame if no fight (up from 30%)
            type_writter("\nA gust of wind scatters diplomas everywhere!", speed=0.02, color=Fore.YELLOW)
            bonus_iq = catch_diploma_game()
            if bonus_iq > 0:
                type_writter(f"You gained {bonus_iq} bonus IQ points!", speed=0.02, color=Fore.GREEN)
                iq += bonus_iq
                game_state.iq = iq
            pause_line()
            clear_system()

    return iq

# Modify main game loop
def main():
    try:
        while True:
            game_state = GameState()
            clear_system()
            
            name = input(Fore.YELLOW + "What is your name?: ").upper()
            if not name:
                name = "PLAYER"
            game_state.player_name = name
            
            intro(name, game_state)
            if not determine_ending(game_state):
                break
    except KeyboardInterrupt:
        type_writter("\nGame interrupted. Cleaning up...", color=Fore.YELLOW)
    finally:
        cleanup()

def cleanup():
    try:
        pygame.mixer.quit()
        pygame.quit()
    except:
        pass
    finally:
        print(Fore.YELLOW + "\nThanks for playing!")

def show_iq_status(iq):
    type_writter(f"\nCurrent IQ: {iq}", speed=0.02, color=Fore.YELLOW)
    if iq >= 150:
        status = "On track for Valedictorian! 🌟"
    elif iq >= 120:
        status = "On track for Honors! 🎉"
    elif iq >= 100:
        status = "On track to graduate 😐"
    elif iq <= 200:
        status = "Wow you're really smart! 😀"
    elif iq <= 250:
        status = "I think harvard wants you... 😭"
    elif iq <= 350:
        status = "... Are you the smartest person ever? ☠️"
    elif iq >= 500:
        status = "Fine you win 🎗️"
    else:
        status = "At risk of failing! ⚠️"
    type_writter(status, speed=0.02, color=Fore.CYAN)

if __name__ == "__main__":
    main()
