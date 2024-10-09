
# Интеграция Hugging Face API в Django для обучения

## Шаг 1: Создание аккаунта на Hugging Face

1. Зарегистрируйтесь на [Hugging Face](https://huggingface.co/join).
2. Получите **токен доступа** через [страницу настроек](https://huggingface.co/settings/tokens), чтобы использовать его для работы с API.

## Шаг 2: Установка библиотеки `transformers`

Для взаимодействия с API Hugging Face можно использовать библиотеку `transformers` или отправлять HTTP-запросы. Установите библиотеку:

```bash
pip install transformers
```

## Шаг 3: Интеграция API Hugging Face в Django

1. В вашем приложении Django создайте представление для обращения к Hugging Face API. Откройте `views.py` и добавьте следующий код:

```python
import requests
from django.http import JsonResponse

def ask_hugging_face(request):
    # Получите вопрос из запроса
    question = request.GET.get('question', '')

    if not question:
        return JsonResponse({'error': 'No question provided'}, status=400)

    # Токен доступа Hugging Face
    API_TOKEN = 'ваш_токен'

    # Модель для работы с вопросами и ответами
    model = "deepset/roberta-base-squad2"  # Пример модели

    # URL для обращения к API
    API_URL = f"https://api-inference.huggingface.co/models/{model}"

    headers = {
        "Authorization": f"Bearer {API_TOKEN}"
    }

    data = {
        "inputs": {
            "question": question,
            "context": "Добавьте контекст для ваших вопросов, например, учебный материал"
        }
    }

    response = requests.post(API_URL, headers=headers, json=data)

    if response.status_code == 200:
        return JsonResponse(response.json(), safe=False)
    else:
        return JsonResponse({'error': 'API request failed'}, status=response.status_code)
```

2. Настройте маршрутизацию. В `urls.py` добавьте маршрут для нового представления:

```python
from django.urls import path
from .views import ask_hugging_face

urlpatterns = [
    path('ask/', ask_hugging_face, name='ask_hugging_face'),
]
```

## Шаг 4: Тестирование API

Теперь можно протестировать API с помощью инструмента Postman или браузера. Отправьте GET-запрос с вопросом:

- URL: `http://127.0.0.1:8000/ask/?question=ваш_вопрос`
- Ответ от Hugging Face API вернется в формате JSON.

### Пример запроса:

```
GET /ask/?question=What is Django?
```

### Пример ответа:

```json
{
    "answer": "Django is a high-level Python Web framework.",
    "score": 0.99,
    "start": 10,
    "end": 40
}
```

## Шаг 5: Использование Hugging Face для мобильных приложений

Для авторизации в мобильном приложении используйте Django Rest Framework и Hugging Face API. В мобильных запросах отправляйте заголовки с токеном:

```
Authorization: Token <ваш_токен>
```

Так вы сможете интегрировать API с мобильным приложением, реализуя систему вопросов и ответов.

## Заключение

В этом руководстве мы рассмотрели, как интегрировать Hugging Face API в Django для обучения. Этот процесс позволяет студентам задавать вопросы и получать ответы, используя мощь моделей машинного обучения.
