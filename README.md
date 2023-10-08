# Django-Celery-Setup-Guide

*Expecting that you already have the Django setup and migrated the database properly.*

1. Activate the virtualenvrionment. And install the celery package using this command: `python -m pip install celery`

2. Run `python -m celery worker` to check if the celery is working.
This will show an error like below:
*`[ERROR/MainProcess] consumer: Cannot connect to amqp://guest:**@127.0.0.1:5672//: [Errno 61] Connection refused.
Trying again in 2.00 seconds... (1/100)`*

3. Now you need to install redis as the message broker so that celery can communicate with the task broker.
`sudo apt update` \
`sudo apt install redis`

4. Check if the redis is installed properly by running: 
> `redis-cli` \
> `127.0.0.1:6379> ping` \
> `PONG` 

5. Install the redis in virtualenvrionment.
`python -m pip install redis`.

6. Run the celery worker. 
`python -m celery -A project_name worker`

7. Create a `celery.py` file in the project folder where the settings.py is. And insert this code into that file.
``` 
# project_name/celery.py

import os
from celery import Celery
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "project_name.settings")
app = Celery("project_name")
app.config_from_object("django.conf:settings", namespace="CELERY")
app.autodiscover_tasks()
```
8. Add the celery conf code in settings.py file.
```
# django_celery/settings.py

# ...

# Celery settings
CELERY_BROKER_URL = "redis://localhost:6379"
CELERY_RESULT_BACKEND = "redis://localhost:6379"
```

9. Add Celery initialization code in __init__.py file inside the project folder.
```
# django_celery/__init__.py

from .celery import app as celery_app

__all__ = ("celery_app",)
```


10. Check celery tasks.
`celery -A start_project inspect active`
