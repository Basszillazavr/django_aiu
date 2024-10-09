
# Практическое руководство: Авторизация в Django и API для мобильных приложений

## Шаг 1: Настройка Django проекта

Для начала, создайте новый Django проект, если он еще не создан.

```bash
django-admin startproject myproject
cd myproject
```

## Шаг 2: Создание приложения

Создайте новое приложение, которое будет управлять пользователями и авторизацией.

```bash
python manage.py startapp users
```

Добавьте ваше приложение в `INSTALLED_APPS` в файле `settings.py`:

```python
INSTALLED_APPS = [
    ...
    'users',
    'rest_framework',  # Для создания API
    ...
]
```

## Шаг 3: Модель пользователя

Если вы хотите использовать стандартную модель пользователя, ее можно оставить. Однако, если вы хотите кастомизировать модель пользователя, создайте её в файле `models.py`:

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

## Шаг 4: Создание системы регистрации и логина

Для того чтобы реализовать систему регистрации и логина, вам нужно создать сериализаторы, которые будут обрабатывать данные.

Создайте файл `serializers.py` в приложении `users` и добавьте следующие классы:

```python
from rest_framework import serializers
from django.contrib.auth.models import User
from django.contrib.auth import authenticate

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ('id', 'username', 'email')

class RegisterSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ('username', 'email', 'password')
        extra_kwargs = {'password': {'write_only': True}}

    def create(self, validated_data):
        user = User.objects.create_user(validated_data['username'], validated_data['email'], validated_data['password'])
        return user

class LoginSerializer(serializers.Serializer):
    username = serializers.CharField()
    password = serializers.CharField()

    def validate(self, data):
        user = authenticate(**data)
        if user and user.is_active:
            return user
        raise serializers.ValidationError("Incorrect Credentials")
```

## Шаг 5: Создание представлений для регистрации и логина

Теперь создайте представления для регистрации и логина в файле `views.py`:

```python
from rest_framework import generics
from rest_framework.response import Response
from rest_framework.permissions import AllowAny
from django.contrib.auth.models import User
from rest_framework.authtoken.models import Token
from .serializers import UserSerializer, RegisterSerializer, LoginSerializer

class RegisterAPI(generics.GenericAPIView):
    serializer_class = RegisterSerializer
    permission_classes = [AllowAny]

    def post(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        user = serializer.save()
        token = Token.objects.create(user=user)
        return Response({
            "user": UserSerializer(user, context=self.get_serializer_context()).data,
            "token": token.key
        })

class LoginAPI(generics.GenericAPIView):
    serializer_class = LoginSerializer
    permission_classes = [AllowAny]

    def post(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        user = serializer.validated_data
        token, created = Token.objects.get_or_create(user=user)
        return Response({
            "user": UserSerializer(user, context=self.get_serializer_context()).data,
            "token": token.key
        })
```

## Шаг 6: Настройка URL

Теперь настройте маршрутизацию для новых представлений. В файле `urls.py` в приложении `users` добавьте:

```python
from django.urls import path
from .views import RegisterAPI, LoginAPI

urlpatterns = [
    path('register/', RegisterAPI.as_view(), name='register'),
    path('login/', LoginAPI.as_view(), name='login'),
]
```

Не забудьте подключить эти URL в основном файле `urls.py` вашего проекта:

```python
from django.urls import path, include

urlpatterns = [
    path('api/users/', include('users.urls')),
]
```

## Шаг 7: Подключение аутентификации по токенам

Для авторизации через API мы будем использовать токены. Убедитесь, что `rest_framework.authtoken` добавлен в `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    ...
    'rest_framework.authtoken',
]
```

Затем создайте миграции и примените их:

```bash
python manage.py migrate
```

## Шаг 8: Тестирование API

Теперь вы можете протестировать API регистрации и логина через Postman или любой другой инструмент для тестирования API.

### Пример запроса на регистрацию:

- URL: `http://127.0.0.1:8000/api/users/register/`
- Метод: POST
- Тело запроса (JSON):

```json
{
    "username": "testuser",
    "email": "testuser@example.com",
    "password": "password123"
}
```

### Пример запроса на логин:

- URL: `http://127.0.0.1:8000/api/users/login/`
- Метод: POST
- Тело запроса (JSON):

```json
{
    "username": "testuser",
    "password": "password123"
}
```

Ответы будут включать токен, который необходимо использовать для авторизации в последующих запросах.

## Шаг 9: Авторизация для мобильных приложений

Теперь для авторизации на мобильном приложении вам нужно передавать токен в заголовках запросов.

Каждый запрос к API должен содержать заголовок:

```
Authorization: Token <ваш_токен>
```

Таким образом, вы сможете осуществлять защищенные запросы от имени аутентифицированного пользователя.

## Заключение

В этом руководстве мы настроили аутентификацию с использованием токенов и создали API для регистрации и логина пользователей, что позволит интегрировать авторизацию в мобильные приложения.
