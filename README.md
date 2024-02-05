import os
import requests
import pyttsx3
import pywhatkit
import speech_recognition as sr
import datetime
import wikipedia
import webbrowser
import googlesearch
from selenium import webdriver




# Инициализация модуля распознавания речи и синтеза речи
engine = pyttsx3.init('sapi5')
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[0].id)

def change_voice(gender='male'):
    voices = engine.getProperty('voices')
    if gender == 'male':
        engine.setProperty('voice', voices[0].id)  # Первый голос в списке обычно мужской
    elif gender == 'female':
        engine.setProperty('voice', voices[1].id)  # Второй голос в списке обычно женский

def speak(text):
    engine.say(text)
    engine.runAndWait()

# Пример использования



def greet():
    hour = datetime.datetime.now().hour
    if 0 <= hour < 12:
        speak("Доброе утро!")
    elif 12 <= hour < 18:
        speak("Добрый день!")
    else:
        speak("Добрый вечер!")
    speak("Я - Джарвис. Чем могу помочь?")

def get_weather(api_key, city):
    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={api_key}&units=metric"
    response = requests.get(url)
    data = response.json()

    if response.status_code == 200:
        weather_description = data['weather'][0]['description']
        temperature = data['main']['temp']
        return f"Погода в {city} сегодня: {weather_description}. Температура {temperature} градусов по Цельсию."
    else:
        return "Невозможно получить информацию о погоде."

def search_wikipedia(query):
    try:
        speak('Ищу в Википедии...')
        results = wikipedia.summary(query, sentences=2)
        speak("Вот что я нашел в Википедии.")
        print(results)
        speak(results)
    except wikipedia.exceptions.DisambiguationError as e:
        speak("Уточните ваш запрос. Несколько статей соответствуют вашему запросу.")
    except wikipedia.exceptions.PageError as e:
        speak("Не удалось найти информацию по вашему запросу.")

# Далее в вашем коде вызывайте функцию search_wikipedia(query) там, где обрабатывается ввод пользователя

def fetch_data_from_internet():
    # Здесь можно добавить код для получения данных из интернета
    response = requests.get("https://example.com/data")
    data = response.json()
    return data

def update_learning_model(data):
    # Здесь можно добавить код для обновления модели обучения на основе полученных данных
    # Например, обновить базу знаний или переобучить модель машинного обучения
    pass




def take_command():
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        print("Слушаю...")
        recognizer.pause_threshold = 1
        audio = recognizer.listen(source)

    try:
        print("Распознавание...")
        query = recognizer.recognize_google(audio, language='ru')
        print(f"Вы сказали: {query}\n")
    except Exception as e:
        print("Повторите, пожалуйста...")
        return "None"
    return query



if __name__ == "__main__":
    greet()
    while True:
        api_key = "64a9a6b56bb7f86149e6441a3c841b01"
        query = take_command().lower()

        if 'википедия' in query:
            speak('Ищу в Википедии...')
            query = query.replace("википедия", "")
            results = wikipedia.summary(query, sentences=2)
            speak("Вот что я нашел в Википедии.")
            print(results)
            speak(results)

        elif 'открой блокнот' in query:
            speak("открываю блокнот")
            os.system("notepad.exe")


        elif 'открой Paint' in query:
            speak("открываю Paint")
            os.system("mspaint")

#https://www.youtube.com/playlist?list=PLLX1i6Jw4VGQooO7HhWJGjquyTu7kc3ab плейлист с музыкой в ютубе
        elif 'открой гугл' in query:
            speak("открываю гугл")
            webbrowser.open("https://www.google.com")



        if 'включи музыку' in query:
            speak("Включаю музыку")
            webbrowser.open("https://www.youtube.com/playlist?list=PLLX1i6Jw4VGQooO7HhWJGjquyTu7kc3ab")


        elif 'время' in query:
            str_time = datetime.datetime.now().strftime("%H:%M:%S")
            speak(f"Сейчас {str_time}")

        elif 'дата' in query:
            current_date = datetime.datetime.now().strftime("%d.%m.%Y")
            speak('Сегодняшняя дата: ' + current_date)


        elif 'спасибо' in query:
            speak("Пожалуйста!")



        elif 'погода' in query:
            speak('Для какого города вы хотите узнать погоду?')
            city = take_command()  # Здесь используйте ваш метод получения ввода от пользователя
            weather_info = get_weather(api_key, city)
            speak(weather_info)

        elif 'поиск в интернете' in query:
            speak('Что искать?')
            search_query = take_command()
            for j in googlesearch.search(search_query, num=10, stop=1, pause=2):
                webbrowser.open(j)



        elif 'спокойной ночи' in query:
            speak("и вам тоже спокойной ночи")
            break

        elif 'пока' in query:
            speak("До свидания!")
            break


while True:
    new_data = fetch_data_from_internet()
    update_learning_model(new_data)
    speak("Я узнал что-то новое. Могу ли я помочь вам чем-то?")
    # Далее продолжайте взаимодействие с пользователем и повторяйте процесс обновления знаний
