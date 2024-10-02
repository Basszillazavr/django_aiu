
# Урок 5: Пользователи и аутентификация

## Часть 1: Создание пользователей и управление ими

**Описание:**
- Создание модели пользователя.
- Встроенная система аутентификации Django.
- Создание и настройка страниц регистрации и авторизации.

## Пример реализации:

### Добавление приложения для пользователей:

Создаем новое приложение `users`:

```bash
python manage.py startapp users
```

### Настройка модели пользователя:

В файле `models.py` приложения `users`:

```python
from django.contrib.auth.models import AbstractUser

class CustomUser(AbstractUser):
    pass
```

### Настройка формы регистрации:

В файле `forms.py`:

```python
from django import forms
from django.contrib.auth.forms import UserCreationForm
from .models import CustomUser

class CustomUserCreationForm(UserCreationForm):
    class Meta:
        model = CustomUser
        fields = ('username', 'email')
```

### Настройка представлений для регистрации:

В файле `views.py`:

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

### Настройка маршрутов:

В файле `urls.py` приложения `users`:

```python
from django.urls import path
from .views import register

urlpatterns = [
    path('register/', register, name='register'),
]
```

### Подключение маршрутов приложения `users`:

В основном файле `urls.py` проекта:

```python
from django.urls import path, include

urlpatterns = [
    path('users/', include('users.urls')),
]
```

## Часть 2: Ограничение доступа

**Описание:**
- Использование декораторов для ограничения доступа к страницам.
- Создание страниц, доступных только авторизованным пользователям.

### Пример использования декораторов:

В файле `views.py`:

```python
from django.contrib.auth.decorators import login_required
from django.shortcuts import render

@login_required
def dashboard(request):
    return render(request, 'dashboard.html')
```

### Настройка маршрута:

В файле `urls.py`:

```python
from django.urls import path
from .views import dashboard

urlpatterns = [
    path('dashboard/', dashboard, name='dashboard'),
]
```

