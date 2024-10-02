
# Урок 10: ModelForm и валидация

## Цель урока:
На этом уроке мы изучим ModelForm в Django и научимся автоматически создавать формы на основе моделей. Также рассмотрим, как добавить кастомную валидацию данных формы.

## 1. Введение в ModelForm
ModelForm позволяет автоматически создавать форму на основе модели. Пример:

```python
from django import forms
from .models import Contact

class ContactForm(forms.ModelForm):
    class Meta:
        model = Contact
        fields = ['name', 'email', 'message']
```

Эта форма создаст поля на основе модели Contact.

## 2. Кастомизация и валидация
Вы можете добавить свою валидацию в ModelForm:

```python
class ContactForm(forms.ModelForm):
    class Meta:
        model = Contact
        fields = ['name', 'email', 'message']

    def clean_email(self):
        email = self.cleaned_data.get('email')
        if not email.endswith('@example.com'):
            raise forms.ValidationError('Используйте только email с доменом example.com.')
        return email
```

## 3. Отображение формы
Чтобы использовать ModelForm в представлении:

```python
def contact_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            form.save()  # Сохраняем данные в базу
    else:
        form = ContactForm()
    return render(request, 'contact.html', {'form': form})
```

## 4. Как добавить в проект:
1. Создайте модель Contact в models.py.
2. В forms.py создайте ModelForm с проверкой данных.
3. В представлении сохраните форму в базу данных или обработайте данные.

