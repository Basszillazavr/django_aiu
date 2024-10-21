
# Django-да бар формаға CAPTCHA қосу

Егер сізде дайын форма болса және оған CAPTCHA қосқыңыз келсе, `django-simple-captcha` пакетін қолданыңыз. Төмендегі қадамдарды орындаңыз:

### 1-қадам: `django-simple-captcha` орнату

Төмендегі команданы қолданыңыз:

```bash
pip install django-simple-captcha
```

### 2-қадам: `INSTALLED_APPS` тізіміне `captcha` қосу

Жоба баптаулары (`settings.py`) файлына `'captcha'` қосыңыз:

```python
INSTALLED_APPS = [
    # Басқа орнатылған қосымшалар
    'captcha',
]
```

### 3-қадам: CAPTCHA URL-ді `urls.py` файлына қосу

Жобаның негізгі `urls.py` файлына мынаны қосыңыз:

```python
from django.urls import path, include

urlpatterns = [
    # Басқа URL үлгілері
    path('captcha/', include('captcha.urls')),
]
```

### 4-қадам: Бар формаға CAPTCHA өрісін қосу

Сіздің бар формаңызды төмендегідей жаңартыңыз:

```python
from django import forms
from captcha.fields import CaptchaField

class MyExistingForm(forms.Form):
    # Бар форманың өрістері
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)

    # CAPTCHA қосу
    captcha = CaptchaField()  # Жаңа CAPTCHA өрісі
```

### 5-қадам: Шаблонды жаңарту

Бар форма шаблоныңызда CAPTCHA өрісін көрсету:

```html
<form method="post">
    {% csrf_token %}
    {{ form.as_p }} <!-- CAPTCHA өрісі автоматты түрде қосылады -->
    <button type="submit">Жіберу</button>
</form>
```

### 6-қадам: Көріністі тексеру

Бар көріністің форманы дұрыс өңдейтініне көз жеткізіңіз:

```python
from django.shortcuts import render
from .forms import MyExistingForm

def contact_view(request):
    if request.method == 'POST':
        form = MyExistingForm(request.POST)
        if form.is_valid():
            # Форманы өңдеу
            return render(request, 'success.html')
    else:
        form = MyExistingForm()
    return render(request, 'contact.html', {'form': form})
```

### 7-қадам: Интеграцияны тексеру

Django серверін іске қосыңыз:

```bash
python manage.py runserver
```

Форма бетіне кіріп, CAPTCHA-ның дұрыс қосылғанын тексеріңіз.
