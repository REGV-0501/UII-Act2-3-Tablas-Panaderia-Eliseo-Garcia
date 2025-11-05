
Model
¡Excelente! Aquí tienes la primera parte del proyecto "Panadería" con todas las especificaciones que solicitaste, siguiendo la estructura y los pasos detallados para que puedas implementarlo fácilmente en VS Code con Python/Django.  
Proyecto: Panadería  
Lenguaje: Python  
Framework: Django  
Editor: VS Code  
1 — Estructura general y nombres  
Proyecto raíz: UIII_Panadería_0501  
Proyecto Django (backend): backend_Panaderia  
App: app_Panaderia  
2 — Procedimiento para crear carpeta del Proyecto: UIII_Panadería_0501  
En tu sistema (Terminal):  
code  
Bash  
mkdir UIII_Panadería_0501  
cd UIII_Panadería_0501  
3 — Procedimiento para abrir VS Code sobre la carpeta UIII_Panadería_0501  
En la misma carpeta UIII_Panadería_0501:  
code  
Bash  
code .
(Si code . no funciona, abre VS Code manualmente y selecciona File > Open Folder... y elige UIII_Panadería_0501.)  
4 — Procedimiento para abrir terminal en VS Code  
En VS Code: Terminal > New Terminal o usa el atajo (normalmente Ctrl+ñ o `Ctrl+`` dependiendo de tu configuración).  
5 — Procedimiento para crear carpeta entorno virtual “.venv” desde terminal de VS Code  
Dentro de la carpeta del proyecto UIII_Panadería_0501:   
code  
Bash  

    python -m venv .venv
(Esto crea la carpeta oculta .venv en UIII_Panadería_0501.)  
6 — Procedimiento para activar el entorno virtual  
En Windows PowerShell:  
code  
Powershell  

    .venv\Scripts\Activate.ps1
En Windows (cmd):  
code  
Cmd   

    .venv\Scripts\activate
En macOS / Linux:  
code  
Bash  
source .venv/bin/activate  
7 — Procedimiento para activar intérprete de Python en VS Code  
En VS Code, abre la paleta de comandos (Ctrl+Shift+P).  
Escribe Python: Select Interpreter  
Selecciona el que apunta a .../UIII_Panadería_0501/.venv/bin/python (o equivalente en Windows .venv\Scripts\python.exe  
8 — Procedimiento para instalar Django  
Con el entorno virtual activado:  
code  
Bash
pip install --upgrade pip
pip install django
9 — Procedimiento para crear proyecto backend_Panaderia sin duplicar carpeta
Para evitar que Django cree una carpeta anidada con el mismo nombre, ejecuta (asegúrate de estar en la raíz del proyecto UIII_Panadería_0501):
code
Bash
django-admin startproject backend_Panaderia .
Nota: El punto . hace que los archivos de proyecto estén directamente en la carpeta actual y evita crear UIII_Panadería_0501/backend_Panaderia/backend_Panaderia.
10 — Procedimiento para ejecutar servidor en el puerto 8014
Asegúrate de estar en la raíz del proyecto (UIII_Panadería_0501) y con el entorno virtual .venv activado:
code
Bash
python manage.py runserver 8014
Esto iniciará el servidor de desarrollo en http://127.0.0.1:8014/.
11 — Procedimiento para copiar y pegar el link en el navegador
Abre tu navegador web y pega: http://127.0.0.1:8014/ (o el link que muestre la consola).
12 — Procedimiento para crear aplicación app_Panaderia
Con el entorno virtual activado y en la raíz del proyecto (UIII_Panadería_0501):
code
Bash

    python manage.py startapp app_Panaderia
13 — models.py (Modelo: Ingrediente)
Crea/edita app_Panaderia/models.py con el siguiente contenido. Por ahora, solo usaremos el modelo Ingrediente.
code
Python
# app_Panaderia/models.py
from django.db import models

# ==========================================
# MODELO: Ingrediente
# ==========================================
    class Ingrediente(models.Model):
        nombre = models.CharField(max_length=100)
        descripcion = models.TextField(blank=True, null=True)
        unidad_medida = models.CharField(max_length=50)
        costo_unitario = models.DecimalField(max_digits=10, decimal_places=2)
        proveedor = models.CharField(max_length=100, blank=True, null=True)
        fecha_compra = models.DateField()
        stock_disponible = models.DecimalField(max_digits=10, decimal_places=2)

    def __str__(self):
        return self.nombre

    class Meta:
        verbose_name = "Ingrediente"
        verbose_name_plural = "Ingredientes"
# ==========================================
 MODELO: Receta (Pendiente por ahora)

     class Receta(models.Model):
         nombre = models.CharField(max_length=100)
         descripcion = models.TextField(blank=True, null=True)
         tiempo_preparacion = models.IntegerField(help_text="Tiempo en minutos")
         fecha_creacion = models.DateField()
         autor = models.CharField(max_length=100)
         ingredientes = models.ManyToManyField(Ingrediente, related_name='recetas')

     def __str__(self):
         return self.nombre

     class Meta:
         verbose_name = "Receta"
        verbose_name_plural = "Recetas"
# # ==========================================
# MODELO: Producto (Pendiente por ahora)

     class Producto(models.Model):
         nombre = models.CharField(max_length=100)
         descripcion = models.TextField(blank=True, null=True)
         precio_venta = models.DecimalField(max_digits=10, decimal_places=2)
         fecha_elaboracion = models.DateField()
         fecha_vencimiento = models.DateField()
         receta = models.ForeignKey(Receta, on_delete=models.CASCADE, related_name='productos')

     def __str__(self):
         return f"{self.nombre} (${self.precio_venta})"

     class Meta:
         verbose_name = "Producto"
         verbose_name_plural = "Productos"
14 — Procedimiento para realizar las migraciones (makemigrations y migrate)
Con el entorno activado y en la raíz del proyecto:
code
Bash

    python manage.py makemigrations
    python manage.py migrate
15 — En views.py de app_Panaderia crear las funciones con sus códigos correspondientes
Crea/edita app_Panaderia/views.py con las siguientes funciones para el modelo Ingrediente, sin usar forms.py y manejando POST manualmente:
code
Python
app_Panaderia/views.py

    from django.shortcuts import render, redirect, get_object_or_404
    from .models import Ingrediente
    from django.urls import reverse
    from django.utils import timezone

    def inicio_panaderia(request):
        # Página principal de la app
        # Obtener el año actual para el footer
        now = timezone.now()
        return render(request, 'inicio.html', {'now': now})

    def agregar_ingrediente(request):
        if request.method == 'POST':
            nombre = request.POST.get('nombre', '').strip()
            descripcion = request.POST.get('descripcion', '').strip() or None
            unidad_medida = request.POST.get('unidad_medida', '').strip()
            costo_unitario = request.POST.get('costo_unitario')
            proveedor = request.POST.get('proveedor', '').strip() or None
            fecha_compra = request.POST.get('fecha_compra')
            stock_disponible = request.POST.get('stock_disponible')

        ingrediente = Ingrediente(
            nombre=nombre,
            descripcion=descripcion,
            unidad_medida=unidad_medida,
            costo_unitario=costo_unitario,
            proveedor=proveedor,
            fecha_compra=fecha_compra,
            stock_disponible=stock_disponible
        )
        ingrediente.save()
        return redirect('ver_ingredientes')
    return render(request, 'ingredientes/agregar_ingrediente.html', {})
    
    def ver_ingredientes(request):
        ingredientes = Ingrediente.objects.all().order_by('nombre')
        return render(request, 'ingredientes/ver_ingredientes.html', {'ingredientes': ingredientes})
    
    def actualizar_ingrediente(request, ingrediente_id):
        ingrediente = get_object_or_404(Ingrediente, id=ingrediente_id)
        return render(request, 'ingredientes/actualizar_ingrediente.html', {'ingrediente': ingrediente})
    
    def realizar_actualizacion_ingrediente(request, ingrediente_id):
        ingrediente = get_object_or_404(Ingrediente, id=ingrediente_id)
        if request.method == 'POST':
            ingrediente.nombre = request.POST.get('nombre', ingrediente.nombre).strip()
            ingrediente.descripcion = request.POST.get('descripcion', ingrediente.descripcion).strip() or None
            ingrediente.unidad_medida = request.POST.get('unidad_medida', ingrediente.unidad_medida).strip()
            ingrediente.costo_unitario = request.POST.get('costo_unitario', ingrediente.costo_unitario)
            ingrediente.proveedor = request.POST.get('proveedor', ingrediente.proveedor).strip() or None
            ingrediente.fecha_compra = request.POST.get('fecha_compra', ingrediente.fecha_compra)
            ingrediente.stock_disponible = request.POST.get('stock_disponible', ingrediente.stock_disponible)
            ingrediente.save()
            return redirect('ver_ingredientes')
        # Si se accede por GET, redirigir al formulario de edición
        return redirect('actualizar_ingrediente', ingrediente_id=ingrediente.id)
    
    def borrar_ingrediente(request, ingrediente_id):
        ingrediente = get_object_or_404(Ingrediente, id=ingrediente_id)
        if request.method == 'POST':
            ingrediente.delete()
            return redirect('ver_ingredientes')
        return render(request, 'ingredientes/borrar_ingrediente.html', {'ingrediente': ingrediente})
16 — Crear la estructura de carpetas templates y ingredientes
Crea la siguiente estructura de carpetas y archivos dentro de app_Panaderia/:
code
Code

    app_Panaderia/
        templates/
            base.html
            navbar.html
            footer.html
            inicio.html
            ingredientes/
                agregar_ingrediente.html
                ver_ingredientes.html
                actualizar_ingrediente.html
                borrar_ingrediente.html
   Puedes crear esto con VS Code o en la terminal (desde la raíz del proyecto UIII_Panadería_0501):
   
    code
    Bash
    mkdir -p app_Panaderia/templates/ingredientes
    touch app_Panaderia/templates/base.html \
          app_Panaderia/templates/navbar.html \
          app_Panaderia/templates/footer.html \
          app_Panaderia/templates/inicio.html
    touch app_Panaderia/templates/ingredientes/agregar_ingrediente.html \
          app_Panaderia/templates/ingredientes/ver_ingredientes.html \
          app_Panaderia/templates/ingredientes/actualizar_ingrediente.html \
          app_Panaderia/templates/ingredientes/borrar_ingrediente.html
(No es necesario header.html ya que navbar.html y base.html cubren esa funcionalidad en la plantilla inicial).
17 — base.html (Agregar Bootstrap para CSS y JS)
Edita app_Panaderia/templates/base.html:
code
Html

    <!doctype html>
    <html lang="es">
    <head>
      <meta charset="utf-8">
      <meta name="viewport" content="width=device-width,initial-scale=1">
      <title>{% block title %}Sistema Panadería{% endblock %}</title>
      <!-- Bootstrap CSS (CDN) -->
      <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
      <!-- Bootstrap Icons CDN -->
      <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css">
      <style>
        /* Colores suaves y modernos */
        body { background: #fdf5e6; color: #4a2c2a; } /* Tonos crema y marrón suave */
        .navbar { background-color: #ffffff; border-bottom: 1px solid #e0d9cd; }
        .navbar-brand { font-weight: 700; color: #8b4513; } /* Marrón chocolate */
        .nav-link { color: #5a3c3b; }
        .nav-link:hover { color: #8b4513; }
        .btn-primary { background-color: #8b4513; border-color: #8b4513; }
        .btn-primary:hover { background-color: #a0522d; border-color: #a0522d; }
        .btn-success { background-color: #5cb85c; border-color: #5cb85c; }
        .btn-info { background-color: #5bc0de; border-color: #5bc0de; }
        .btn-danger { background-color: #d9534f; border-color: #d9534f; }
        .btn-secondary { background-color: #6c757d; border-color: #6c757d; }
        footer { position: fixed; bottom: 0; left: 0; right: 0; background:#fff; padding:8px 16px; border-top:1px solid #e6e9ec; color: #5a3c3b;}
        .main-container { padding-bottom: 70px; } /* espacio para footer fijo */
      </style>
      {% block extra_head %}{% endblock %}
    </head>
    <body>
      {% include 'navbar.html' %}
      <div class="container main-container mt-4">
        {% block content %}{% endblock %}
      </div>
    
      {% include 'footer.html' %}
    
      <!-- Bootstrap JS (CDN) -->
      <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
      {% block extra_js %}{% endblock %}
    </body>
    </html>
18 — navbar.html (Incluir opciones con iconos)
    Edita app_Panaderia/templates/navbar.html:
code
Html
    
    <nav class="navbar navbar-expand-lg navbar-light bg-white shadow-sm">
      <div class="container">
        <a class="navbar-brand" href="{% url 'inicio_panaderia' %}">🍞 Sistema de Administración Panadería</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navMenu">
          <ul class="navbar-nav ms-auto">
            <li class="nav-item"><a class="nav-link" href="{% url 'inicio_panaderia' %}"><i class="bi bi-house-fill"></i> Inicio</a></li>
    
            <li class="nav-item dropdown">
              <a class="nav-link dropdown-toggle" href="#" id="ingredientesDrop" role="button" data-bs-toggle="dropdown">
                <i class="bi bi-egg-fried"></i> Ingredientes
              </a>
              <ul class="dropdown-menu" aria-labelledby="ingredientesDrop">
                <li><a class="dropdown-item" href="{% url 'agregar_ingrediente' %}">Agregar Ingrediente</a></li>
                <li><a class="dropdown-item" href="{% url 'ver_ingredientes' %}">Ver Ingredientes</a></li>
                <!-- Los enlaces de actualizar y borrar se manejan en la tabla de ver ingredientes -->
                <!-- <li><a class="dropdown-item" href="#">Actualizar Ingrediente</a></li> -->
                <!-- <li><a class="dropdown-item" href="#">Borrar Ingrediente</a></li> -->
              </ul>
            </li>
    
            <li class="nav-item dropdown">
              <a class="nav-link dropdown-toggle" href="#" id="recetasDrop" role="button" data-bs-toggle="dropdown">
                <i class="bi bi-journal-text"></i> Recetas
              </a>
              <ul class="dropdown-menu" aria-labelledby="recetasDrop">
                <li><a class="dropdown-item" href="#">Agregar Receta</a></li>
                <li><a class="dropdown-item" href="#">Ver Recetas</a></li>
                <li><a class="dropdown-item" href="#">Actualizar Receta</a></li>
                <li><a class="dropdown-item" href="#">Borrar Receta</a></li>
              </ul>
            </li>
    
            <li class="nav-item dropdown">
              <a class="nav-link dropdown-toggle" href="#" id="productosDrop" role="button" data-bs-toggle="dropdown">
                <i class="bi bi-bag"></i> Productos
              </a>
              <ul class="dropdown-menu" aria-labelledby="productosDrop">
                <li><a class="dropdown-item" href="#">Agregar Producto</a></li>
                <li><a class="dropdown-item" href="#">Ver Productos</a></li>
                <li><a class="dropdown-item" href="#">Actualizar Producto</a></li>
                <li><a class="dropdown-item" href="#">Borrar Producto</a></li>
              </ul>
            </li>
          </ul>
        </div>
      </div>
    </nav>
19 — footer.html (Incluir derechos de autor y mantenerlo fijo)
Edita app_Panaderia/templates/footer.html:
code
Html

    <footer class="text-center">
      <div class="container">
        <small>
          &copy; {{ now|date:"Y" }} Derechos reservados. Creado por Técn. Eliseo García, Cbtis 128.
        </small>
      </div>
    </footer>
    Nota: {{ now|date:"Y" }} funcionará porque timezone.now() se pasa desde la vista inicio_panaderia.
20 — inicio.html (Información del sistema y una imagen de panadería)
    Edita app_Panaderia/templates/inicio.html:
    code
    Html
    
    {% extends 'base.html' %}
    {% block title %}Inicio - Panadería{% endblock %}
    {% block content %}
    <div class="row align-items-center">
      <div class="col-md-7">
        <h1 class="display-4">Bienvenido al sistema de administración de Panadería</h1>
        <p class="lead">Gestiona tus ingredientes, recetas y productos de manera eficiente. Usa el menú superior para navegar por las diferentes secciones del sistema.</p>
        <hr class="my-4">
        <p>Empieza gestionando tus ingredientes para asegurar que siempre tengas lo necesario para tus deliciosas creaciones.</p>
        <a href="{% url 'ver_ingredientes' %}" class="btn btn-primary btn-lg">Ver Ingredientes</a>
      </div>
      <div class="col-md-5">
        <img src="https://images.unsplash.com/photo-1588636100913-9037c7891cf1?w=800&q=80" class="img-fluid rounded shadow-sm" alt="Panadería artesanal">
      </div>
    </div>
    {% endblock %}
    (Imagen tomada desde la red — URL pública de Unsplash.)
21 — Templates de Ingredientes (sin validación)
    Crea los siguientes archivos dentro de app_Panaderia/templates/ingredientes/.
    agregar_ingrediente.html
code
Html
    
    {% extends 'base.html' %}
    {% block title %}Agregar Ingrediente{% endblock %}
    {% block content %}
    <div class="card shadow-sm p-4">
      <h2 class="card-title mb-4">Agregar Nuevo Ingrediente</h2>
      <form method="post">
        {% csrf_token %}
        <div class="mb-3">
          <label for="nombre" class="form-label">Nombre</label>
          <input type="text" name="nombre" id="nombre" class="form-control" required>
        </div>
        <div class="mb-3">
          <label for="descripcion" class="form-label">Descripción</label>
          <textarea name="descripcion" id="descripcion" class="form-control"></textarea>
        </div>
        <div class="mb-3">
          <label for="unidad_medida" class="form-label">Unidad de Medida</label>
          <input type="text" name="unidad_medida" id="unidad_medida" class="form-control" required>
        </div>
        <div class="mb-3">
          <label for="costo_unitario" class="form-label">Costo Unitario</label>
          <input type="number" name="costo_unitario" id="costo_unitario" class="form-control" step="0.01" required>
        </div>
        <div class="mb-3">
          <label for="proveedor" class="form-label">Proveedor</label>
          <input type="text" name="proveedor" id="proveedor" class="form-control">
        </div>
        <div class="mb-3">
          <label for="fecha_compra" class="form-label">Fecha de Compra</label>
          <input type="date" name="fecha_compra" id="fecha_compra" class="form-control" required>
        </div>
        <div class="mb-3">
          <label for="stock_disponible" class="form-label">Stock Disponible</label>
          <input type="number" name="stock_disponible" id="stock_disponible" class="form-control" step="0.01" required>
        </div>
        <button type="submit" class="btn btn-primary me-2">Guardar Ingrediente</button>
        <a href="{% url 'ver_ingredientes' %}" class="btn btn-secondary">Cancelar</a>
      </form>
    </div>
    {% endblock %}
ver_ingredientes.html
code
Html
    
    {% extends 'base.html' %}
    {% block title %}Ver Ingredientes{% endblock %}
    {% block content %}
    <div class="card shadow-sm p-4">
      <h2 class="card-title mb-4">Lista de Ingredientes</h2>
      <a href="{% url 'agregar_ingrediente' %}" class="btn btn-success mb-3"><i class="bi bi-plus-circle"></i> Agregar Ingrediente</a>
      {% if ingredientes %}
      <div class="table-responsive">
        <table class="table table-striped table-hover">
          <thead>
            <tr>
              <th>Nombre</th>
              <th>Unidad de Medida</th>
              <th>Costo Unitario</th>
              <th>Proveedor</th>
              <th>Fecha de Compra</th>
              <th>Stock Disponible</th>
              <th>Acciones</th>
            </tr>
          </thead>
          <tbody>
            {% for ingrediente in ingredientes %}
            <tr>
              <td>{{ ingrediente.nombre }}</td>
              <td>{{ ingrediente.unidad_medida }}</td>
              <td>${{ ingrediente.costo_unitario }}</td>
              <td>{{ ingrediente.proveedor|default:"N/A" }}</td>
              <td>{{ ingrediente.fecha_compra }}</td>
              <td>{{ ingrediente.stock_disponible }}</td>
              <td>
                <a class="btn btn-sm btn-info me-1" href="{% url 'actualizar_ingrediente' ingrediente.id %}"><i class="bi bi-pencil-square"></i> Editar</a>
                <a class="btn btn-sm btn-danger" href="{% url 'borrar_ingrediente' ingrediente.id %}"><i class="bi bi-trash"></i> Borrar</a>
              </td>
            </tr>
            {% endfor %}
          </tbody>
        </table>
      </div>
      {% else %}
      <div class="alert alert-info mt-3" role="alert">
        No hay ingredientes registrados. ¡Empieza agregando uno!
      </div>
      {% endif %}
    </div>
    {% endblock %}
actualizar_ingrediente.html
    code
    Html
    
    {% extends 'base.html' %}
    {% block title %}Actualizar Ingrediente{% endblock %}
    {% block content %}
    <div class="card shadow-sm p-4">
      <h2 class="card-title mb-4">Editar Ingrediente</h2>
      <form method="post" action="{% url 'realizar_actualizacion_ingrediente' ingrediente.id %}">
        {% csrf_token %}
        <div class="mb-3">
          <label for="nombre" class="form-label">Nombre</label>
          <input type="text" name="nombre" id="nombre" value="{{ ingrediente.nombre }}" class="form-control" required>
        </div>
        <div class="mb-3">
          <label for="descripcion" class="form-label">Descripción</label>
          <textarea name="descripcion" id="descripcion" class="form-control">{{ ingrediente.descripcion|default:"" }}</textarea>
        </div>
        <div class="mb-3">
          <label for="unidad_medida" class="form-label">Unidad de Medida</label>
          <input type="text" name="unidad_medida" id="unidad_medida" value="{{ ingrediente.unidad_medida }}" class="form-control" required>
        </div>
        <div class="mb-3">
          <label for="costo_unitario" class="form-label">Costo Unitario</label>
          <input type="number" name="costo_unitario" id="costo_unitario" value="{{ ingrediente.costo_unitario }}" class="form-control" step="0.01" required>
        </div>
        <div class="mb-3">
          <label for="proveedor" class="form-label">Proveedor</label>
          <input type="text" name="proveedor" id="proveedor" value="{{ ingrediente.proveedor|default:"" }}" class="form-control">
        </div>
        <div class="mb-3">
          <label for="fecha_compra" class="form-label">Fecha de Compra</label>
          <input type="date" name="fecha_compra" id="fecha_compra" value="{{ ingrediente.fecha_compra|date:'Y-m-d' }}" class="form-control" required>
        </div>
        <div class="mb-3">
          <label for="stock_disponible" class="form-label">Stock Disponible</label>
          <input type="number" name="stock_disponible" id="stock_disponible" value="{{ ingrediente.stock_disponible }}" class="form-control" step="0.01" required>
        </div>
        <button type="submit" class="btn btn-primary me-2">Actualizar Ingrediente</button>
        <a href="{% url 'ver_ingredientes' %}" class="btn btn-secondary">Cancelar</a>
      </form>
    </div>
    {% endblock %}
 borrar_ingrediente.html
    code
    Html
    
    {% extends 'base.html' %}
    {% block title %}Borrar Ingrediente{% endblock %}
    {% block content %}
    <div class="card shadow-sm p-4">
      <h2 class="card-title mb-4">Confirmar Borrado de Ingrediente</h2>
      <div class="alert alert-warning" role="alert">
        <p>¿Estás seguro de que deseas eliminar el ingrediente <strong>{{ ingrediente.nombre }}</strong>?</p>
        <p>¡Esta acción es irreversible!</p>
      </div>
      <form method="post">
        {% csrf_token %}
        <button type="submit" class="btn btn-danger me-2"><i class="bi bi-trash"></i> Sí, Eliminar</button>
        <a href="{% url 'ver_ingredientes' %}" class="btn btn-secondary"><i class="bi bi-x-circle"></i> Cancelar</a>
      </form>
    </div>
    {% endblock %}
22 — Procedimiento para crear el archivo urls.py en app_Panaderia
    
Crea app_Panaderia/urls.py:

Python
    
    from django.urls import path
    from . import views
    
    urlpatterns = [
        path('', views.inicio_panaderia, name='inicio_panaderia'),
        path('ingredientes/agregar/', views.agregar_ingrediente, name='agregar_ingrediente'),
        path('ingredientes/', views.ver_ingredientes, name='ver_ingredientes'),
        path('ingredientes/editar/<int:ingrediente_id>/', views.actualizar_ingrediente, name='actualizar_ingrediente'),
        path('ingredientes/editar/guardar/<int:ingrediente_id>/', views.realizar_actualizacion_ingrediente, name='realizar_actualizacion_ingrediente'),
        path('ingredientes/borrar/<int:ingrediente_id>/', views.borrar_ingrediente, name='borrar_ingrediente'),
    ]
23 — Procedimiento para agregar app_Panaderia en settings.py de backend_Panaderia
En backend_Panaderia/settings.py, en la lista INSTALLED_APPS, añade 'app_Panaderia':
code
Python
# backend_Panaderia/settings.py

    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'app_Panaderia', # <-- ¡Añadir esta línea!
    ]
Además, asegúrate de que 'django.template.context_processors.request' esté en TEMPLATES['OPTIONS']['context_processors'] para que el now en el footer funcione correctamente (normalmente viene por defecto):
code
Python
# backend_Panaderia/settings.py
    TEMPLATES = [
        {
            # ...
            'OPTIONS': {
                'context_processors': [
                    'django.template.context_processors.debug',
                    'django.template.context_processors.request', # Asegúrate de que esta línea esté presente
                    'django.contrib.auth.context_processors.auth',
                    'django.contrib.messages.context_processors.messages',
                ],
            },
        },
    ]
24 — Realizar las configuraciones correspondiente a urls.py de backend_Panaderia para enlazar con app_Panaderia
Edita backend_Panaderia/urls.py:
code
Python
# backend_Panaderia/urls.py
    from django.contrib import admin
    from django.urls import path, include
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('', include('app_Panaderia.urls')), # Ruta raíz para la app de Panadería
    ]
25 — Procedimiento para registrar los modelos en admin.py y volver a realizar las migraciones
Edita app_Panaderia/admin.py para registrar el modelo Ingrediente:
code
Python
# app_Panaderia/admin.py
    from django.contrib import admin
    from .models import Ingrediente # Solo Ingrediente por ahora
    
    @admin.register(Ingrediente)
    class IngredienteAdmin(admin.ModelAdmin):
        list_display = ('nombre', 'unidad_medida', 'costo_unitario', 'proveedor', 'fecha_compra', 'stock_disponible')
        search_fields = ('nombre', 'proveedor')
        list_filter = ('unidad_medida', 'proveedor', 'fecha_compra')
        date_hierarchy = 'fecha_compra' # Para una navegación por fechas en el admin
Después de registrar el modelo en admin.py, es una buena práctica (aunque no estrictamente necesario si no has modificado el modelo en sí) volver a realizar las migraciones para asegurarte de que todo esté sincronizado.
code
Bash

    python manage.py makemigrations app_Panaderia
    python manage.py migrate
26 — Crear superusuario (opcional para probar admin)
Si aún no tienes un superusuario, créalo para acceder al panel de administración de Django:
code
Bash

    python manage.py createsuperuser
Sigue las instrucciones en la terminal (nombre de usuario, email, contraseña).
27 — Proyecto totalmente funcional y ejecución final
Con todas las instrucciones anteriores, el proyecto debería estar totalmente funcional para la gestión de Ingredientes:
Se crea la app app_Panaderia.
CRUD básico para Ingredientes implementado.
Templates listos con Bootstrap y diseño moderno.
Panel de administración con el modelo Ingrediente registrado.
Servidor listo para ejecutarse en el puerto 8014.
**28 — Finalmente,
Use Arrow Up and Arrow Down to select a turn, Enter to jump to it, and Escape to return to the chat.
