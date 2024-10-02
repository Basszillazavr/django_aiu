
# Урок 9: Работа с формами в Django

## Цель урока:
На этом уроке мы научимся работать с формами в Django: создавать формы, обрабатывать отправку данных, а также проверять их корректность.

## 1. Создание формы
Формы в Django создаются через Python-классы. Пример:

```python
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)
```

Эта форма включает три поля: имя, email и сообщение.

## 2. Отображение формы
Для отображения формы используем view:

```python
from django.shortcuts import render
from .forms import ContactForm

def contact_view(request):
    form = ContactForm()
    return render(request, 'contact.html', {'form': form})
```

## 3. Обработка формы
Чтобы обработать отправку формы, используем методы GET и POST:

```python
def contact_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            # Здесь можно обработать данные
            print(form.cleaned_data)
    else:
        form = ContactForm()
    return render(request, 'contact.html', {'form': form})
```

## 4. Валидация данных
Django автоматически проверяет данные, но можно добавить свою валидацию:

```python
def clean_email(self):
    email = self.cleaned_data.get('email')
    if not "@example.com" in email:
        raise forms.ValidationError("Используйте только email на домене example.com")
    return email
```

## 5. Как добавить в проект:
1. Добавьте формы в файл forms.py.
2. В views.py создайте представление и выведите форму в шаблоне.
3. Проверьте корректность данных, обработайте их или сохраните в базу данных.
