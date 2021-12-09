# Intro to Django

### Unit 2 - Getting Started

Install Django by running command

```scheme
pip3 install Django==2.2.12
```

Make a directory for your project files and navigate there with command prompt.

run 
```scheme
django-admin startproject {project_name}
```

Then run..
```scheme
python3 manage.py migrate
```
to automatically configure new files.

After creating the project, Django creates a file `manage.py` and a file directory named after your project.

Basic syntax for using this utility is `python3 manage.py {command}`

**`> runserver`**

It allows you to deploy your website on the server

Settings are located at *settings.py*

```scheme
ALLOWED_HOSTS = ['0.0.0.0', '127.0.0.1']
```

**`> createsuperuser`**
Allows you to create an admin account for your Django web admin panel

**`> startapp`**
Allows you to initialize an app for your project. Basic syntax:

```sheme
python3 manage.py startapp {app_name}
```

##### Question 1
*How would we create an app called Forms?*
`python3 manage.py startapp Forms`

##### Question 2
*How would we run our project on our local network?*
`python3 manage.py runserver 0.0.0.0:8000`

### Unit 3 - Creating a website
