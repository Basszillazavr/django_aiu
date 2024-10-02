
# Урок 1: Подключение форм в Django и запись данных в базу

### Шаг 1: Создание формы с использованием `forms.Form` или `forms.ModelForm`

Для создания формы в Django, можно использовать два варианта:

1. **`forms.Form`** — это для создания простой формы, где нужно вручную указать поля.
2. **`forms.ModelForm`** — это более удобный способ создания формы, который автоматически создает поля на основе модели.

#### Пример с использованием `forms.ModelForm`:

```python
# forms.py
from django import forms
from .models import Post

class PostForm(forms.ModelForm):
    class Meta:
        model = Post  # Указываем модель, с которой работаем
        fields = ['title', 'content']  # Поля, которые будут отображаться в форме
```

### Шаг 2: Создание представления для обработки формы

Необходимо создать представление, которое будет обрабатывать отображение формы и сохранение данных в базу.

#### Пример функции-представления:

```python
# views.py
from django.shortcuts import render, redirect
from .forms import PostForm

def create_post(request):
    if request.method == 'POST':  # Если запрос POST, то обрабатываем данные формы
        form = PostForm(request.POST)
        if form.is_valid():
            form.save()  # Сохраняем данные формы в базу данных
            return redirect('post_list')  # Перенаправляем на список постов
    else:
        form = PostForm()  # Если GET-запрос, просто отображаем форму
    
    return render(request, 'create_post.html', {'form': form})
```

### Шаг 3: Шаблон для отображения формы

Теперь нужно создать HTML-шаблон для отображения формы.

#### Пример шаблона `create_post.html`:

```html
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Сохранить пост</button>
</form>
```

### Шаг 4: Миграции базы данных

Перед тем, как данные смогут записываться в базу, нужно создать модель и миграции.

#### Пример модели `Post`:

```python
# models.py
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()

    def __str__(self):
        return self.title
```

Теперь нужно создать и выполнить миграции:

```bash
python manage.py makemigrations
python manage.py migrate
```

### Шаг 5: Отображение данных

Чтобы отобразить список постов, нужно добавить еще одно представление и шаблон.

#### Пример представления для отображения списка постов:

```python
# views.py
from django.shortcuts import render
from .models import Post

def post_list(request):
    posts = Post.objects.all()  # Получаем все записи из базы данных
    return render(request, 'post_list.html', {'posts': posts})
```

#### Пример шаблона `post_list.html`:

```html
<ul>
    {% for post in posts %}
        <li>{{ post.title }}: {{ post.content }}</li>
    {% endfor %}
</ul>
```

---

# Урок 2: Использование внешнего API для записи данных и отображения в Django

### Шаг 1: Использование библиотеки `requests` для получения данных

Для получения данных с внешнего API, можно использовать библиотеку `requests`.

#### Пример получения данных с внешнего API:

```python
# views.py
import requests
from django.shortcuts import render
from .models import Post

def fetch_posts_from_api(request):
    response = requests.get('https://jsonplaceholder.typicode.com/posts')
    if response.status_code == 200:
        posts_data = response.json()  # Получаем данные в формате JSON

        # Сохраняем данные в базе данных
        for post in posts_data:
            Post.objects.create(title=post['title'], content=post['body'])

    return render(request, 'post_list.html', {'posts': Post.objects.all()})
```

### Шаг 2: Обработка полученных данных

После получения данных с API, мы сохраняем их в базу данных с помощью метода `create`. После этого данные можно отобразить, как в первом уроке.

### Шаг 3: Настройка URL и шаблона

Необходимо добавить URL для нового представления:

```python
# urls.py
from django.urls import path
from .views import fetch_posts_from_api

urlpatterns = [
    path('fetch-posts/', fetch_posts_from_api, name='fetch_posts'),
]
```

Шаблон для отображения можно использовать тот же, что и в первом уроке.

Теперь данные будут получены с внешнего API и сохранены в базу данных, а затем отображены в шаблоне.
