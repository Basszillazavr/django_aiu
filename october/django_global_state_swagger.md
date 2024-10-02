
# Управление глобальным состоянием в Django

В Django нет встроенного эквивалента Redux, как в React, для управления глобальным состоянием. Однако можно использовать различные механизмы для передачи данных между компонентами приложения и сохранения состояния.

### Способы управления глобальным состоянием:

1. **Контекстные процессоры Django**  
   Контекстные процессоры позволяют добавлять данные в контекст всех шаблонов. Это можно использовать как аналог глобального состояния.

   Пример добавления глобальных данных в шаблоны:

   ```python
   # settings.py
   TEMPLATES = [
       {
           'OPTIONS': {
               'context_processors': [
                   'your_app.context_processors.global_settings',
               ],
           },
       },
   ]

   # your_app/context_processors.py
   def global_settings(request):
       return {
           'site_name': 'My Django Site',
           'admin_email': 'admin@mysite.com',
       }
   ```

   Теперь `site_name` и `admin_email` будут доступны в каждом шаблоне вашего приложения.

2. **Сессии и куки**  
   Django поддерживает сессии, которые можно использовать для хранения данных между запросами. Это полезно для хранения информации, специфичной для пользователя.

   Пример использования сессий:

   ```python
   # views.py
   def set_session(request):
       request.session['username'] = 'John'
       return HttpResponse('Session set')

   def get_session(request):
       username = request.session.get('username', 'Guest')
       return HttpResponse(f'Hello {username}')
   ```

3. **Сигналы Django**  
   Сигналы позволяют компонентам приложения общаться между собой. Это удобно для создания глобальной логики взаимодействия между моделями и другими компонентами.

   Пример сигнала:

   ```python
   # signals.py
   from django.db.models.signals import post_save
   from django.dispatch import receiver
   from .models import Post

   @receiver(post_save, sender=Post)
   def my_handler(sender, instance, **kwargs):
       print(f'Post "{instance.title}" was saved!')
   ```

---

# Инструменты для API-документации в Django (Swagger)

Swagger — это мощный инструмент для документирования и тестирования API. В Django есть несколько пакетов, которые предоставляют аналогичный функционал:

1. **Django REST Framework + drf-yasg**  
   Это комбинация Django REST Framework (DRF) и библиотеки `drf-yasg` для генерации Swagger-документации.

   Установка:

   ```bash
   pip install drf-yasg
   ```

   Настройка:

   ```python
   # urls.py
   from rest_framework import permissions
   from drf_yasg.views import get_schema_view
   from drf_yasg import openapi

   schema_view = get_schema_view(
       openapi.Info(
           title="Your API",
           default_version='v1',
           description="API documentation",
           terms_of_service="https://www.google.com/policies/terms/",
           contact=openapi.Contact(email="contact@yourdomain.local"),
           license=openapi.License(name="BSD License"),
       ),
       public=True,
       permission_classes=(permissions.AllowAny,),
   )

   urlpatterns = [
       path('swagger/', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
   ]
   ```

   Теперь Swagger-документация будет доступна по пути `/swagger/`.

2. **Django REST Framework + CoreAPI**  
   CoreAPI — это еще один инструмент для документирования и тестирования API в DRF.

   Установка:

   ```bash
   pip install coreapi
   ```

   Настройка:

   ```python
   # settings.py
   REST_FRAMEWORK = {
       'DEFAULT_SCHEMA_CLASS': 'rest_framework.schemas.coreapi.AutoSchema',
   }
   ```

   После этого документация API будет автоматически сгенерирована и доступна через CoreAPI.
