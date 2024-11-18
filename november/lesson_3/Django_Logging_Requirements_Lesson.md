
# Урок по логированию и requirements.txt в Django

## 1. Логирование в Django

Логирование помогает отслеживать ошибки и события в приложении, сохраняя их в файл для последующего анализа.

### Настройка логирования
1. В файле `settings.py` добавьте секцию `LOGGING`:
    ```python
    LOGGING = {
        'version': 1,
        'disable_existing_loggers': False,
        'handlers': {
            'file': {
                'level': 'DEBUG',
                'class': 'logging.FileHandler',
                'filename': 'debug.log',
            },
        },
        'loggers': {
            'django': {
                'handlers': ['file'],
                'level': 'DEBUG',
                'propagate': True,
            },
        },
    }
    ```

2. После настройки события будут записываться в файл `debug.log` в корневой директории проекта.

### Использование логов
Добавьте логирование в код:
```python
import logging
logger = logging.getLogger('django')

def example_view(request):
    logger.debug('Debug message')
    logger.error('Error message')
    return HttpResponse("Логирование работает!")
```

### Практическое задание
1. Настройте логирование в файл `error.log` для сообщений уровня `ERROR`.
2. Создайте функцию, которая записывает сообщения разных уровней (`DEBUG`, `INFO`, `ERROR`).

---

## 2. Работа с requirements.txt

`requirements.txt` используется для управления зависимостями проекта.

### Установка зависимостей
1. Создайте виртуальное окружение:
    ```bash
    python -m venv venv
    source venv/bin/activate  # Linux/Mac
    venv\Scripts\activate  # Windows
    ```

2. Установите пакеты:
    ```bash
    pip install django requests
    ```

### Сохранение зависимостей
1. Сохраните текущие зависимости в файл `requirements.txt`:
    ```bash
    pip freeze > requirements.txt
    ```
2. Содержимое файла будет выглядеть примерно так:
    ```
    Django==4.2
    requests==2.31.0
    ```

### Установка зависимостей из requirements.txt
1. Убедитесь, что виртуальное окружение активировано.
2. Установите зависимости:
    ```bash
    pip install -r requirements.txt
    ```

### Практическое задание
1. Создайте проект Django.
2. Добавьте библиотеку `requests` и сохраните зависимости в файл `requirements.txt`.
3. Проверьте, что файл корректно устанавливает все зависимости в новом виртуальном окружении.

---

## Результат
Теперь вы знаете, как:
- Настраивать логирование в Django и записывать логи в файл.
- Управлять зависимостями проекта с помощью `requirements.txt`.
