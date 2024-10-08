
# Инструкция по получению API-ключа для OpenAI GPT

Для использования GPT через OpenAI API в вашем проекте, необходимо получить API-ключ. Следуйте этим шагам:

## Шаг 1: Регистрация на сайте OpenAI

1. Перейдите на сайт [OpenAI](https://openai.com/).
2. Нажмите кнопку "Sign Up" в правом верхнем углу экрана.
3. Зарегистрируйтесь, используя ваш email-адрес, Google или Microsoft аккаунт.
4. Подтвердите свою почту, если требуется.

## Шаг 2: Получение API-ключа

1. После регистрации войдите в свой аккаунт на сайте OpenAI.
2. Перейдите в раздел **API** или по прямой ссылке [platform.openai.com](https://platform.openai.com/).
3. В верхнем правом углу выберите свой профиль и нажмите на **API keys**.
4. В разделе API Keys нажмите на кнопку **Create new secret key**.
5. Скопируйте сгенерированный ключ и сохраните его в безопасном месте.

> **Важно:** Секретные API-ключи отображаются только один раз. Если вы его потеряете, нужно будет создать новый ключ.

## Шаг 3: Настройка переменных окружения

Чтобы защитить ваш API-ключ, рекомендуется не хранить его напрямую в коде, а использовать переменные окружения. 

1. В корневой папке вашего Django-проекта создайте файл `.env` и добавьте в него строку:

   ```env
   OPENAI_API_KEY=ВАШ_СЕКРЕТНЫЙ_API_КЛЮЧ
   ```

2. В вашем `views.py` получите ключ из переменных окружения:

   ```python
   import os
   import openai
   from dotenv import load_dotenv

   load_dotenv()

   openai.api_key = os.getenv('OPENAI_API_KEY')
   ```

Теперь ваш проект защищён, и ключ не будет храниться в явном виде в коде.

## Шаг 4: Настройка тарифного плана

На странице с API-ключами вы также сможете увидеть информацию о лимитах использования API в бесплатной версии. Если вам нужно больше запросов, вы можете перейти на платный тариф.

---

Эта инструкция поможет вам получить и настроить API-ключ для использования GPT в вашем Django-проекте.
