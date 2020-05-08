# Introducción a Django

## Requerimientos

- virtualenv: para proteger los paquetes globales
```bash
virtualenv --system-site-packages -p python3 dir/to/venv
source dir/to/venv/bin/activate
```
- python 3.7
- pip

dentro del ambiente virtual instalar django

```bash
pip install django
```

## Inicializar un nuevo proyecto

abrir la terminal en alguna carpeta donde vaya a estar ubicado el proyecto
```bash
django-admin startproject mysite
```

## Desplegar el proyecto

```bash
cd mysite
python manage.py runserver 0.0.0.0:port
```

## Configurar static/

poner en el archivo **mysite/settings.py** depués de STATIC_URL
```python
STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
```

más adelante sirve para ejecutar el comando

```bash
python manage.py collectstatic
```

se puede manejar una carpeta **/templates/** o **

## Crear App
Dentro del proyecto se pueden crear diferentes apps para, dependiendo de las funcionalidades, englobar modulos

```bash
python manage.py startapp projectApp
```

Cada vez que se crea un app se deben crear los directorios **projectApp/static** para manejar archivos css y js,
y **projectApp/templates/projectApp** para manejar los archivos html

Al crear una nueva app añadirla a **settings.py**, esto permite anexar las urls, archivos estaticos y templates propios de la aplicación

```python
INSTALLED_APPS = [
    ...
    'projectApp',
    ...
]
```

### Glosario:
> **vista:** función que renderiza un html al ser llamada por medio de una url  
> **path:** función que le asigna una url a una vista  
> **include:** función que consulta modulos .py en aplicaciones

para acceder a vistas de esta app en la app principal se debe hacer lo siguiente:

crear el archivo **projectApp/urls.py** con la misma estructura de **mysite/urls.py** con algunos cambios  
luego agregar al archivo **projectApp/views.py** una vista

Crear el archivo **projectApp/urls.py:**, ejemplo:
```python
from django.urls import path
# importar el archivo projectApp/views.py
from .views import *

urlpatterns = [
    # Donde index es una función importada desde projectApp/views.py
    path('', index, name="index"),
    # Donde page es una función importada desde projectApp/views.py
    path('page', page, name="page"),
]
```

Crear el archivo **projectApp/views.py**, ejemplo
``` python
# permite ejecutar tags de django-templates
from django.shortcuts import render

# permite crear una respuesta HTTP
from django.http import HttpResponse

# Create your views here.
# index retorna string
def index(request):
    return HttpResponse("Hola mundo")

# page procesa el archivo index.html
def page(request):
    return render(request,'projectApp/index.html')
```

Añadir urls de la nueva app a **mysite/urls.py:**
```python
from django.urls import include

urlpatterns = [ 
    #...
    path('projectApp/', include("projectApp.urls")), 
    #...
] 
```

Como se puede observar la vista **page** procesa el archivo index.html

Este archivo tiene etiquetas propias de django templates las cuales se 
procesan por medio de la función **render**
Este archivo se encuentra en **projectApp/templates/projectApp/index.html**:
```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link href="{% static 'style.css' %}" rel="stylesheet">
    <title>Index</title>
</head>
<body class="text-red">
    Hola Mundo
</body>
</html>
```

Archivo **projectApp/static/style.css** respectivo:
```css
.text-red{
    color:red;
}
```