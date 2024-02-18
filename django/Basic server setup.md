Start with `django-admin startproject proj-name`

---
# urls.py
``` python
from django.contrib import admin

from django.urls import path

from . import views

urlpatterns = [

    path('admin/', admin.site.urls),

    path("", views.index, name="index"),

    path("removepunc", views.removepunc, name="removepunc"),

    path("capfirst", views.capfirst, name="capfirst"),

    path("newlineremove", views.newlineremove, name="newlineremove"),

    path("spaceremove", views.spaceremove, name="spaceremove"),

    path("charcount", views.charcount, name="charcount"),

]

```

---

# views.py
``` python
from django.http import HttpResponse

  

def index(request):

    return HttpResponse("Home")

  

def removepunc(request):

    return HttpResponse("removepunc")

  

def capfirst(request):

    return HttpResponse("capfirst")

  

def newlineremove(request):

    return HttpResponse("newlineremove")

  

def spaceremove(request):

    return HttpResponse("spaceremove")

  

def charcount(request):

    return HttpResponse("charcount")

```

---

After that, do `python manage.py runserver`