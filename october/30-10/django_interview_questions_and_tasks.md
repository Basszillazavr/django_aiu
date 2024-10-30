
# Django Interview Questions and Technical Tasks

## Теоретические вопросы по Django

1. **Что такое Django и какие основные особенности этого фреймворка?**
   - **Ответ**: Django — это высокоуровневый веб-фреймворк для Python, предназначенный для быстрой разработки веб-приложений. Его основные особенности включают встроенный ORM, маршрутизацию URL, систему шаблонов, аутентификацию, поддержку административного интерфейса и масштабируемость. Django следует принципу "не повторяйся" (DRY).

2. **Как работает система маршрутизации URL в Django?**
   - **Ответ**: Маршрутизация в Django определяется в файлах `urls.py`, где с помощью функции `path()` или `re_path()` связываются URL-шаблоны с представлениями (views). Основной файл `urls.py` проекта может включать ссылки на файлы `urls.py` приложений, обеспечивая модульную маршрутизацию.

3. **Какие типы представлений существуют в Django, и как они работают?**
   - **Ответ**: Django поддерживает два типа представлений: функциональные (`function-based views`) и классовые (`class-based views`). Функциональные представления — это простые функции, обрабатывающие запросы и возвращающие ответы. Классовые представления предоставляют более структурированный подход, организуя код в классах, позволяя использовать наследование и методы, такие как `get()` и `post()`.

4. **Как работают модели и ORM в Django?**
   - **Ответ**: Django ORM (Object-Relational Mapping) предоставляет способ работы с базой данных через Python-классы, называемые моделями. Каждая модель представляет таблицу в базе данных, а ее атрибуты — столбцы. ORM позволяет выполнять SQL-запросы через методы, такие как `.filter()`, `.all()`, `.get()`, `.save()`, и `.delete()`.

5. **Что такое миграции в Django и как они работают?**
   - **Ответ**: Миграции — это механизм, позволяющий синхронизировать структуру базы данных с моделями Django. Миграции создаются командой `makemigrations`, а применяются с помощью `migrate`. Они фиксируют изменения в моделях и обновляют базу данных, не требуя ручного написания SQL.

6. **Как работает система шаблонов в Django?**
   - **Ответ**: Django использует систему шаблонов, позволяющую рендерить HTML с использованием переменных, циклов и условий. Шаблоны поддерживают наследование (используя `{% extends %}`) и включение (с помощью `{% include %}`). Django также предоставляет фильтры и теги для работы с данными в шаблонах.

7. **Что такое Django Admin и как его использовать?**
   - **Ответ**: Django Admin — это встроенный административный интерфейс, позволяющий управлять данными, моделями и пользователями без необходимости разрабатывать панель администратора с нуля. Интерфейс доступен по адресу `/admin`, и модели можно зарегистрировать для отображения в админке через `admin.py`.

8. **Как работает система аутентификации в Django?**
   - **Ответ**: Django имеет встроенную систему аутентификации, которая включает управление пользователями, группами, разрешениями и аутентификацией. Она предоставляет функции для регистрации, входа, выхода и управления доступом с использованием декораторов, таких как `@login_required`.

9. **Какие методы защиты от CSRF и XSS предоставляет Django?**
   - **Ответ**: Django автоматически защищает от XSS, экранируя HTML-данные в шаблонах. Для защиты от CSRF используется CSRF-токен, который включается в формы и проверяется при отправке POST-запросов. Токен предотвращает подделку межсайтовых запросов.

10. **Как настроить и использовать middleware в Django?**
    - **Ответ**: Middleware — это слои, обрабатывающие запросы и ответы, проходящие через приложение. Middleware настраивается в `settings.py` в списке `MIDDLEWARE`. Django предоставляет встроенные middleware, такие как `AuthenticationMiddleware`, а также поддерживает создание пользовательских.

---

## Технические задачи по Django

### Легкие задачи

1. **Задача:** Создать простое Django-приложение с моделью `Book`, которая имеет поля `title` и `author`, и зарегистрировать эту модель в админке Django.
   - **Решение**:
     ```python
     # models.py
     from django.db import models

     class Book(models.Model):
         title = models.CharField(max_length=100)
         author = models.CharField(max_length=50)

     # admin.py
     from django.contrib import admin
     from .models import Book

     admin.site.register(Book)
     ```

2. **Задача:** Создать представление, которое возвращает JSON-ответ с приветственным сообщением.
   - **Решение**:
     ```python
     # views.py
     from django.http import JsonResponse

     def hello_json(request):
         return JsonResponse({'message': 'Hello, Django!'})
     
     # urls.py
     from django.urls import path
     from . import views

     urlpatterns = [
         path('hello_json/', views.hello_json, name='hello_json'),
     ]
     ```

---

### Средние задачи

1. **Задача:** Реализовать форму для создания нового объекта `Book` с полями `title` и `author` и сохранить его в базе данных через представление.
   - **Решение**:
     ```python
     # forms.py
     from django import forms
     from .models import Book

     class BookForm(forms.ModelForm):
         class Meta:
             model = Book
             fields = ['title', 'author']

     # views.py
     from django.shortcuts import render, redirect
     from .forms import BookForm

     def create_book(request):
         if request.method == 'POST':
             form = BookForm(request.POST)
             if form.is_valid():
                 form.save()
                 return redirect('book_list')
         else:
             form = BookForm()
         return render(request, 'create_book.html', {'form': form})

     # urls.py
     urlpatterns = [
         path('create_book/', views.create_book, name='create_book'),
     ]
     ```

2. **Задача:** Создать простой API для получения списка книг и добавления новой книги с помощью Django REST framework.
   - **Решение**:
     ```python
     # serializers.py
     from rest_framework import serializers
     from .models import Book

     class BookSerializer(serializers.ModelSerializer):
         class Meta:
             model = Book
             fields = '__all__'

     # views.py
     from rest_framework import viewsets
     from .models import Book
     from .serializers import BookSerializer

     class BookViewSet(viewsets.ModelViewSet):
         queryset = Book.objects.all()
         serializer_class = BookSerializer

     # urls.py
     from rest_framework.routers import DefaultRouter
     from .views import BookViewSet

     router = DefaultRouter()
     router.register(r'books', BookViewSet)

     urlpatterns = router.urls
     ```

---

### Сложные задачи

1. **Задача:** Создать CRUD-приложение для управления задачами с использованием Django и Django REST framework, включая поддержку аутентификации с помощью токенов JWT.
   - **Решение**:
     - Настроить модели, представления и маршруты для CRUD-операций.
     - Подключить библиотеку `djangorestframework-simplejwt` для аутентификации.

     ```python
     # models.py
     from django.db import models

     class Task(models.Model):
         title = models.CharField(max_length=100)
         description = models.TextField()
         completed = models.BooleanField(default=False)

     # serializers.py
     from rest_framework import serializers
     from .models import Task

     class TaskSerializer(serializers.ModelSerializer):
         class Meta:
             model = Task
             fields = '__all__'

     # views.py
     from rest_framework import viewsets
     from rest_framework.permissions import IsAuthenticated
     from .models import Task
     from .serializers import TaskSerializer

     class TaskViewSet(viewsets.ModelViewSet):
         queryset = Task.objects.all()
         serializer_class = TaskSerializer
         permission_classes = [IsAuthenticated]

     # urls.py
     from rest_framework.routers import DefaultRouter
     from .views import TaskViewSet

     router = DefaultRouter()
     router.register(r'tasks', TaskViewSet)

     urlpatterns = router.urls

     # settings.py (Add JWT authentication)
     REST_FRAMEWORK = {
         'DEFAULT_AUTHENTICATION_CLASSES': (
             'rest_framework_simplejwt.authentication.JWTAuthentication',
         ),
     }
     ```

2. **Задача:** Создать модель с полем изображения и настроить Django для обработки и отображения загруженных изображений.
   - **Решение**:
     ```python
     # models.py
     from django.db import models

     class Profile(models.Model):
         name = models.CharField(max_length=50)
         photo = models.ImageField(upload_to='photos/')

     # settings.py
     MEDIA_URL = '/media/'
     MEDIA_ROOT = BASE_DIR / 'media/'

     # urls.py
     from django.conf import settings
     from django.conf.urls.static import static

     urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
     ```

---

---

## Дополнительные задачи по популярным алгоритмам

### 1. Алгоритмы сортировки

1. **Задача:** Реализуйте сортировку вставками (Insertion Sort) для списка чисел.
   - **Решение**:
     ```python
     def insertion_sort(arr):
         for i in range(1, len(arr)):
             key = arr[i]
             j = i - 1
             while j >= 0 and key < arr[j]:
                 arr[j + 1] = arr[j]
                 j -= 1
             arr[j + 1] = key
         return arr
     ```

2. **Задача:** Напишите функцию для сортировки массива слиянием (Merge Sort).
   - **Решение**:
     ```python
     def merge_sort(arr):
         if len(arr) > 1:
             mid = len(arr) // 2
             left = arr[:mid]
             right = arr[mid:]

             merge_sort(left)
             merge_sort(right)

             i = j = k = 0
             while i < len(left) and j < len(right):
                 if left[i] < right[j]:
                     arr[k] = left[i]
                     i += 1
                 else:
                     arr[k] = right[j]
                     j += 1
                 k += 1

             while i < len(left):
                 arr[k] = left[i]
                 i += 1
                 k += 1

             while j < len(right):
                 arr[k] = right[j]
                 j += 1
                 k += 1
         return arr
     ```

3. **Задача:** Реализуйте алгоритм быстрой сортировки (Quick Sort).
   - **Решение**:
     ```python
     def quick_sort(arr):
         if len(arr) <= 1:
             return arr
         pivot = arr[len(arr) // 2]
         left = [x for x in arr if x < pivot]
         middle = [x for x in arr if x == pivot]
         right = [x for x in arr if x > pivot]
         return quick_sort(left) + middle + quick_sort(right)
     ```

### 2. Алгоритмы поиска

1. **Задача:** Реализуйте двоичный поиск (Binary Search) для отсортированного списка.
   - **Решение**:
     ```python
     def binary_search(arr, target):
         left, right = 0, len(arr) - 1
         while left <= right:
             mid = (left + right) // 2
             if arr[mid] == target:
                 return mid
             elif arr[mid] < target:
                 left = mid + 1
             else:
                 right = mid - 1
         return -1
     ```

2. **Задача:** Найдите первое и последнее вхождение заданного элемента в отсортированном списке.
   - **Решение**:
     ```python
     def find_first_and_last(arr, target):
         def find_index(arr, target, find_first):
             left, right, result = 0, len(arr) - 1, -1
             while left <= right:
                 mid = (left + right) // 2
                 if arr[mid] == target:
                     result = mid
                     if find_first:
                         right = mid - 1
                     else:
                         left = mid + 1
                 elif arr[mid] < target:
                     left = mid + 1
                 else:
                     right = mid - 1
             return result
         return find_index(arr, target, True), find_index(arr, target, False)
     ```

3. **Задача:** Реализуйте поиск минимального и максимального элементов в массиве.
   - **Решение**:
     ```python
     def find_min_max(arr):
         if not arr:
             return None, None
         min_val, max_val = arr[0], arr[0]
         for num in arr:
             if num < min_val:
                 min_val = num
             elif num > max_val:
                 max_val = num
         return min_val, max_val
     ```

### 3. Алгоритмы работы с данными

1. **Задача:** Удалите дубликаты из списка и сохраните порядок элементов.
   - **Решение**:
     ```python
     def remove_duplicates(arr):
         seen = set()
         return [x for x in arr if not (x in seen or seen.add(x))]
     ```

2. **Задача:** Найдите два элемента в списке, сумма которых равна заданному числу.
   - **Решение**:
     ```python
     def two_sum(arr, target):
         nums_map = {}
         for i, num in enumerate(arr):
             complement = target - num
             if complement in nums_map:
                 return [nums_map[complement], i]
             nums_map[num] = i
         return []
     ```

3. **Задача:** Разверните слова в строке, сохраняя исходный порядок слов.
   - **Решение**:
     ```python
     def reverse_words(sentence):
         return ' '.join(word[::-1] for word in sentence.split())
     ```

---
