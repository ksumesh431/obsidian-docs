# In settings.py under TEMPLATES, put the directory name under DIRS

``` python
TEMPLATES = [

    {

        'BACKEND': 'django.template.backends.django.DjangoTemplates',

        'DIRS': ['templates'],

        'APP_DIRS': True,

        'OPTIONS': {

            'context_processors': [

                'django.template.context_processors.debug',

                'django.template.context_processors.request',

                'django.contrib.auth.context_processors.auth',

                'django.contrib.messages.context_processors.messages',

            ],

        },

    },

]

```

---

# Create a directory anmed "templates" in the same path as manage.py and put index.html in it


---

# In any function, use "render" to render the html page

``` python
from django.shortcuts import render

def index(request):
    context = { 'variable1': 'value1', 'variable2': 'value2' }
    return render(request, 'index.html', context)

```

### The third argument can be used to pass key value pairs which can be accessed by the index.html file
``` html
<!DOCTYPE html>
<html lang="en">
<head>
<title>My Page</title>
</head>
<body> <h1>{{ variable1 }}</h1> <p>{{ variable2 }}</p>
</body>
</html>

```