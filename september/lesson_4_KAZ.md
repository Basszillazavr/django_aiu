
# Сабақ 4: Django әкімшілік панелі және формалармен жұмыс

**Сабақтың мақсаты:**  
Студенттерді Django-ның әкімшілік панелімен жұмыс істеуді және мәліметтермен өзара әрекеттесу үшін формалар жасауды үйрету. Сабақтан кейін студенттер өз модельдерін әкімшілік панель арқылы басқарып, мәліметтерді енгізу және өңдеу үшін формалар жасай алады.

---

### 1. Django әкімшілік панелі

Django әкімшілік панелі — модельдер мен мәліметтерді веб-интерфейс арқылы басқарудың қуатты құралы. Әкімшілік панель модель жасалған кезде автоматты түрде құрылады және оны `admin.py` файлында тіркеу керек.

#### Негізгі тұстары:
- Әкімшілік панель код жазуды қажет етпейтін мәліметтермен жұмыс істеудің ыңғайлы интерфейсін ұсынады.
- Әкімшілік панельге кіру үшін суперпайдаланушы жасау қажет.
- Модельдер `admin.py` файлында тіркелуі керек.

#### Әкімшілік панельді баптау қадамдары:

1. **Суперпайдаланушы жасау**:
   ```bash
   python manage.py createsuperuser
   ```

2. **Модельді әкімшілік панельде тіркеу**:
   Қосымшадағы `admin.py` файлын ашып, келесі кодты қосыңыз:
   ```python
   from django.contrib import admin
   from .models import Post

   admin.site.register(Post)
   ```

3. **Серверді іске қосып, әкімшілік панельге кіріңіз**:
   [http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/) мекенжайын ашып, суперпайдаланушы деректерімен кіріңіз.

Енді сіз модельдермен веб-интерфейс арқылы жұмыс жасай аласыз.

---

### 2. Django Формалары

Django формалары пайдаланушылардан мәліметтерді алу және оларды өңдеу үшін қолданылады. Django модельдермен (ModelForms) және қарапайым формалармен (Forms) жұмыс істеу үшін дайын формаларды ұсынады.

#### Негізгі тұстары:
- **ModelForm** модель негізінде форманы автоматты түрде жасауға мүмкіндік береді.
- Формалар жазбаларды жасау, өңдеу және жою үшін қолданылады.
- Форма деректерді POST сұранысы арқылы көрсетілімге береді.

#### ModelForm қолдану мысалы:

1. **Модель негізінде форма жасау**:
   `forms.py` файлын ашып, келесі форманы жасаңыз:
   ```python
   from django import forms
   from .models import Post

   class PostForm(forms.ModelForm):
       class Meta:
           model = Post
           fields = ['title', 'content']
   ```

2. **Форманы View-де қолдану**:
   `views.py` файлын ашып, жаңа пост жасау үшін көрсетілім жасаңыз:
   ```python
   from django.shortcuts import render, redirect
   from .forms import PostForm

   def create_post(request):
       if request.method == "POST":
           form = PostForm(request.POST)
           if form.is_valid():
               form.save()
               return redirect('home')
       else:
           form = PostForm()
       return render(request, 'create_post.html', {'form': form})
   ```

3. **Форманы көрсету үшін шаблон жасау**:
   `templates` қалтасында `create_post.html` файлын жасаңыз:
   ```html
   <h2>Жаңа пост жасау</h2>
   <form method="POST">
       {% csrf_token %}
       {{ form.as_p }}
       <button type="submit">Жасау</button>
   </form>
   ```

Енді пайдаланушылар жаңа посттарды форма арқылы жасай алады.

---

### Практикалық тапсырма

1. Пікірлерді сақтау үшін жаңа модель жасаңыз. Пікірлерді посттармен "бірге-көп" қатынас арқылы байланыстырыңыз.
2. Пікірлер моделін әкімшілік панельге қосыңыз.
3. Пікірлерді қосу үшін форма жасаңыз. ModelForm пайдаланыңыз.
4. Әрбір посттың астына пікірлерді көрсетіңіз.
