
# Урок: Генерация DOCX и PDF файлов в Django

Этот урок посвящен созданию функциональности для генерации DOCX и PDF файлов с использованием Django.

---

## 1. Установка необходимых библиотек
Для работы с документами Word и PDF установите библиотеки:
```bash
pip install python-docx
pip install weasyprint
```

---

## 2. Генерация DOCX файла

### Пример представления
Вот пример функции представления, которая создает документ Word (DOCX) с заголовками, таблицами и текстом:
```python
from django.http import HttpResponse
from docx import Document

def generate_syllabus(request):
    # Создаем новый документ
    doc = Document()
    
    # Добавляем заголовки и текст
    doc.add_heading('АСТАНА ХАЛЫҚАРАЛЫҚ УНИВЕРСИТЕТІ', level=1)
    doc.add_heading('МЕЖДУНАРОДНЫЙ УНИВЕРСИТЕТ АСТАНА', level=1)
    doc.add_heading('Программа дисциплины (Syllabus)', level=2)
    
    doc.add_paragraph('по дисциплине DWF 2212 Веб-фреймворк Django')
    doc.add_paragraph('Код и полное наименование дисциплины по рабочему учебному плану.')
    
    # Добавляем таблицы
    table = doc.add_table(rows=3, cols=3)
    table.style = 'Table Grid'
    table.cell(0, 0).text = 'Код дисциплины'
    table.cell(0, 1).text = 'Наименование дисциплины'
    table.cell(0, 2).text = 'Кредиты'
    
    table.cell(1, 0).text = 'DWF 2212'
    table.cell(1, 1).text = 'Веб-фреймворк Django'
    table.cell(1, 2).text = '5'

    # Сохраняем в HTTP-ответ
    response = HttpResponse(content_type='application/vnd.openxmlformats-officedocument.wordprocessingml.document')
    response['Content-Disposition'] = 'attachment; filename="syllabus.docx"'
    doc.save(response)
    return response
```

---

## 3. Генерация PDF файла

### Пример представления
Для создания PDF используйте библиотеку WeasyPrint. В качестве шаблона используем HTML.

```python
from django.template.loader import render_to_string
from django.http import HttpResponse
from weasyprint import HTML

def generate_pdf(request):
    # Данные для шаблона
    context = {
        'university': 'АСТАНА ХАЛЫҚАРАЛЫҚ УНИВЕРСИТЕТІ',
        'discipline_code': 'DWF 2212',
        'discipline_name': 'Веб-фреймворк Django',
        'credits': 5,
        'instructor': 'Ержанов А.Т.'
    }

    # Рендер HTML шаблона
    html_string = render_to_string('syllabus_template.html', context)

    # Генерация PDF
    pdf_file = HTML(string=html_string).write_pdf()

    # Возврат PDF в HTTP-ответе
    response = HttpResponse(pdf_file, content_type='application/pdf')
    response['Content-Disposition'] = 'attachment; filename="syllabus.pdf"'
    return response
```

### Пример HTML-шаблона для PDF
Шаблон `syllabus_template.html`:
```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Силабус</title>
</head>
<body>
    <h1>{{ university }}</h1>
    <h2>Программа дисциплины (Syllabus)</h2>
    <p>Код дисциплины: {{ discipline_code }}</p>
    <p>Наименование дисциплины: {{ discipline_name }}</p>
    <p>Кредиты: {{ credits }}</p>
    <p>Преподаватель: {{ instructor }}</p>
</body>
</html>
```

---

## 4. Настройка маршрутов
Добавьте маршруты в `urls.py`:
```python
from django.urls import path
from .views import generate_syllabus, generate_pdf

urlpatterns = [
    path('generate-docx/', generate_syllabus, name='generate_docx'),
    path('generate-pdf/', generate_pdf, name='generate_pdf'),
]
```

---

## 5. Расширенные возможности
### Добавление изображения в DOCX
```python
from docx.shared import Inches
doc.add_picture('path_to_image.png', width=Inches(1.25))
```

### Поддержка сложных таблиц
Вы можете добавлять строки и изменять ячейки таблицы динамически.

---

## 6. Результат
- Вы можете генерировать файлы DOCX и PDF по запросу.
- Эти файлы могут содержать текст, таблицы, изображения и другие элементы.

---

Этот урок позволяет создать гибкую функциональность для генерации сложных файлов в Django.
