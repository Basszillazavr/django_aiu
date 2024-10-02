
# План по созданию сайта для генерации курсовых и дипломных работ с использованием GPT

Этот план разбит по шагам для создания веб-сайта, который будет генерировать академические документы на основе данных, введенных пользователями (тип документа и тема). Для генерации текста будет использоваться GPT, а для создания документов — формат DOC.

## Шаг 1: Определение функциональности
1. Пользователь вводит тип документа (курсовая, дипломная, эссе и т.д.) и название темы.
2. GPT генерирует текст на основе указанных параметров.
3. Сайт возвращает сгенерированный текст в формате DOC.

## Шаг 2: Выбор технологии
1. **Backend:** Django — для обработки запросов, интеграции с GPT, и генерации файлов.
2. **Frontend:** HTML/CSS/JavaScript для пользовательского интерфейса.
3. **API для GPT:** OpenAI API (или альтернативы).
4. **Генерация DOC-файлов:** Используем библиотеку `python-docx` для создания документов в формате DOC.

## Шаг 3: Структура проекта

1. **Frontend:**
   - Форма для ввода типа документа и темы.
   - Кнопка отправки данных на сервер.
   - Поле для отображения ссылки на сгенерированный документ.

2. **Backend:**
   - Обработка запроса с формы.
   - Интеграция с GPT для генерации текста.
   - Генерация DOC-файла.
   - Отправка файла пользователю.

## Шаг 4: Создание Django-проекта

1. Создать новый проект Django:

   ```bash
   django-admin startproject gpt_courses
   cd gpt_courses
   python manage.py startapp generator
   ```

2. Настроить базовые маршруты для генерации документов:
   
   - Добавить приложение в `INSTALLED_APPS`.
   - Создать маршруты для отображения формы и получения сгенерированного файла.

## Шаг 5: Настройка формы для ввода данных

1. Создать HTML-форму для ввода типа документа и темы:

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

2. Настроить соответствующие URL-маршруты:

   ```python
   # urls.py
   from django.urls import path
   from .views import generate_doc

   urlpatterns = [
       path('', generate_doc, name='generate_doc'),
   ]
   ```

## Шаг 6: Интеграция с GPT для генерации текста

1. Подключиться к GPT через API OpenAI:

   ```python
   # views.py
   import openai
   from django.shortcuts import render
   from django.http import HttpResponse
   from docx import Document

   openai.api_key = 'ВАШ_API_КЛЮЧ'

   def generate_doc(request):
       if request.method == 'POST':
           doc_type = request.POST.get('doc_type')
           topic = request.POST.get('topic')
           
           # Генерация текста с GPT
           response = openai.Completion.create(
               engine="text-davinci-003",
               prompt=f"Напишите {doc_type} на тему {topic}",
               max_tokens=1500
           )
           generated_text = response['choices'][0]['text']
           
           # Создание документа
           document = Document()
           document.add_heading(f'{doc_type.capitalize()} на тему "{topic}"', 0)
           document.add_paragraph(generated_text)
           
           # Сохранение файла
           response = HttpResponse(content_type='application/vnd.openxmlformats-officedocument.wordprocessingml.document')
           response['Content-Disposition'] = f'attachment; filename="{doc_type}_{topic}.docx"'
           document.save(response)
           
           return response
       
       return render(request, 'index.html')
   ```

## Шаг 7: Генерация и сохранение DOC-файла

Для генерации и сохранения файла используется библиотека `python-docx`. Этот файл можно будет скачать сразу после генерации на сервере.

## Шаг 8: Тестирование

1. Запустите проект:

   ```bash
   python manage.py runserver
   ```

2. Перейдите по адресу `http://localhost:8000/`, введите тип документа и тему, и сгенерируйте текст, который будет возвращён в виде файла `.docx`.

## Шаг 9: Дополнительные улучшения

1. **Кэширование:** Чтобы избежать повторной генерации одних и тех же документов, можно реализовать систему кэширования.
2. **Аутентификация:** Добавить систему пользователей, где студенты смогут сохранять свои документы и возвращаться к ним.
3. **Платная подписка:** Внедрить систему оплаты для доступа к большему количеству запросов или более сложных тем.
4. **Перевод текста:** Добавить поддержку генерации на нескольких языках.

## Шаг 10: Хостинг

Для публикации сайта можно использовать платформы вроде:
- **Heroku** (бесплатная и простая настройка для Django-проектов).
- **PythonAnywhere** (предоставляет бесплатный тариф для размещения небольших Django-приложений).
- **DigitalOcean** для более масштабных проектов.

### Заключение

Этот сайт позволит студентам генерировать курсовые и дипломные работы по различным темам, используя мощные возможности GPT.
