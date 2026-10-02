import random
import sounddevice as sd
import scipy.io.wavfile as wav
import speech_recognition as sr


kelimeler = {
    "easy": ["apple", "car", "house", "table"],
    "medium": ["computer", "garden", "telephone", "library"],
    "hard": ["electromagnetism", "psychology", "integration", "astronomy"]
}


seviye = input("Choose difficulty (easy/medium/hard): ").lower()
if seviye not in kelimeler:
    print("Invalid choice, easy mode selected.")
    seviye = "easy"

can = 3
puan = 0
sample_rate = 44100
duration = 3  

recognizer = sr.Recognizer()

while can > 0:
    
    hedef = random.choice(kelimeler[seviye])
    print(f"Say this word: {hedef}")

    
    recording = sd.rec(int(duration * sample_rate), samplerate=sample_rate, channels=1, dtype="int16")
    sd.wait()
    wav.write("output.wav", sample_rate, recording)

    # Tanıma
    with sr.AudioFile("output.wav") as source:
        audio = recognizer.record(source)

    try:
        
        text = recognizer.recognize_google(audio, language="en-US").lower()
        print("You said:", text)

        if hedef == text:
            puan += 10
            print("Correct! Score:", puan)
        else:
            can -= 1
            print(f"Wrong! Correct word: {hedef}. Lives left: {can}")

    except sr.UnknownValueError:
        can -= 1
        print(f"Could not understand. Lives left: {can}")
    except sr.RequestError as e:
        print(f"Service error: {e}")
        break

print("Game over! Final score:", puan)
