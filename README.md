# IMPORTING MODULES
from datetime import datetime
from googleapiclient.discovery import build
import speech_recognition as sr
import pyttsx3
import webbrowser
import pyjokes
#  INITIALIZING THE OBJECTS FROM THIER RESPECTIVE CLASS
Recognizer = sr.Recognizer()
Engine = pyttsx3.init()
Engine.setProperty('rate', 150)
Engine.setProperty('volume', )

API_KEY = "AIzaSyBGfyXLjRgbqqRKCj7yTBYWwpoYEpfndjw"
youtube = build("youtube", "v3", developerKey=API_KEY)

def search_youtube(query):
    """Search YouTube for a video matching the query."""
    request = youtube.search().list(
        part="snippet",
        q=query,
        type="video",
        maxResults=1
    )
    response = request.execute()

    # Extract the first search result
    video = response["items"][0]
    video_title = video["snippet"]["title"]
    video_url = f"https://www.youtube.com/watch?v={video['id']['videoId']}"

    print(f"Found: {video_title}")
    print(f"Play this video: {video_url}")

    return video_url

def play_song(song_name):
    """Search for a song and open it in the browser."""
    video_url = search_youtube(song_name)
    webbrowser.open(video_url)

def speak_old(text):
    Engine.say(text)
    Engine.runAndWait()
if (__name__ == "__main__") :
    speak_old("INITIALIZING JARVIS...")
# LISTEN FOR THE ACTIVATION WORD JARVIS
    while True:
        with sr.Microphone() as source:
            Recognizer.adjust_for_ambient_noise(source)
            print("LISTENING.....")
            audio = Recognizer.listen(source, timeout=2, phrase_time_limit=2)
            try:
                print("recognizing")
                Gcommand = Recognizer.recognize_google(audio).lower()
                print(Gcommand)

                # using google speech recognition
                if "jarvis" in Gcommand.lower():
                    speak_old("how can i help you?")
                    with sr.Microphone() as source:
                        Recognizer.adjust_for_ambient_noise(source, duration=2)
                        print("listening for the command")
                        audio1 = Recognizer.listen(source, timeout=3, phrase_time_limit=3)
                        command = Recognizer.recognize_google(audio1)
                        print("\nPROCESSING AUDIO...\n",command)
                        new_command = command.lower()
                        if "open google" in new_command:
                            speak_old("Opening google")
                            webbrowser.open("https://google.com")
                        elif "open facebook" in new_command:
                            speak_old("Opening facebook")
                            webbrowser.open("https://facebook.com")
                        elif "open youtube" in new_command:
                            speak_old("Opening youtube")
                            webbrowser.open("https://youtube.com")
                        elif "open linkedin" in new_command:
                            speak_old("Opening linkedin")
                            webbrowser.open("https://linkedin.com")
                        elif new_command.startswith("play"):
                            song_name = new_command.replace("play", "").strip()
                            speak_old(f"Searching for {song_name} on YouTube...")
                            play_song(song_name)
                        elif 'joke' in new_command:
                            joke = pyjokes.get_joke(category="neutral")
                            speak_old(joke)
                        elif 'time' in new_command:
                            now = datetime.now()
                            hours = now.strftime("%I")  # 12-hour format
                            minutes = now.strftime("%M")
                            period = now.strftime("%p")  # AM or PM

                            # Convert minutes to spoken form
                            if minutes == "00":
                                time_in_words = f"It is {hours} o'clock {period}."
                            else:
                                time_in_words = f"It is {hours}:{minutes} {period}."
                            
                            print(f"{hours}:{minutes}")
                            speak_old(time_in_words)

                if "shut" in Gcommand.lower():
                    print("SHUTTING DOWN...")
                    speak_old("SHUTTING DOWN")
                    break
            except sr.UnknownValueError:
                print("Sorry, I did not get that")
            except sr.RequestError as a:
                print(f"Could not request results from Google Speech Recognition service;{a}")
            except sr.WaitTimeoutError:
                speak_old("No speech detected")
