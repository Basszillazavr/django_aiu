
# 10-урок: ModelForm және валидация

## Сабақтың мақсаты:
Бұл сабақта біз Django-дағы ModelForm және олардың көмегімен модель негізінде формаларды автоматты түрде қалай құруға болатынын үйренеміз. Сонымен қатар, формадағы деректерді тексеру әдістерін қарастырамыз.

## 1. ModelForm енгізу
ModelForm — Django-да модель негізінде формаларды құрудың жеңіл жолы. Мысал:

```python
from django import forms
from .models import Contact

class ContactForm(forms.ModelForm):
    class Meta:
        model = Contact
        fields = ['name', 'email', 'message']
```

Бұл пішін Contact моделі негізінде автоматты түрде өрістерді жасайды.

## 2. Кастомизация және қосымша валидация
ModelForm көмегімен қосымша валидация қосуға болады:

```python
class ContactForm(forms.ModelForm):
    class Meta:
        model = Contact
        fields = ['name', 'email', 'message']

    def clean_email(self):
        email = self.cleaned_data.get('email')
        if not email.endswith('@example.com'):
            raise forms.ValidationError('Тек example.com поштасын пайдаланыңыз.')
        return email
```

## 3. Форманы көрсету
ModelForm-ты view ішінде қолдану үшін:

```python
def contact_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            form.save()  # Деректерді базаға сақтаймыз
    else:
        form = ContactForm()
    return render(request, 'contact.html', {'form': form})
```

## 4. Жобаға қалай қосу:
1. models.py файлында Contact моделін жасаңыз.
2. forms.py файлында ModelForm жасап, деректерді тексеруді қосыңыз.
3. View ішінде форманы базаға сақтаңыз немесе деректерді өңдеңіз.

