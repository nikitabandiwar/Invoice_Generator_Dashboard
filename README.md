# Invoice_Generator_Dashboard
## Ubuntu-Pythonic Brilliance: Crafting an Elegant Invoice Dashboard with Django-Rest Framework.


### Link to the website:(http://127.0.0.1:5555/admin/login/?next=/admin/)


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
#### `Create an admin user that you can log in to the admin panel:`
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
### `Again run the server :`
```
python manage.py runserver 0.0.0.0:5000

```

