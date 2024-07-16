# Invoice_Generator_Dashboard
## Ubuntu-Pythonic Brilliance: Crafting an Elegant Invoice Dashboard with Django-Rest Framework.





### `Working of the Invoice Generator :`
(https://youtu.be/vXO60e9ZE9U)



### This project contains three models:  Client , Product/Services , Invoices. 
--We can --
`login with user`
`add/ delete invoices, clients, product , services .`


#### Connecting to Database:
```
$ sudo service postgresql start
$ sudo -u postgres psql
```
#### Database Created-- `invoice`

Granted all privileges on database:
```
ALTER ROLE invoiceuser SET client_encoding TO 'utf-8';
ALTER ROLE invoiceuser SET default_transaction_isolation TO 'read committed';
ALTER ROLE invoiceuser SET timezone to 'Asia/Calcutta';
```

#### Creating a virtual Environment:
```
$ source invoiceenv/bin/activate
```
#### `Installing Django and psycopg2--`
```
pip install django
pip install psycopg2
```

#### Invoicing Project created - Django

So the directory workspace breakdown is-
`(invoiceenv)nikita@Desktop:/mnt/c/development/invoicing/invoicing/invoicing$ ls -l`

This shows the files created in the directory.

#### Edit the settings.py to be able to connect to the database-
nano settings.py

```
import os
TIMEZONE='Asia/Calcutta'
DEBUG= True
ALLOWED_HOSTS=[*]
```
#### --Running migrations to set up database: 
```
python manage.py makemigrations
python manage.py migrate
```
#### Create an admin user that you can log in to the admin panel:
```
Username:
Email address:
Password:
#Superuser created successfully
```

### `TO RUN THE SERVER :`
```
python manage.py runserver 0.0.0.0:5000
```
#### `To generate ssh key:`
```
ssh-keygen -t rsa
Enter the desired passphrase:
Key's randomart image is created and displayed.
```

#### `Invoice Startapp-- We have our models here `
![image](https://github.com/nikitabandiwar/Invoice_Generator_Dasboard/assets/104710564/512e41ce-af2e-45cd-b366-79c80328fd7a)


#### `Database Model Design`
![image](https://github.com/nikitabandiwar/Invoice_Generator_Dasboard/assets/104710564/6e942f12-708d-420a-a371-e3a862d288cf)




#### `--Installing uuids to make unique id s for slugs inside of the models `
![image](https://github.com/nikitabandiwar/Invoice_Generator_Dasboard/assets/104710564/3d448979-fee6-490d-a278-a30fc1c80aea)
#### Use of uuid's:
`So if we find two clients with the same name then we can differentiate on the basis of this unique id’s.

Now in the models.py, we have created such that even if we are generating the invoice @ 1 pm in India . And a subordinate client sees it in U.S it will reflect the time acc. To their timezone.
`


#### Create the Three models:
```
1)Client model
2)Product/services model
3) Invoice Model

```

### Code for creating models in Django :
```
from django.db import models
from django.template.defaultfilters import slugify
from django.utils import timezone
from uuid import uuid4
from django.contrib.auth.models import User



class Client(models.Model):

    PROVINCES = [
    ('Maharashtra', 'Maharashtra'),
    ('Delhi', 'Delhi'),
    ('Kerala', 'Kerala'),
    ]

    #Basic Fields.
    clientName = models.CharField(null=True, blank=True, max_length=200)
    addressLine1 = models.CharField(null=True, blank=True, max_length=200)
    
    province = models.CharField(choices=PROVINCES, blank=True, max_length=100)
    postalCode = models.CharField(null=True, blank=True, max_length=10)
    phoneNumber = models.CharField(null=True, blank=True, max_length=100)
    emailAddress = models.CharField(null=True, blank=True, max_length=100)
    taxNumber = models.CharField(null=True, blank=True, max_length=100)


    #Utility fields
    uniqueId = models.CharField(null=True, blank=True, max_length=100)
    slug = models.SlugField(max_length=500, unique=True, blank=True, null=True)
    date_created = models.DateTimeField(blank=True, null=True)
    last_updated = models.DateTimeField(blank=True, null=True)


    def __str__(self):
        return '{} {} {}'.format(self.clientName, self.province, self.uniqueId)


    def get_absolute_url(self):
        return reverse('client-detail', kwargs={'slug': self.slug})


    def save(self, *args, **kwargs):
        if self.date_created is None:
            self.date_created = timezone.localtime(timezone.now())
        if self.uniqueId is None:
            self.uniqueId = str(uuid4()).split('-')[4]
            self.slug = slugify('{} {} {}'.format(self.clientName, self.province, self.uniqueId))

        self.slug = slugify('{} {} {}'.format(self.clientName, self.province, self.uniqueId))
        self.last_updated = timezone.localtime(timezone.now())

        super(Client, self).save(*args, **kwargs)



class Product(models.Model):
    CURRENCY = [
    ('R', 'IND'),
    ('$', 'USD'),
    ]

    title = models.CharField(null=True, blank=True, max_length=100)
    description = models.TextField(null=True, blank=True)
    quantity = models.FloatField(null=True, blank=True)
    price = models.FloatField(null=True, blank=True)
    currency = models.CharField(choices=CURRENCY, default='R', max_length=100)

    #Utility fields
    uniqueId = models.CharField(null=True, blank=True, max_length=100)
    slug = models.SlugField(max_length=500, unique=True, blank=True, null=True)
    date_created = models.DateTimeField(blank=True, null=True)
    last_updated = models.DateTimeField(blank=True, null=True)


    def __str__(self):
        return '{} {}'.format(self.title, self.uniqueId)


    def get_absolute_url(self):
        return reverse('product-detail', kwargs={'slug': self.slug})


    def save(self, *args, **kwargs):
        if self.date_created is None:
            self.date_created = timezone.localtime(timezone.now())
        if self.uniqueId is None:
            self.uniqueId = str(uuid4()).split('-')[4]
            self.slug = slugify('{} {}'.format(self.title, self.uniqueId))

        self.slug = slugify('{} {}'.format(self.title, self.uniqueId))
        self.last_updated = timezone.localtime(timezone.now())

        super(Product, self).save(*args, **kwargs)



class Invoice(models.Model):
    TERMS = [
    ('14 days', '14 days'),
    ('30 days', '30 days'),
    ('60 days', '60 days'),
    ]

    STATUS = [
    ('CURRENT', 'CURRENT'),
    ('OVERDUE', 'OVERDUE'),
    ('PAID', 'PAID'),
    ]

    title = models.CharField(null=True, blank=True, max_length=100)
    number = models.CharField(null=True, blank=True, max_length=100)
    dueDate = models.DateField(null=True, blank=True)
    paymentTerms = models.CharField(choices=TERMS, default='14 days', max_length=100)
    status = models.CharField(choices=STATUS, default='CURRENT', max_length=100)
    notes = models.TextField(null=True, blank=True)

    #RELATED fields
    client = models.ForeignKey(Client, blank=True, null=True, on_delete=models.SET_NULL)
    product = models.ForeignKey(Product, blank=True, null=True, on_delete=models.SET_NULL)

    #Utility fields
    uniqueId = models.CharField(null=True, blank=True, max_length=100)
    slug = models.SlugField(max_length=500, unique=True, blank=True, null=True)
    date_created = models.DateTimeField(blank=True, null=True)
    last_updated = models.DateTimeField(blank=True, null=True)


    def __str__(self):
        return '{} {}'.format(self.title, self.uniqueId)


    def get_absolute_url(self):
        return reverse('invoice-detail', kwargs={'slug': self.slug})


    def save(self, *args, **kwargs):
        if self.date_created is None:
            self.date_created = timezone.localtime(timezone.now())
        if self.uniqueId is None:
            self.uniqueId = str(uuid4()).split('-')[4]
            

        self.slug = slugify('{} {}'.format(self.title, self.uniqueId))
        self.last_updated = timezone.localtime(timezone.now())

        super(Invoice, self).save(*args, **kwargs)


class Settings(models.Model):

    PROVINCES = [
    ('Maharashtra', 'Maharashtra'),
    ('Delhi', 'Delhi'),
    ('Kerala', 'Kerala'),
    ]

    #Basic Fields
    clientName = models.CharField(null=True, blank=True, max_length=200)
    
    addressLine1 = models.CharField(null=True, blank=True, max_length=200)
    province = models.CharField(choices=PROVINCES, blank=True, max_length=100)
    postalCode = models.CharField(null=True, blank=True, max_length=10)
    phoneNumber = models.CharField(null=True, blank=True, max_length=100)
    emailAddress = models.CharField(null=True, blank=True, max_length=100)
    taxNumber = models.CharField(null=True, blank=True, max_length=100)


    #Utility fields
    uniqueId = models.CharField(null=True, blank=True, max_length=100)
    slug = models.SlugField(max_length=500, unique=True, blank=True, null=True)
    date_created = models.DateTimeField(blank=True, null=True)
    last_updated = models.DateTimeField(blank=True, null=True)


    def __str__(self):
        return '{} {} {}'.format(self.clientName, self.province, self.uniqueId)


    def get_absolute_url(self):
        return reverse('settings-detail', kwargs={'slug': self.slug})


    def save(self, *args, **kwargs):
        if self.date_created is None:
            self.date_created = timezone.localtime(timezone.now())
        if self.uniqueId is None:
            self.uniqueId = str(uuid4()).split('-')[4]
            self.slug = slugify('{} {} {}'.format(self.clientName, self.province, self.uniqueId))

        self.slug = slugify('{} {} {}'.format(self.clientName, self.province, self.uniqueId))
        self.last_updated = timezone.localtime(timezone.now())

        super(Settings, self).save(*args, **kwargs)
```

### Registering the models:
```
from django.contrib import admin
from .models import *

admin.site.register(Client)
admin.site.register(Product)
admin.site.register(Invoice)
admin.site.register(Settings)

```


### `Again run the server :`
```
python manage.py runserver 0.0.0.0:5000

```

