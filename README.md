# Django helpers (1.11)

## Settings


Setup templates folder within the root
```
'DIRS': [os.path.join(BASE_DIR, 'templates')],
```

Setup static folder within the root
```
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, "static"),
]
```

Django admin bar add internal IPs
```
INTERNAL_IPS = ('127.0.0.1',)
```

Login redirect path
```
LOGIN_REDIRECT_URL = '/'
```