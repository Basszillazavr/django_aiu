
# Добавление CAPTCHA в существующую форму Django

Если у вас уже есть форма и вы хотите добавить CAPTCHA, используйте пакет `django-simple-captcha`. Следуйте этим шагам:

### Шаг 1: Установка `django-simple-captcha`

Запустите следующую команду:

```bash
pip install django-simple-captcha
```

### Шаг 2: Добавьте `captcha` в `INSTALLED_APPS`

Добавьте `'captcha'` в список `INSTALLED_APPS` в настройках вашего проекта (`settings.py`):

```python
INSTALLED_APPS = [
    # Другие установленные приложения
    'captcha',
]
```

### Шаг 3: Добавьте URL CAPTCHA в `urls.py`

Добавьте следующую строку в основной файл `urls.py` вашего проекта:

```python
from django.urls import path, include

urlpatterns = [
    # Другие URL-маршруты
    path('captcha/', include('captcha.urls')),
]
```

### Шаг 4: Добавьте поле CAPTCHA в существующую форму

Обновите вашу существующую форму, добавив CAPTCHA:

```python
from django import forms
from captcha.fields import CaptchaField

class MyExistingForm(forms.Form):
    # Ваши существующие поля формы
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)

    # Добавьте CAPTCHA
    captcha = CaptchaField()  # Новое поле CAPTCHA
```

### Шаг 5: Обновите шаблон

Убедитесь, что ваш шаблон формы отображает поле CAPTCHA:

```html
<form method="post">
    {% csrf_token %}
    {{ form.as_p }} <!-- CAPTCHA будет автоматически включена -->
    <button type="submit">Отправить</button>
</form>
```

### Шаг 6: Проверьте контроллер

Убедитесь, что ваш контроллер корректно обрабатывает форму:

```python
from django.shortcuts import render
from .forms import MyExistingForm

def contact_view(request):
    if request.method == 'POST':
        form = MyExistingForm(request.POST)
        if form.is_valid():
            # Обработка формы
            return render(request, 'success.html')
    else:
        form = MyExistingForm()
    return render(request, 'contact.html', {'form': form})
```

### Шаг 7: Тестирование интеграции

Запустите сервер Django:

```bash
python manage.py runserver
```

Перейдите на страницу с формой и убедитесь, что CAPTCHA корректно работает.
