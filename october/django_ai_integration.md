
# Урок: Подключение AI к веб-приложению Django

В этом уроке мы рассмотрим, как интегрировать искусственный интеллект (AI) в веб-приложение Django, используя внешние API для AI, и предоставим информацию о бесплатных сервисах AI для интеграции.

## Шаг 1: Определение задачи

Перед тем как приступить к интеграции AI в ваше приложение Django, нужно четко определить задачу, которую вы хотите решить с помощью AI. Возможные сценарии:
- Обработка изображений (например, распознавание лиц, классификация изображений).
- Обработка текста (анализ тональности, генерация текста, перевод).
- Рекомендательные системы (рекомендации продуктов, фильмов и т. д.).

Мы рассмотрим пример обработки текста, где AI будет анализировать тональность сообщений пользователей и классифицировать их как положительные, нейтральные или отрицательные.

## Шаг 2: Выбор бесплатных AI-сервисов

Есть несколько бесплатных AI-сервисов, которые можно интегрировать в ваше Django-приложение:

1. **OpenAI (GPT-3, GPT-4)** — мощный генератор текста и анализа данных. Бесплатная версия дает ограниченное количество запросов.
2. **Hugging Face** — платформа с множеством моделей машинного обучения для обработки текста, изображений и аудио. Многие модели доступны бесплатно.
3. **IBM Watson** — предоставляет широкий спектр AI-сервисов, включая обработку текста и изображений. Бесплатная версия включает ограниченные ресурсы.
4. **Google Cloud AI** — предлагает бесплатные кредиты для обработки изображений, текста и видео через API.

В этом примере мы используем **Hugging Face** для анализа тональности текста.

## Шаг 3: Установка необходимых библиотек

Для интеграции с внешним AI-сервисом потребуется установить библиотеку для отправки HTTP-запросов — `requests`, а также библиотеку для работы с Hugging Face.

Установите зависимости:

```bash
pip install requests
pip install transformers
```

## Шаг 4: Создание представления для обработки данных

Создадим представление в `views.py`, которое будет отправлять текст на сервер Hugging Face для анализа тональности.

```python
# views.py
import requests
from django.shortcuts import render

def analyze_sentiment(request):
    if request.method == 'POST':
        user_input = request.POST.get('text')

        # Отправка запроса на Hugging Face API
        API_URL = "https://api-inference.huggingface.co/models/distilbert-base-uncased-finetuned-sst-2-english"
        headers = {"Authorization": "Bearer ВАШ_API_КЛЮЧ"}  # Получите API ключ на Hugging Face
        
        def query(payload):
            response = requests.post(API_URL, headers=headers, json=payload)
            return response.json()
        
        # Запрос к модели для анализа текста
        output = query({"inputs": user_input})

        return render(request, 'result.html', {'output': output})

    return render(request, 'analyze.html')
```

## Шаг 5: Шаблон для ввода текста

Создадим простой HTML-шаблон `analyze.html`, где пользователь сможет ввести текст для анализа.

```html
<!-- analyze.html -->
<form method="POST">
    {% csrf_token %}
    <textarea name="text" placeholder="Введите текст для анализа"></textarea>
    <button type="submit">Анализировать</button>
</form>
```

## Шаг 6: Шаблон для отображения результатов

Шаблон `result.html` будет отображать результат анализа тональности.

```html
<!-- result.html -->
<h2>Результат анализа тональности:</h2>
<p>{{ output }}</p>
<a href="{% url 'analyze_sentiment' %}">Попробовать снова</a>
```

## Шаг 7: Настройка URL-адресов

Теперь добавим маршрут для нашего нового представления в `urls.py`.

```python
# urls.py
from django.urls import path
from .views import analyze_sentiment

urlpatterns = [
    path('analyze/', analyze_sentiment, name='analyze_sentiment'),
]
```

## Шаг 8: Получение API-ключа Hugging Face

Чтобы использовать Hugging Face API, вам нужно зарегистрироваться на сайте [Hugging Face](https://huggingface.co/) и получить API-ключ. Бесплатный тариф предоставляет доступ к широкому спектру моделей с ограничениями по числу запросов.

## Шаг 9: Тестирование приложения

Теперь, когда всё настроено, запустите сервер Django:

```bash
python manage.py runserver
```

Перейдите по адресу `http://localhost:8000/analyze/` и протестируйте ввод текста. AI-сервис Hugging Face выполнит анализ и вернёт результат, который будет отображён на странице.

---

## Бесплатные AI-сервисы для интеграции

1. **Hugging Face** — предоставляет бесплатный доступ к множеству моделей для обработки текста, изображений и аудио.
2. **IBM Watson** — предлагает широкий спектр AI-сервисов, таких как классификация текста, извлечение сущностей, перевод и анализ тональности. Бесплатная версия ограничена.
3. **OpenAI (GPT-3/4)** — можно использовать для генерации текста, анализа и обработки данных. Бесплатная версия предоставляет ограниченные запросы.
4. **Google Cloud AI** — предоставляет бесплатные кредиты для тестирования API по обработке изображений, текста и видео.

Для большинства из этих сервисов потребуется зарегистрироваться и получить бесплатные API-ключи.
