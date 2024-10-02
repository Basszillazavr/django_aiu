
# Сабақ 5: Пайдаланушылар және аутентификация

## 1-бөлім: Пайдаланушыларды жасау және басқару

**Сипаттамасы:**
- Пайдаланушы моделін жасау.
- Django-ның кіріктірілген аутентификация жүйесі.
- Тіркеу және авторизация беттерін жасау және баптау.

## Іске асыру мысалы:

### Пайдаланушыларға арналған қолданбаны қосу:

Жаңа `users` қолданбасын жасаймыз:

```bash
python manage.py startapp users
```

### Пайдаланушы моделін баптау:

`users` қолданбасының `models.py` файлы:

```python
from django.contrib.auth.models import AbstractUser

class CustomUser(AbstractUser):
    pass
```

### Тіркеу формасын баптау:

`forms.py` файлы:

```python
from django import forms
from django.contrib.auth.forms import UserCreationForm
from .models import CustomUser

class CustomUserCreationForm(UserCreationForm):
    class Meta:
        model = CustomUser
        fields = ('username', 'email')
```

### Тіркеу көріністерін баптау:

`views.py` файлы:

```python
from django.shortcuts import render, redirect
from .forms import CustomUserCreationForm

def register(request):
    if request.method == 'POST':
        form = CustomUserCreationForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('login')
    else:
        form = CustomUserCreationForm()
    return render(request, 'registration/register.html', {'form': form})
```

### Маршруттарды баптау:

`users` қолданбасының `urls.py` файлы:

```python
from django.urls import path
from .views import register

urlpatterns = [
    path('register/', register, name='register'),
]
```

### Пайдаланушылар қолданбасының маршруттарын қосу:

Жобаның негізгі `urls.py` файлы:

```python
from django.urls import path, include

urlpatterns = [
    path('users/', include('users.urls')),
]
```

## 2-бөлім: Қатынауды шектеу

**Сипаттамасы:**
- Беттерге қатынауды шектеу үшін декораторларды пайдалану.
- Тек тіркелген пайдаланушыларға қолжетімді беттер жасау.

### Декораторларды пайдалану мысалы:

`views.py` файлы:

```python
from django.contrib.auth.decorators import login_required
from django.shortcuts import render

@login_required
def dashboard(request):
    return render(request, 'dashboard.html')
```

### Маршрутты баптау:

`urls.py` файлы:

```python
from django.urls import path
from .views import dashboard

urlpatterns = [
    path('dashboard/', dashboard, name='dashboard'),
]
```

