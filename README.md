# Jarvis_v1
This is my first project on GitHub<br>

Jarvis on Python. The first version was created using the vosk library. A small Russian vosk model was used.

Jarvis can:
<ul>
  <li>open browser</li>
  <li>close programs</li>
  <li>search for videos on YouTube</li>
  <li>tell jokes</li>
  <li>hide windows</li>
  <li>look for information from Wikipedia</li>
  <li>take a screenshot of the screen</li>
  <li>open Windows settings</li>
  <li>change language</li>
</ul>
<br>
<p><b>installing the necessary libraries</b></p>


```pip install vosk```<br>
```pip install pyaudio```<br>
```pip install pyautogui```<br>
```pip install wikipedia```<br>
```pip install pywhatkit```<br>
```pip install beautifulsoup4```<br><br><br>

<h1>File main.py</h1>

```python
from vosk import *
from fnc import *
import json, pyaudio

def response(query):
    res = None
    max = 0
    words_query = query.split(' ')
    for key, arr in opts.items():
        k = 0
        for value in arr:
            for word in words_query:
                if str(word) == str(value):
                    k += 2
                if word in value:
                    k += 1
        if k > max:
            max = k
            res = key
    return res

model = Model('small_model')
rec = KaldiRecognizer(model, 16000)
p = pyaudio.PyAudio()
stream = p.open(format=pyaudio.paInt16, channels=1, rate=16000, input=True, frames_per_buffer=8000)
stream.start_stream()

#Команды/не зависит от построения
opts = {
    'hi_jarvis': ('привет', 'добрый день', 'как дела'),
    'open_br': ('браузер','гугл',' веб-браузер'),
    'good_jarvis': ('ты','молодец',"хороший"),
    'wiki': ('вики', 'википедия','что такое'),
    'switch_language': ('смени','поменяй','раскладку','язык'),
    'open_options': ('настройки', 'опции'),
    'screenshot': ('скриншот', 'экрана', 'снимок', 'экран'),
    'kill_process': ('заверши', 'процесс', 'завершить', 'программу','закрой'),
    'hide_windows': ('свернуть', 'окно', 'окна', 'рабочий', 'стол', 'спрячь', 'сверни', 'спрятать','спрятать окна на рабочем столе'),
    'get_joke': ('пошути','расскажи шутку','анекдот, пожалуйста'),
    'find_yt_video': ('найди', 'видео', 'на ютубе ',"ютуб","искать"),
    'search_google': ('напиши','введи', 'ютуб'),
    }

#Функция прослушки текста
def listen():
    while True:
        data = stream.read(4000, exception_on_overflow=False)
        if (rec.AcceptWaveform(data)) and (len(data)>0):
            answer = json.loads(rec.Result())
            if answer['text']:
                yield answer['text']

#Проверка прослушки
for text in listen():
    print(text)
    if 'джарвис' in text:
        text = text.lower()
        text = text.replace('джарвис', '')
        text = text.strip()
        res = response(text)

        if res == 'hi_jarvis':
            hi_jarvis()
        elif res == 'open_br':
            open_br()
        elif res == 'good_jarvis':
            good_jarvis()
        elif res == 'wiki':
            wiki(text)
        elif res == 'switch_language':
            switch_language()
        elif res == 'open_options':
            open_options()
        elif res == 'screenshot':
            screenshot()
        elif res == 'kill_process':
            kill_process()
        elif res == 'hide_windows':
            hide_windows()
        elif res == 'get_joke':
            get_joke()
        elif res == 'find_yt_video':
            command_words = ['найди', 'видео', 'на ютубе', 'ютуб', 'искать']
            for word in command_words:
                text = text.replace(word, '')
            text = text.strip()
            find_yt_video(text)
        elif res == 'search_google':
            text = text.replace('введи','')
            text = text.replace('напиши', '')
            search_google(text)
```
<br><br><br>

<h2>File fnc.py</h2><br><br>

```python
import pyttsx3, bs4
import random, pyautogui, pywhatkit
import webbrowser as wb
import requests
import wikipedia



def say(text):
    ttsEngine = pyttsx3.init()
    ttsEngine.say(text)
    ttsEngine.runAndWait()


def hi_jarvis():
    c = random.randint(1,2)
    if c == 1:
        say('Слушаю вас')
    elif c == 2:
        say('Готов работать, сэр')



def open_br():
    wb.open_new_tab('https://www.google.com/')
    say('Будет сделано')

def good_jarvis():
    say('Тонко подмечено, сэр')


def wiki(text):
    try:
        wikipedia.set_lang('ru')
        res = wikipedia.summary(text)
        say(res)
    except:
        say('Я не понял запрос ни википедию')


def switch_language():
    pyautogui.hotkey('alt', 'shift')
    say('выполнено')

def open_options():
    pyautogui.hotkey('winleft', 'i')
    say('выполнено')

def screenshot():
    pyautogui.hotkey('win', 'printscreen')
    say('выполнено')

def kill_process():
    pyautogui.hotkey('alt', 'f4')
    say('Процесс завершён')

def hide_windows():
    pyautogui.hotkey('winleft', 'd')
    say('Выполнено')

def get_joke():
    response = requests.get('http://anekdotme.ru/random')
    soup = bs4.BeautifulSoup(response.text, 'html.parser')
    jokes_list = soup.find_all('div', class_='anekdot_text')
    random_joke = random.choice(jokes_list).text.strip()
    say(random_joke)

def find_yt_video(text):
    text = del_first_word(text)
    pywhatkit.playonyt(text)
    say('Выполнено')

def search_google(text):
    pywhatkit.search(text)
    say('Выполнено')

def del_first_word(text):
    text = text.split(' ')
    text.pop(0)
    text = ' '.join(text)
    return text
```





