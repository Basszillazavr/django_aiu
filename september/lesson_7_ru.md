
# Урок 7: Работа с внешними данными и API (Продолжение)

## Часть 1: Использование внешних API

**Описание:**
- Взаимодействие с внешними API с использованием библиотеки `requests`.
- Работа с JSON данными, отправка и получение данных от сторонних сервисов.

## Пример реализации:

### Установка библиотеки `requests`:

```bash
pip install requests
```

### Получение данных из внешнего API:

В файле `views.py`:

```python
import requests
from django.shortcuts import render

def get_external_data(request):
    url = 'https://jsonplaceholder.typicode.com/posts'
    response = requests.get(url)
    data = response.json()
    return render(request, 'external_data.html', {'data': data})
```

### Настройка маршрута:

В файле `urls.py`:

```python
from django.urls import path
from .views import get_external_data

urlpatterns = [
    path('external-data/', get_external_data, name='external_data'),
]
```

### Шаблон для отображения данных (`templates/external_data.html`):

```html
<!DOCTYPE html>
<html>
<head>
    <title>External Data</title>
</head>
<body>
    <h1>External Data</h1>
    <ul>
    {% for item in data %}
        <li>{{ item.title }}</li>
    {% endfor %}
    </ul>
</body>
</html>
```

## Часть 2: Отправка данных на внешний API

**Описание:**
- Отправка данных на внешний API.
- Пример отправки JSON данных с использованием `requests`.

### Пример отправки данных:

В файле `views.py`:

```python
def send_data_to_external_api(request):
    url = 'https://jsonplaceholder.typicode.com/posts'
    payload = {
        'title': 'New Post',
        'body': 'This is a new post.',
        'userId': 1
    }
    response = requests.post(url, json=payload)
    return render(request, 'send_data.html', {'response': response.status_code})
```

### Настройка маршрута:

В файле `urls.py`:

```python
from .views import send_data_to_external_api

urlpatterns = [
    path('send-data/', send_data_to_external_api, name='send_data'),
]
```

### Шаблон для отправки данных (`templates/send_data.html`):

```html
<!DOCTYPE html>
<html>
<head>
    <title>Send Data</title>
</head>
<body>
    <h1>Send Data to External API</h1>
    <p>Response Status Code: {{ response }}</p>
</body>
</html>
```

# Урок 8: Развертывание приложения и работа с Django Admin

## Часть 1: Подготовка к развертыванию

**Описание:**
- Основы развертывания Django-приложений.
- Подготовка проекта для продакшн-окружения.
- Использование Gunicorn и Nginx для развертывания.

## Пример реализации:

### Установка Gunicorn:

```bash
pip install gunicorn
```

### Запуск проекта с Gunicorn:

```bash
gunicorn myproject.wsgi:application
```

## Часть 2: Настройка Django Admin

**Описание:**
- Настройка и кастомизация панели администратора.
- Работа с моделями через интерфейс администратора.

### Настройка модели в админке:

В файле `admin.py`:

```python
from django.contrib import admin
from .models import YourModel

admin.site.register(YourModel)
```

### Настройка свойств отображения модели:

```python
class YourModelAdmin(admin.ModelAdmin):
    list_display = ('field1', 'field2')
    search_fields = ('field1',)

admin.site.register(YourModel, YourModelAdmin)
```

