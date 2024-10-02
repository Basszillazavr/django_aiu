
# Урок 6: Работа с внешними данными и API

## Часть 1: Введение в REST API

**Описание:**
- Основы RESTful архитектуры.
- Создание простого API с использованием Django REST Framework (DRF).
- Настройка сериализаторов и представлений для API.

## Пример реализации:

### Установка Django REST Framework:

```bash
pip install djangorestframework
```

### Добавление DRF в `INSTALLED_APPS` в `settings.py`:

```python
INSTALLED_APPS = [
    'rest_framework',
    # другие приложения
]
```

### Создание сериализатора для модели:

В `serializers.py`:

```python
from rest_framework import serializers
from .models import YourModel

class YourModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = YourModel
        fields = '__all__'
```

### Создание API представлений:

В `views.py`:

```python
from rest_framework import viewsets
from .models import YourModel
from .serializers import YourModelSerializer

class YourModelViewSet(viewsets.ModelViewSet):
    queryset = YourModel.objects.all()
    serializer_class = YourModelSerializer
```

### Настройка маршрутов:

В `urls.py`:

```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import YourModelViewSet

router = DefaultRouter()
router.register(r'yourmodel', YourModelViewSet)

urlpatterns = [
    path('api/', include(router.urls)),
]
```

## Часть 2: Подключение внешних API

**Описание:**
- Взаимодействие с внешними API через библиотеку `requests`.
- Пример получения и обработки данных из внешнего API.

### Установка библиотеки `requests`:

```bash
pip install requests
```

### Пример взаимодействия с внешним API:

В `views.py`:

```python
import requests
from django.shortcuts import render

def external_api_view(request):
    response = requests.get('https://api.example.com/data')
    data = response.json()
    return render(request, 'template.html', {'data': data})
```

