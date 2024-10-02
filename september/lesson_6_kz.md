
# Сабақ 6: Сыртқы деректермен және API-пен жұмыс

## 1-бөлім: REST API-ге кіріспе

**Сипаттамасы:**
- RESTful архитектурасының негіздері.
- Django REST Framework (DRF) арқылы қарапайым API жасау.
- API үшін сериализаторлар мен көріністерді баптау.

## Іске асыру мысалы:

### Django REST Framework орнату:

```bash
pip install djangorestframework
```

### DRF-ті `INSTALLED_APPS` ішіне қосу, `settings.py`:

```python
INSTALLED_APPS = [
    'rest_framework',
    # басқа қолданбалар
]
```

### Модельге арналған сериализатор жасау:

`serializers.py` ішінде:

```python
from rest_framework import serializers
from .models import YourModel

class YourModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = YourModel
        fields = '__all__'
```

### API көріністерін жасау:

`views.py` ішінде:

```python
from rest_framework import viewsets
from .models import YourModel
from .serializers import YourModelSerializer

class YourModelViewSet(viewsets.ModelViewSet):
    queryset = YourModel.objects.all()
    serializer_class = YourModelSerializer
```

### Маршруттарды баптау:

`urls.py` ішінде:

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

## 2-бөлім: Сыртқы API-мен жұмыс

**Сипаттамасы:**
- `requests` кітапханасы арқылы сыртқы API-мен әрекеттесу.
- Сыртқы API-дан деректерді алу және өңдеу мысалы.

### `requests` кітапханасын орнату:

```bash
pip install requests
```

### Сыртқы API-пен әрекеттесу мысалы:

`views.py` ішінде:

```python
import requests
from django.shortcuts import render

def external_api_view(request):
    response = requests.get('https://api.example.com/data')
    data = response.json()
    return render(request, 'template.html', {'data': data})
```
