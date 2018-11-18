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

Login redirect path - if not defined login() goes to /accounts/profile
```
LOGIN_REDIRECT_URL = '/'
```

## Models
Names that the template access the querysets as (CBV)
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

## Email
Localhost email debugging
```
python -m smtpd -n -c DebuggingServer localhost:1025
and adjust your mail settings accordingly:

EMAIL_HOST = 'localhost'
EMAIL_PORT = 1025
```


## External Refs

* https://www.codingforentrepreneurs.com/blog/common-regular-expressions-for-django-urls/ common regex
* https://rayed.com/posts/2018/05/django-crud-create-retrieve-update-delete/ basic crud using class based views
* https://www.youtube.com/watch?v=L6y6cn1XUfw ckeditor
* https://scotch.io/tutorials/an-introduction-to-regex-in-python regex
* https://simpleisbetterthancomplex.com/tutorial/2017/02/18/how-to-create-user-sign-up-view.html


## Example populate db from script

```
import os
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'src.settings')

import django
django.setup()

import random

from designers.models import Designer, DesignTypes

from faker import Faker

fakegen = Faker("en_AU")

print (fakegen)

types_choices = ["Commercial",
                "Industrial",
                "Residential",
                "Other"
                ]

def populate(N=5):
    for entry in range(N):
        name = fakegen.name().split()
        firstname = name[0]
        surname = name[1]
        email = fakegen.email()
        telephone = fakegen.phone_number()
        types_name = random.choice(types_choices)
        types = types_choices.index(types_name) + 1
        designer = Designer()
        designer.save()
        designer.firstname = firstname
        designer.surname = surname
        designer.email = email
        designer.types = str(types)

        designer.save()

        # import pdb; pdb.set_trace()


        # designer = Designer.objects.add(firstname=firstname, surname=surname, email=email, telephone=telephone, types=types)


if __name__ == "__main__":
    print("populating")
    populate(50)
    print("populating fin")
```

## Example paginate db

```
from django.shortcuts import render
from django.views.generic import ListView
from django.core.paginator import Paginator
from django.core.paginator import EmptyPage
from django.core.paginator import PageNotAnInteger
from .models import Designer, DesignTypes

# Create your views here.
class DesignerListView(ListView):
    model = Designer

    def get_context_data(self, **kwargs):
        print("get_context_data")

        filter_val = self.request.GET.get('designer-type')
        if filter_val:
            context = super(DesignerListView, self).get_context_data(**kwargs)
            context['designers'] = Designer.objects.filter(
                types=filter_val,
            ).order_by('-updated')
            context['types'] = DesignTypes.objects.all()
        else:
            context = super(DesignerListView, self).get_context_data(**kwargs)
            context['designers'] = Designer.objects.all()
            context['types'] = DesignTypes.objects.all()

        return context


class DesignerPaginateListView(ListView):
    model = Designer
    paginate_by = 10

    def get_context_data(self, **kwargs):
        # import pdb; pdb.set_trace()
        filter_val = self.request.GET.get('designer-type')
        page = self.request.GET.get('filter-page')
        context = super(DesignerPaginateListView, self).get_context_data(**kwargs)


        if filter_val is None:
            sql = Designer.objects.all()
            context['current_type'] = filter_val
        elif filter_val == "All":
            sql = Designer.objects.all()
            context['current_type'] = filter_val
        else:
            sql = Designer.objects.filter(
                types=filter_val,
            ).order_by('-updated')
            context['current_type'] = int(filter_val)


        context['types'] = DesignTypes.objects.all()
        paginator = Paginator(sql, self.paginate_by)

        try:
            context['designers'] = paginator.page(page)
        except PageNotAnInteger:
            # If page is not an integer, deliver first page.
            context['designers'] = paginator.page(1)
        except EmptyPage:
            # If page is out of range (e.g. 9999), deliver last page of results.
            context['designers'] = paginator.page(paginator.num_pages)

        return context
```