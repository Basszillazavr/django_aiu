
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
