
```markdown
# Django with Celery Integration



### 1. Clone the Repository



### 2. Create a Virtual Environment (Optional but Recommended)


# Install virtualenv if you haven't already
```bash
pip install virtualenv
```
# Create a virtual environment
```bash
virtualenv venv
```

# Activate the virtual environment (Windows)
```bash
venv\Scripts\activate
```

# Activate the virtual environment (Unix or MacOS)
```bash
source venv/bin/activate
```

### 3. Install Dependencies

Install Django, Celery, and Redis:

```bash
pip install django celery redis
```

### 4. Configure Django Settings

Edit `django_celery/settings.py` to configure Celery and Django settings:

```python
# django_celery/settings.py


CELERY_BROKER_URL = 'redis://127.0.0.1:6379'  
CELERY_ACCEPT_CONTENT = ['application/json']
CELERY_RESULT_SERIALIZER = 'json'
CELERY_TASK_SERIALIZER = 'json'
CELERY_TIMEZONE = 'Asia/Kolkata'  

# Add 'celery' and your app to INSTALLED_APPS
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'demo',
    'celery',
]
```

### 5. Create Celery Configuration

Create `django_celery/celery.py` to configure Celery:

```python
# django_celery/celery.py

from __future__ import absolute_import, unicode_literals
import os
from celery import Celery
from django.conf import settings

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'django_celery.settings')

app = Celery('django_celery')


app.config_from_object('django.conf:settings', namespace='CELERY')

app.autodiscover_tasks(lambda: settings.INSTALLED_APPS)

app.conf.timezone = 'Asia/Kolkata'
```

### 6. Define a Celery Task

Create `demo/tasks.py` to define a Celery task:

```python
# demo/tasks.py

from celery import shared_task

@shared_task(bind=True)
def test_func(self):
    for i in range(10):
        print(i)
    return "Done"
```

### 7. Integrate Celery Task in a Django View

Modify `demo/views.py` to integrate Celery task in a Django view:

```python
# demo/views.py

from django.http import HttpResponse
from .tasks import test_func

def test(request):
    test_func.delay()  # Run test_func asynchronously
    return HttpResponse("Task started. Check console for output.")
```

### 8. Setup URL Routing

Modify `demo/urls.py` to set up URL routing for the view:

```python
# demo/urls.py

from django.urls import path
from . import views

urlpatterns = [
    path('', views.test, name='test'),
]
```

### 9. Run Celery Worker

Start a Celery worker to execute tasks:

```bash
celery -A django_celery worker -l info
```

### 10. Start Django Development Server

Run the Django development server to test your application:

```bash
python manage.py runserver
```

Now, visit `http://localhost:8000` in your browser and trigger the `/test` endpoint to see Celery in action.
```
