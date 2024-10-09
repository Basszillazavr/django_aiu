
# Лекция: Авторизация на Django

Авторизация и аутентификация — это два связанных, но разных процесса. Аутентификация проверяет личность пользователя, а авторизация определяет, что пользователю разрешено делать.

В Django есть встроенная система авторизации, которая позволяет управлять правами пользователей, группами и разрешениями.

## 1. Аутентификация и Авторизация

Django предоставляет систему аутентификации через модуль `django.contrib.auth`, который включает модели, представления и другие инструменты для управления пользователями и их правами.

### 1.1 Установка

Если ваше приложение еще не настроено для работы с аутентификацией, добавьте следующие приложения в `INSTALLED_APPS` вашего проекта:

```python
INSTALLED_APPS = [
    ...
    'django.contrib.auth',
    'django.contrib.contenttypes',
    ...
]
```

### 1.2 Создание пользователей

Создайте суперпользователя, который будет иметь доступ ко всем частям админки:

```bash
python manage.py createsuperuser
```

После этого можно войти в административную панель по адресу `/admin/`.

## 2. Система разрешений

Каждый пользователь в Django может иметь определенные права на выполнение операций. Эти права связаны с моделями и могут быть назначены как индивидуальным пользователям, так и группам.

### 2.1 Пример использования разрешений

Предположим, у нас есть модель `Article`, и мы хотим разрешить определенным пользователям редактировать статьи.

1. Добавьте разрешения к модели `Article`:
   
```python
from django.db import models
from django.contrib.auth.models import User

class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()

    class Meta:
        permissions = [
            ("can_edit_article", "Может редактировать статью"),
        ]
```

2. Настройте проверки в представлениях:

```python
from django.shortcuts import render, get_object_or_404
from django.contrib.auth.decorators import permission_required

@permission_required('app.can_edit_article')
def edit_article(request, article_id):
    article = get_object_or_404(Article, id=article_id)
    # код для редактирования статьи
    return render(request, 'edit_article.html', {'article': article})
```

### 2.2 Группы и их права

Группы в Django позволяют управлять правами нескольких пользователей сразу. Вы можете создать группу, назначить ей разрешения и добавить пользователей в эту группу.

Пример:

```python
from django.contrib.auth.models import Group, Permission

# Создаем группу
editors_group = Group.objects.create(name='Editors')

# Назначаем разрешение
permission = Permission.objects.get(codename='can_edit_article')
editors_group.permissions.add(permission)
```

Затем вы можете добавлять пользователей в эту группу через админку или программно:

```python
user.groups.add(editors_group)
```

## 3. Использование миксинов для авторизации

Django также предоставляет удобные миксины для авторизации пользователей при доступе к представлениям.

Пример использования `LoginRequiredMixin`:

```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import ListView
from .models import Article

class ArticleListView(LoginRequiredMixin, ListView):
    model = Article
    template_name = 'articles_list.html'
```

Таким образом, доступ к странице будет предоставлен только аутентифицированным пользователям.

## 4. Кастомизация системы аутентификации

Вы можете создавать свои классы пользователей и добавлять кастомные поля к модели пользователя, используя `AbstractBaseUser`.

Пример кастомной модели пользователя:

```python
from django.contrib.auth.models import AbstractBaseUser, BaseUserManager

class CustomUserManager(BaseUserManager):
    def create_user(self, email, password=None):
        if not email:
            raise ValueError('У пользователя должен быть email')
        user = self.model(email=self.normalize_email(email))
        user.set_password(password)
        user.save(using=self._db)
        return user

class CustomUser(AbstractBaseUser):
    email = models.EmailField(unique=True)
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)

    objects = CustomUserManager()

    USERNAME_FIELD = 'email'
```

Эта модель заменяет стандартную модель пользователя Django, и теперь аутентификация будет происходить по email.

## Заключение

В данной лекции мы рассмотрели основные возможности системы аутентификации и авторизации в Django. Вы научились работать с разрешениями, группами, а также настраивать доступ к представлениям с помощью миксинов и декораторов.
