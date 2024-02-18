
After creating a model in models.py

``` python
from django.db import models

# Create your models here.

class Product(models.Model):

    product_id = models.AutoField

    product_name = models.CharField(max_length=50)

    desc = models.CharField(max_length=300)

    pub_date = models.DateField()

```


---


do migrations and must register model under admin.py

``` python
from django.contrib import admin

# Register your models here.

from .models import Product

admin.site.register(Product)

```

## Now we can see out models in admin dashboard