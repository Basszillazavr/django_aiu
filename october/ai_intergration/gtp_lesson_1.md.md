
# Обновлённый план по созданию сайта для генерации курсовых и дипломных работ с использованием GPT

Этот план описывает шаги для создания веб-сайта, который генерирует академические документы (курсовая, дипломная работа, эссе и т.д.), используя GPT для генерации текста и формата DOC для выхода.

## Шаг 1: Определение функциональности

1. Пользователь вводит тип документа (курсовая, дипломная работа, эссе и т.д.) и тему.
2. GPT генерирует текст на основе введённых данных.
3. Сайт возвращает сгенерированный текст в формате DOC.

## Шаг 2: Выбор технологии

1. **Backend:** Django — для обработки запросов и интеграции с GPT.
2. **Frontend:** HTML/CSS/JavaScript для пользовательского интерфейса.
3. **API для GPT:** OpenAI API (версия 0.28.0).
4. **Генерация DOC-файлов:** Использование библиотеки `python-docx` для создания документов в формате DOC.

## Шаг 3: Структура проекта

1. **Frontend:**
   - Форма для ввода типа документа и темы.
   - Кнопка отправки данных.
   - Поле для отображения ссылки на сгенерированный документ.
   
2. **Backend:**
   - Обработка запроса с формы.
   - Интеграция с GPT для генерации текста.
   - Генерация DOC-файла и его отправка пользователю.

## Шаг 4: Создание Django-проекта

1. Создайте проект Django:

   ```bash
   django-admin startproject gpt_courses
   cd gpt_courses
   python manage.py startapp generator
   ```

2. Настройте маршруты для обработки запросов в файле `urls.py`:

   ```python
   # urls.py
   from django.urls import path
   from generator import views

   urlpatterns = [
       path('', views.generate_doc, name='generate_doc'),
   ]
   ```

3. Добавьте приложение в `INSTALLED_APPS` в `settings.py`.

## Шаг 5: Создание формы для ввода данных

HTML-форма для сбора данных:

```html
<!-- templates/index.html -->
<form method="POST" action="{% url 'generate_doc' %}">
    {% csrf_token %}
    <label>Тип документа:</label>
    <select name="doc_type">
        <option value="coursework">Курсовая</option>
        <option value="thesis">Дипломная</option>
        <option value="essay">Эссе</option>
    </select>
    <br>
    <label>Тема:</label>
    <input type="text" name="topic" required>
    <br>
    <button type="submit">Сгенерировать документ</button>
</form>
```

## Шаг 6: Интеграция с GPT для генерации текста

1. Установите библиотеку OpenAI версии `0.28.0`:

   ```bash
   pip install openai==0.28.0
   ```

2. В `views.py` реализуйте функцию для обработки запроса:

   ```python
   # views.py
   import openai
   from django.shortcuts import render
   from django.http import HttpResponse
   from docx import Document
   from dotenv import load_dotenv
   import os

   load_dotenv()  # Загрузить переменные окружения из .env

   openai.api_key = os.getenv("OPENAI_API_KEY")

   def generate_doc(request):
       if request.method == 'POST':
           doc_type = request.POST.get('doc_type')
           topic = request.POST.get('topic')
           
           # Генерация текста с использованием GPT-3.5-turbo
           response = openai.ChatCompletion.create(
               model="gpt-3.5-turbo",
               messages=[
                   {"role": "system", "content": "Вы ассистент, который помогает писать документы."},
                   {"role": "user", "content": f"Напишите {doc_type} на тему: {topic}."}
               ],
               max_tokens=1500
           )
           generated_text = response['choices'][0]['message']['content']
           
           # Создание DOC-файла
           document = Document()
           document.add_heading(f'{doc_type.capitalize()} на тему "{topic}"', 0)
           document.add_paragraph(generated_text)
           
           # Отправка файла пользователю
           response = HttpResponse(content_type='application/vnd.openxmlformats-officedocument.wordprocessingml.document')
           response['Content-Disposition'] = f'attachment; filename="{doc_type}_{topic}.docx"'
           document.save(response)
           
           return response
       
       return render(request, 'index.html')
   ```

## Шаг 7: Генерация и сохранение DOC-файла

Для создания и сохранения документа используется библиотека `python-docx`. Генерированный документ будет автоматически отправлен пользователю после генерации.

## Шаг 8: Тестирование

1. Запустите сервер:

   ```bash
   python manage.py runserver
   ```

2. Перейдите на `http://localhost:8000/`, введите тип документа и тему, и сгенерируйте файл DOC.

## Шаг 9: Дополнительные улучшения

1. **Кэширование:** Чтобы избежать повторной генерации одних и тех же документов, можно реализовать кэширование.
2. **Аутентификация:** Добавить возможность входа для студентов, чтобы они могли сохранять документы.
3. **Платная подписка:** Можно внедрить модель монетизации для большего количества запросов.
4. **Поддержка нескольких языков:** Добавить поддержку разных языков для генерации текстов.

## Шаг 10: Хостинг

Вы можете опубликовать ваш сайт на следующих платформах:

1. **Heroku** — бесплатная и удобная платформа для хостинга Django-приложений.
2. **PythonAnywhere** — предоставляет бесплатный тариф для небольших проектов.
3. **DigitalOcean** — для более крупных проектов.

---

Теперь сайт готов для генерации курсовых и дипломных работ с использованием актуальных моделей GPT.
