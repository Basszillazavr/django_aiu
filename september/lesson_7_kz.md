
# Сабақ 7: Сыртқы деректермен және API-пен жұмыс (Жалғасы)

## 1-бөлім: Сыртқы API-ды пайдалану

**Сипаттамасы:**
- `requests` кітапханасы арқылы сыртқы API-мен өзара әрекеттесу.
- JSON деректерімен жұмыс, үшінші тарап қызметтерінен деректерді жіберу және алу.

## Іске асыру мысалы:

### `requests` кітапханасын орнату:

```bash
pip install requests
```

### Сыртқы API-дан деректерді алу:

`views.py` файлы:

```python
import requests
from django.shortcuts import render

def get_external_data(request):
    url = 'https://jsonplaceholder.typicode.com/posts'
    response = requests.get(url)
    data = response.json()
    return render(request, 'external_data.html', {'data': data})
```

### Маршрутты баптау:

`urls.py` файлы:

```python
from django.urls import path
from .views import get_external_data

urlpatterns = [
    path('external-data/', get_external_data, name='external_data'),
]
```

### Деректерді көрсетуге арналған шаблон (`templates/external_data.html`):

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

## 2-бөлім: Деректерді сыртқы API-ға жіберу

**Сипаттамасы:**
- Деректерді сыртқы API-ға жіберу.
- `requests` пайдалану арқылы JSON деректерін жіберу мысалы.

### Деректерді жіберу мысалы:

`views.py` файлы:

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

### Маршрутты баптау:

`urls.py` файлы:

```python
from .views import send_data_to_external_api

urlpatterns = [
    path('send-data/', send_data_to_external_api, name='send_data'),
]
```

### Деректерді жіберуге арналған шаблон (`templates/send_data.html`):

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

# Сабақ 8: Қолданбаны орналастыру және Django Admin-пен жұмыс

## 1-бөлім: Орналастыруға дайындық

**Сипаттамасы:**
- Django қолданбаларын орналастыру негіздері.
- Өндірістік ортаға жобаны дайындау.
- Gunicorn және Nginx қолдану арқылы орналастыру.

## Іске асыру мысалы:

### Gunicorn орнату:

```bash
pip install gunicorn
```

### Жобаны Gunicorn-пен іске қосу:

```bash
gunicorn myproject.wsgi:application
```

## 2-бөлім: Django Admin баптау

**Сипаттамасы:**
- Администратор панелін баптау және кастомизациялау.
- Модельдермен администратор интерфейсі арқылы жұмыс.

### Админкадағы модельді баптау:

`admin.py` файлы:

```python
from django.contrib import admin
from .models import YourModel

admin.site.register(YourModel)
```

### Модельдің көрсету қасиеттерін баптау:

```python
class YourModelAdmin(admin.ModelAdmin):
    list_display = ('field1', 'field2')
    search_fields = ('field1',)

admin.site.register(YourModel, YourModelAdmin)
```

