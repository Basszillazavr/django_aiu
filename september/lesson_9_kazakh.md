
# 9-урок: Django Forms-ты пайдалану

## Сабақтың мақсаты:
Бұл сабақта біз Django-дағы формалармен жұмыс істеп үйренеміз. Нақтырақ айтсақ, базалық форма құру, деректерді тексеру, және форманы жіберу процесі.

## 1. Формаларды жасау
Django-де формалар python класстарында анықталады. Мысалы:

```python
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)
```

Бұл ContactForm пішіні үш өрісті қамтиды: аты, электрондық пошта және хабарлама.

## 2. Форманы көрсету
Форманы көрсету үшін view-ты пайдалану керек:

```python
from django.shortcuts import render
from .forms import ContactForm

def contact_view(request):
    form = ContactForm()
    return render(request, 'contact.html', {'form': form})
```

## 3. Форманы өңдеу
GET және POST сұраныстарын өңдеу арқылы форманы жіберу процесі жүзеге асады:

```python
def contact_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            # Мұнда деректерді өңдей аласыз
            print(form.cleaned_data)
    else:
        form = ContactForm()
    return render(request, 'contact.html', {'form': form})
```

## 4. Валидация
Django автоматты түрде деректерді тексереді, бірақ өзіңізге керекті валидацияны қосуға болады:

```python
def clean_email(self):
    email = self.cleaned_data.get('email')
    if not "@example.com" in email:
        raise forms.ValidationError("Тек example.com поштасын пайдаланыңыз")
    return email
```

## 5. Жобаға қалай қосу:
1. forms.py файлына пішіндерді қосыңыз.
2. views.py файлында view жасап, шаблонда форманы көрсетіңіз.
3. Деректердің дұрыс екенін тексеріп, оларды базаға сақтаңыз немесе өңдеңіз.
