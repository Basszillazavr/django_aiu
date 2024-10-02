
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
