release: python manage.py migrate
web: gunicorn staynest.wsgi --bind 0.0.0.0:$PORT
