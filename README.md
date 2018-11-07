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

## Models

* object_list
* object
* instance

Model reverse
* used when the url name parameter being passed. (namespace)
```
from django.urls import reverse
#urls - with name param
url(r'^projects/(?P<my_id>\d+)/$', dynamic_lookup_view, name='project-detail'),

# models
def get_absolute_url(self):
    return reverse("project-detail", kwargs={"my_id": self.id})

```

Model get_absolute_url
```
# within the model class
def get_absolute_url(self):
    return "/projects/" + str(self.id)
```

## Model handle no object

```
from django.http import Http404
try:
    obj = Projects.objects.get(id=my_id)
except Projects.DoesNotExist:
    raise Http404
```

```
from django.shortcuts import get_object_or_404
obj = get_object_or_404(Projects, id=my_id)
```

## URL dispatcher

* 1.11

```
url(r'projects/(?P<my_id>\d+)/', dynamic_lookup_view),
```

* 2.0+
```
url(r'projects/(<int:my_id>/', dynamic_lookup_view),
```


## External Refs

* https://www.codingforentrepreneurs.com/blog/common-regular-expressions-for-django-urls/ common regex
* https://rayed.com/posts/2018/05/django-crud-create-retrieve-update-delete/ basic crud using class based views
* https://www.youtube.com/watch?v=L6y6cn1XUfw ckeditor