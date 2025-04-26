# Virtual-Assistant-Jarvis-
mport speech_recognition as sr
import webbrowser
import pyttsx3
import datetime
import wikipedia
import pywhatkit
import tkinter as tk
import threading

# Initialize TTS engine
engine = pyttsx3.init('sapi5')
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[0].id)

def speak(audio):
    engine.say(audio)
    engine.runAndWait()

def wishMe():
    hour = int(datetime.datetime.now().hour)
    if 0 <= hour < 12:
        speak("Good Morning Kopal Maam!")
    elif 12 <= hour < 18:
        speak("Good Afternoon Kopal Maam!")
    else:
        speak("Good Evening Kopal Maam!")
    speak("I Am Jarvis, Please tell me how may I help you")

def takeCommand():
    r = sr.Recognizer()
    with sr.Microphone() as source:
        status_label.config(text="Listening...")
        r.pause_threshold = 1
        r.energy_threshold = 100
        audio = r.listen(source)
    try:
        status_label.config(text="Recognizing...")
        query = r.recognize_google(audio, language='en-in')
        status_label.config(text=f"You said: {query}")
        speak(query)
    except Exception as e:
        print(f"Error: {e}")
        status_label.config(text="Sorry, say that again please...")
        speak("Say that again please...")
        return ""
    return query.lower()

def runAssistant():
    wishMe()
    while True:
        query = takeCommand()
        if not query:
            continue

        if 'wikipedia' in query:
            speak('Searching Wikipedia...')
            query = query.replace("wikipedia", "")
            results = wikipedia.summary(query, sentences=2)
            speak("According to Wikipedia")
            status_label.config(text=results)
            speak(results)

        elif 'open youtube' in query:
            webbrowser.open("https://www.youtube.com")
            break

        elif 'open google' in query:
            webbrowser.open("https://www.google.com")
            break

        elif 'the time' in query:
            time = datetime.datetime.now().strftime("%H:%M:%S")
            speak(f"Ma'am, the time is {time}")
            status_label.config(text=f"Time: {time}")

        elif 'who are you' in query:
            speak("I am Jarvis, your personal assistant.")
            status_label.config(text="I am Jarvis, your personal assistant.")

        elif 'play music' in query or 'play song' in query:
            speak("Which song would you like to hear?")
            song_query = takeCommand()
            speak(f"Playing {song_query} on YouTube")
            pywhatkit.playonyt(song_query)
            break

        elif 'exit' in query:
            speak("Shutting down. Goodbye Ma'am.")
            break

        elif 'Jarvis' in query or 'are you there' in query:
            speak("Yes, I am listening...")
            takeCommand()

def start_threaded_assistant():
    thread = threading.Thread(target=runAssistant)
    thread.start()

# GUI setup
root = tk.Tk()
root.title("Jarvis Voice Assistant")
root.geometry("400x300")
root.resizable(False, False)

title = tk.Label(root, text="Jarvis Voice Assistant", font=("Arial", 18, "bold"))
title.pack(pady=10)

status_label = tk.Label(root, text="Click 'Start' to activate Jarvis.", wraplength=350, font=("Arial", 12))
status_label.pack(pady=20)

start_button = tk.Button(root, text="Start", font=("Arial", 14), command=start_threaded_assistant)
start_button.pack(pady=20)

exit_button = tk.Button(root, text="Exit", font=("Arial", 12), command=root.quit)
exit_button.pack()

# Launch the GUI
root.mainloop()
