
# Урок 3: Модели, миграции и работа с базами данных в Django

**Цель урока:**  
Изучить создание моделей, миграций и работу с базами данных в Django. После этого урока студенты смогут создавать модели, выполнять миграции и взаимодействовать с базой данных через Django ORM.

---

### 1. Модели в Django

Модель — это описание структуры данных, которые будут храниться в базе данных. Модели в Django представляют таблицы базы данных и создаются в файле `models.py` приложения.

#### Основные моменты:
- Каждая модель представляет таблицу базы данных.
- Поля модели представляют колонки таблицы.
- Django автоматически создает таблицы и управляет миграциями базы данных на основе моделей.

#### Пример простой модели:

```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    published_date = models.DateTimeField(auto_now_add=True)
```

В этом примере модель `Post` представляет таблицу с полями для заголовка (`title`), контента (`content`) и даты публикации (`published_date`).

---

### 2. Миграции в Django

Миграции — это способ, с помощью которого Django управляет изменениями в структуре базы данных. Миграции создаются на основе изменений в моделях.

#### Основные моменты:
- Миграции создаются с помощью команды `makemigrations`.
- Применение миграций происходит с помощью команды `migrate`.
- Django сохраняет все миграции, что позволяет откатывать и обновлять базу данных при изменении моделей.

#### Пример создания и применения миграций:

1. **Создайте миграции**:
   ```bash
   python manage.py makemigrations
   ```

2. **Примените миграции**:
   ```bash
   python manage.py migrate
   ```

---

### 3. Django ORM (Object-Relational Mapping)

Django предоставляет мощную систему ORM, которая позволяет взаимодействовать с базой данных через Python-код, без необходимости писать SQL-запросы.

#### Основные функции Django ORM:
- Создание записей в базе данных.
- Обновление, удаление и выборка данных.
- Работа с отношениями между моделями (один ко многим, многие ко многим и т.д.).

#### Пример работы с ORM:

1. **Создание записи**:
   ```python
   post = Post.objects.create(title="Мой первый пост", content="Это содержимое моего первого поста")
   ```

2. **Получение всех записей**:
   ```python
   posts = Post.objects.all()
   ```

3. **Фильтрация записей**:
   ```python
   posts = Post.objects.filter(title__icontains="пост")
   ```

4. **Обновление записи**:
   ```python
   post = Post.objects.get(id=1)
   post.title = "Обновленный заголовок"
   post.save()
   ```

5. **Удаление записи**:
   ```python
   post = Post.objects.get(id=1)
   post.delete()
   ```

---

### Практическое задание

1. Создайте модель для хранения информации о книгах: название, автор, дата публикации, ISBN.
2. Создайте миграции и примените их.
3. Взаимодействуйте с моделью через Django ORM: создайте несколько записей, отфильтруйте их по автору, обновите одну из записей и удалите другую.