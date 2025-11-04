Proyecto Panadería - Django
Te ayudo a crear el proyecto completo de la panadería paso a paso. Voy a crear toda la estructura de archivos y código necesario.

Estructura completa del proyecto:
text
UIII_Panadería_0501/
├── .venv/
├── backend_Panaderia/
│   ├── backend_Panaderia/
│   │   ├── __init__.py
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── wsgi.py
│   └── manage.py
├── app_Panaderia/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   ├── views.py
│   ├── urls.py
│   └── templates/
│       ├── app_Panaderia/
│       │   ├── base.html
│       │   ├── header.html
│       │   ├── navbar.html
│       │   ├── footer.html
│       │   ├── inicio.html
│       │   └── ingredientes/
│       │       ├── agregar_ingrediente.html
│       │       ├── ver_ingredientes.html
│       │       ├── actualizar_ingrediente.html
│       │       └── borrar_ingrediente.html
Procedimientos paso a paso:
1. Crear carpeta del proyecto
bash
mkdir UIII_Panadería_0501
cd UIII_Panadería_0501
2. Abrir VS Code en la carpeta
bash
code .
3-8. Configuración inicial del entorno
En la terminal de VS Code:

bash
# 3. Abrir terminal en VS Code (Ctrl+`)

# 4. Crear entorno virtual
python -m venv .venv

# 5. Activar entorno virtual
# Windows:
.venv\Scripts\activate
# Linux/Mac:
source .venv/bin/activate

# 6. Activar intérprete de Python (seleccionar el del entorno virtual en VS Code)

# 7. Instalar Django
pip install django

# 8. Crear proyecto Django
django-admin startproject backend_Panaderia .
9-10. Ejecutar servidor
bash
# 9. Ejecutar servidor en puerto 8014
python manage.py runserver 8014

# 10. Copiar y pegar en navegador: http://127.0.0.1:8014/
11. Crear aplicación
bash
python manage.py startapp app_Panaderia
Archivos de código:
12. models.py (app_Panaderia/models.py)
python
from django.db import models

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
12.5 Realizar migraciones
bash
python manage.py makemigrations
python manage.py migrate
14. views.py (app_Panaderia/views.py)
python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Ingrediente

def inicio_panaderia(request):
    return render(request, 'app_Panaderia/inicio.html')

def agregar_ingrediente(request):
    if request.method == 'POST':
        Ingrediente.objects.create(
            nombre=request.POST['nombre'],
            descripcion=request.POST['descripcion'],
            unidad_medida=request.POST['unidad_medida'],
            costo_unitario=request.POST['costo_unitario'],
            proveedor=request.POST['proveedor'],
            fecha_compra=request.POST['fecha_compra'],
            stock_disponible=request.POST['stock_disponible']
        )
        return redirect('ver_ingredientes')
    return render(request, 'app_Panaderia/ingredientes/agregar_ingrediente.html')

def ver_ingredientes(request):
    ingredientes = Ingrediente.objects.all()
    return render(request, 'app_Panaderia/ingredientes/ver_ingredientes.html', {'ingredientes': ingredientes})

def actualizar_ingrediente(request, id):
    ingrediente = get_object_or_404(Ingrediente, id=id)
    return render(request, 'app_Panaderia/ingredientes/actualizar_ingrediente.html', {'ingrediente': ingrediente})

def realizar_actualizacion_ingrediente(request, id):
    if request.method == 'POST':
        ingrediente = get_object_or_404(Ingrediente, id=id)
        ingrediente.nombre = request.POST['nombre']
        ingrediente.descripcion = request.POST['descripcion']
        ingrediente.unidad_medida = request.POST['unidad_medida']
        ingrediente.costo_unitario = request.POST['costo_unitario']
        ingrediente.proveedor = request.POST['proveedor']
        ingrediente.fecha_compra = request.POST['fecha_compra']
        ingrediente.stock_disponible = request.POST['stock_disponible']
        ingrediente.save()
        return redirect('ver_ingredientes')
    return redirect('ver_ingredientes')

def borrar_ingrediente(request, id):
    ingrediente = get_object_or_404(Ingrediente, id=id)
    if request.method == 'POST':
        ingrediente.delete()
        return redirect('ver_ingredientes')
    return render(request, 'app_Panaderia/ingredientes/borrar_ingrediente.html', {'ingrediente': ingrediente})
17. base.html (app_Panaderia/templates/app_Panaderia/base.html)
html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema Panadería</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            background-color: #f8f9fa;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        .navbar {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        .sidebar {
            background-color: #ffffff;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        .main-content {
            min-height: calc(100vh - 120px);
            padding: 20px;
        }
        .footer {
            background-color: #343a40;
            color: white;
            padding: 15px 0;
            margin-top: auto;
        }
        .card {
            border: none;
            border-radius: 15px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            transition: transform 0.3s ease;
        }
        .card:hover {
            transform: translateY(-5px);
        }
        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border: none;
        }
    </style>
</head>
<body>
    {% include 'app_Panaderia/header.html' %}
    {% include 'app_Panaderia/navbar.html' %}
    
    <div class="container-fluid">
        <div class="row">
            <main class="col-md-12 main-content">
                {% block content %}
                {% endblock %}
            </main>
        </div>
    </div>
    
    {% include 'app_Panaderia/footer.html' %}

    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
18. navbar.html (app_Panaderia/templates/app_Panaderia/navbar.html)
html
<nav class="navbar navbar-expand-lg navbar-dark">
    <div class="container">
        <a class="navbar-brand" href="{% url 'inicio_panaderia' %}">
            🍞 Sistema de Administración Panadería
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav ms-auto">
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'inicio_panaderia' %}">
                        🏠 Inicio
                    </a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                        📦 Ingredientes
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="{% url 'agregar_ingrediente' %}">Agregar Ingrediente</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_ingredientes' %}">Ver Ingredientes</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                        📋 Recetas
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar Receta</a></li>
                        <li><a class="dropdown-item" href="#">Ver Recetas</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Receta</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Receta</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                        🎂 Productos
                    </a>
                    <ul class="dropdown-menu">
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
19. footer.html (app_Panaderia/templates/app_Panaderia/footer.html)
html
<footer class="footer mt-5">
    <div class="container text-center">
        <p class="mb-0">
            &copy; {% now "Y" %} Sistema Panadería - Todos los derechos reservados | 
            Fecha del sistema: {% now "d/m/Y H:i" %} | 
            Creado por Técn. Eliseo García, Cbtis 128
        </p>
    </div>
</footer>
20. inicio.html (app_Panaderia/templates/app_Panaderia/inicio.html)
html
{% extends 'app_Panaderia/base.html' %}

{% block content %}
<div class="row">
    <div class="col-md-8 mx-auto text-center">
        <div class="card">
            <div class="card-body">
                <h1 class="display-4 mb-4">🍞 Bienvenido al Sistema de Panadería</h1>
                <p class="lead">Sistema integral para la gestión de ingredientes, recetas y productos de panadería.</p>
                
                <div class="row mt-5">
                    <div class="col-md-4 mb-4">
                        <div class="card h-100">
                            <div class="card-body">
                                <h5>📦 Ingredientes</h5>
                                <p>Gestiona el inventario de ingredientes</p>
                            </div>
                        </div>
                    </div>
                    <div class="col-md-4 mb-4">
                        <div class="card h-100">
                            <div class="card-body">
                                <h5>📋 Recetas</h5>
                                <p>Administra las recetas de productos</p>
                            </div>
                        </div>
                    </div>
                    <div class="col-md-4 mb-4">
                        <div class="card h-100">
                            <div class="card-body">
                                <h5>🎂 Productos</h5>
                                <p>Controla los productos terminados</p>
                            </div>
                        </div>
                    </div>
                </div>
                
                <div class="mt-4">
                    <img src="https://images.unsplash.com/photo-1509440159596-0249088772ff?ixlib=rb-4.0.3&auto=format&fit=crop&w=1200&q=80" 
                         alt="Panadería" class="img-fluid rounded" style="max-height: 400px;">
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
22. Templates de ingredientes:
agregar_ingrediente.html
html
{% extends 'app_Panaderia/base.html' %}

{% block content %}
<div class="row">
    <div class="col-md-8 mx-auto">
        <div class="card">
            <div class="card-header">
                <h3 class="text-center">➕ Agregar Nuevo Ingrediente</h3>
            </div>
            <div class="card-body">
                <form method="POST">
                    {% csrf_token %}
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Nombre</label>
                            <input type="text" class="form-control" name="nombre" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Unidad de Medida</label>
                            <input type="text" class="form-control" name="unidad_medida" required>
                        </div>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Descripción</label>
                        <textarea class="form-control" name="descripcion" rows="3"></textarea>
                    </div>
                    <div class="row">
                        <div class="col-md-4 mb-3">
                            <label class="form-label">Costo Unitario</label>
                            <input type="number" step="0.01" class="form-control" name="costo_unitario" required>
                        </div>
                        <div class="col-md-4 mb-3">
                            <label class="form-label">Proveedor</label>
                            <input type="text" class="form-control" name="proveedor">
                        </div>
                        <div class="col-md-4 mb-3">
                            <label class="form-label">Stock Disponible</label>
                            <input type="number" step="0.01" class="form-control" name="stock_disponible" required>
                        </div>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Fecha de Compra</label>
                        <input type="date" class="form-control" name="fecha_compra" required>
                    </div>
                    <div class="text-center">
                        <button type="submit" class="btn btn-primary me-2">Guardar Ingrediente</button>
                        <a href="{% url 'ver_ingredientes' %}" class="btn btn-secondary">Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
ver_ingredientes.html
html
{% extends 'app_Panaderia/base.html' %}

{% block content %}
<div class="row">
    <div class="col-12">
        <div class="card">
            <div class="card-header d-flex justify-content-between align-items-center">
                <h3>📦 Lista de Ingredientes</h3>
                <a href="{% url 'agregar_ingrediente' %}" class="btn btn-primary">➕ Agregar Ingrediente</a>
            </div>
            <div class="card-body">
                <div class="table-responsive">
                    <table class="table table-striped table-hover">
                        <thead class="table-dark">
                            <tr>
                                <th>Nombre</th>
                                <th>Unidad</th>
                                <th>Costo Unitario</th>
                                <th>Proveedor</th>
                                <th>Stock</th>
                                <th>Acciones</th>
                            </tr>
                        </thead>
                        <tbody>
                            {% for ingrediente in ingredientes %}
                            <tr>
                                <td>{{ ingrediente.nombre }}</td>
                                <td>{{ ingrediente.unidad_medida }}</td>
                                <td>${{ ingrediente.costo_unitario }}</td>
                                <td>{{ ingrediente.proveedor|default:"-" }}</td>
                                <td>{{ ingrediente.stock_disponible }}</td>
                                <td>
                                    <a href="{% url 'actualizar_ingrediente' ingrediente.id %}" class="btn btn-warning btn-sm">✏️ Editar</a>
                                    <a href="{% url 'borrar_ingrediente' ingrediente.id %}" class="btn btn-danger btn-sm">🗑️ Borrar</a>
                                </td>
                            </tr>
                            {% empty %}
                            <tr>
                                <td colspan="6" class="text-center">No hay ingredientes registrados</td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
actualizar_ingrediente.html
html
{% extends 'app_Panaderia/base.html' %}

{% block content %}
<div class="row">
    <div class="col-md-8 mx-auto">
        <div class="card">
            <div class="card-header">
                <h3 class="text-center">✏️ Actualizar Ingrediente</h3>
            </div>
            <div class="card-body">
                <form method="POST" action="{% url 'realizar_actualizacion_ingrediente' ingrediente.id %}">
                    {% csrf_token %}
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Nombre</label>
                            <input type="text" class="form-control" name="nombre" value="{{ ingrediente.nombre }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Unidad de Medida</label>
                            <input type="text" class="form-control" name="unidad_medida" value="{{ ingrediente.unidad_medida }}" required>
                        </div>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Descripción</label>
                        <textarea class="form-control" name="descripcion" rows="3">{{ ingrediente.descripcion }}</textarea>
                    </div>
                    <div class="row">
                        <div class="col-md-4 mb-3">
                            <label class="form-label">Costo Unitario</label>
                            <input type="number" step="0.01" class="form-control" name="costo_unitario" value="{{ ingrediente.costo_unitario }}" required>
                        </div>
                        <div class="col-md-4 mb-3">
                            <label class="form-label">Proveedor</label>
                            <input type="text" class="form-control" name="proveedor" value="{{ ingrediente.proveedor }}">
                        </div>
                        <div class="col-md-4 mb-3">
                            <label class="form-label">Stock Disponible</label>
                            <input type="number" step="0.01" class="form-control" name="stock_disponible" value="{{ ingrediente.stock_disponible }}" required>
                        </div>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Fecha de Compra</label>
                        <input type="date" class="form-control" name="fecha_compra" value="{{ ingrediente.fecha_compra|date:'Y-m-d' }}" required>
                    </div>
                    <div class="text-center">
                        <button type="submit" class="btn btn-primary me-2">Actualizar Ingrediente</button>
                        <a href="{% url 'ver_ingredientes' %}" class="btn btn-secondary">Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
borrar_ingrediente.html
html
{% extends 'app_Panaderia/base.html' %}

{% block content %}
<div class="row">
    <div class="col-md-6 mx-auto">
        <div class="card">
            <div class="card-header">
                <h3 class="text-center text-danger">🗑️ Confirmar Eliminación</h3>
            </div>
            <div class="card-body text-center">
                <h5>¿Estás seguro de que deseas eliminar el ingrediente?</h5>
                <p class="lead"><strong>{{ ingrediente.nombre }}</strong></p>
                <p><strong>Unidad:</strong> {{ ingrediente.unidad_medida }}</p>
                <p><strong>Costo:</strong> ${{ ingrediente.costo_unitario }}</p>
                
                <form method="POST">
                    {% csrf_token %}
                    <div class="mt-4">
                        <button type="submit" class="btn btn-danger me-2">Sí, Eliminar</button>
                        <a href="{% url 'ver_ingredientes' %}" class="btn btn-secondary">Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
24. urls.py (app_Panaderia/urls.py)
python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_panaderia, name='inicio_panaderia'),
    path('ingredientes/agregar/', views.agregar_ingrediente, name='agregar_ingrediente'),
    path('ingredientes/', views.ver_ingredientes, name='ver_ingredientes'),
    path('ingredientes/actualizar/<int:id>/', views.actualizar_ingrediente, name='actualizar_ingrediente'),
    path('ingredientes/realizar_actualizacion/<int:id>/', views.realizar_actualizacion_ingrediente, name='realizar_actualizacion_ingrediente'),
    path('ingredientes/borrar/<int:id>/', views.borrar_ingrediente, name='borrar_ingrediente'),
]
25. settings.py (backend_Panaderia/settings.py) - AGREGAR:
python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Panaderia',  # Agregar esta línea
]
26. urls.py (backend_Panaderia/urls.py)
python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Panaderia.urls')),
]
27. admin.py (app_Panaderia/admin.py)
python
from django.contrib import admin
from .models import Ingrediente, Receta, Producto

@admin.register(Ingrediente)
class IngredienteAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'unidad_medida', 'costo_unitario', 'stock_disponible')
    list_filter = ('proveedor',)
    search_fields = ('nombre',)

@admin.register(Receta)
class RecetaAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'tiempo_preparacion', 'autor')
    filter_horizontal = ('ingredientes',)

@admin.register(Producto)
class ProductoAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'precio_venta', 'fecha_vencimiento')
    list_filter = ('fecha_elaboracion',)
31. Ejecutar servidor final
bash
python manage.py makemigrations
python manage.py migrate
python manage.py runserver 8014
El proyecto estará completamente funcional en http://127.0.0.1:8014/ con todas las operaciones CRUD para ingredientes y una interfaz moderna y atractiva.


