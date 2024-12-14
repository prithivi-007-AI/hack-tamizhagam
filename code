!pip install pyttsx3
import tkinter as tk
import pyttsx3
import openai

# OpenAI API key
openai.api_key = "your_openai_api_key_here"

# Initialize pyttsx3 engine
engine = pyttsx3.init()

# Global variables for the game
score = 0
question_count = 0
questions = []  # Dynamic questions from ChatGPT


def talk(text):
    """Speaks the given text using pyttsx3."""
    engine.say(text)
    engine.runAndWait()


def fetch_questions(subject):
    """Fetch questions dynamically from OpenAI API for the selected subject."""
    try:
        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[
                {
                    "role": "system",
                    "content": "You are a quiz generator. Create 5 multiple-choice questions on a given topic. Include the correct answer and three wrong options.",
                },
                {
                    "role": "user",
                    "content": f"Generate quiz questions for {subject}.",
                },
            ],
        )
        questions_data = response.choices[0].message.content
        return parse_questions(questions_data)
    except Exception as e:
        talk("Sorry, I couldn't fetch questions. Please try again.")
        print(f"Error fetching questions: {e}")
        return None


def parse_questions(questions_data):
    """Parses the OpenAI API response into a structured format."""
    questions_list = []
    lines = questions_data.split("\n")
    current_question = {}
    for line in lines:
        if line.startswith("Q:"):
            if current_question:
                questions_list.append(current_question)
            current_question = {"question": line[2:].strip(), "options": [], "answer": ""}
        elif line.startswith("A:"):
            current_question["options"].append(line[2:].strip())
            current_question["answer"] = line[2:].strip()
        elif line.startswith("B:") or line.startswith("C:") or line.startswith("D:"):
            current_question["options"].append(line[2:].strip())
    if current_question:
        questions_list.append(current_question)
    return questions_list


def start_quiz(subject):
    """Starts the quiz for the selected subject."""
    global questions, question_count
    question_count = 0
    talk(f"Fetching questions for {subject}. Please wait.")
    questions = fetch_questions(subject)
    if questions:
        ask_question()
    else:
        talk("No questions available. Please try again later.")


def ask_question():
    """Asks a question from the fetched list."""
    global question_count
    if question_count < len(questions):
        current_question = questions[question_count]
        display_question(current_question)
    else:
        talk(f"Quiz complete! Your final score is {score}.")
        show_leaderboard()


def display_question(question):
    """Displays the current question and its options."""
    question_text.set(f"Q: {question['question']}")
    for i, option in enumerate(question["options"]):
        options_buttons[i].config(text=option, command=lambda opt=option: check_answer(opt, question))


def check_answer(selected_option, question):
    """Checks if the selected option is correct."""
    global score, question_count
    if selected_option == question["answer"]:
        talk("Correct!")
        score += 10
    else:
        talk(f"Incorrect. The correct answer was {question['answer']}.")
    question_count += 1
    ask_question()


def show_leaderboard():
    """Displays the final score."""
    question_text.set(f"Quiz Complete! Your Score: {score}")
    for button in options_buttons:
        button.pack_forget()
    start_button.pack(pady=10)


def choose_subject():
    """Prompts the user to choose a subject."""
    question_text.set("Choose a subject to start:")
    for button, subject in zip(options_buttons, ["Math", "Science", "General Knowledge"]):
        button.config(text=subject, command=lambda subj=subject: start_quiz(subj))
        button.pack()


# Create GUI
root = tk.Tk()
root.title("Gamified Learning Bot")
root.geometry("500x300")

# Variables
question_text = tk.StringVar()
question_text.set("Welcome to the Gamified Learning Bot!")

# Widgets
question_label = tk.Label(root, textvariable=question_text, wraplength=450, font=("Arial", 14))
question_label.pack(pady=20)

options_buttons = [tk.Button(root, font=("Arial", 12)) for _ in range(4)]
for button in options_buttons:
    button.pack_forget()

start_button = tk.Button(root, text="START QUIZ", font=("Arial", 12), command=choose_subject)
start_button.pack(pady=10)

# Main loop
root.mainloop()
