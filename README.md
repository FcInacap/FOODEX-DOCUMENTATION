# FOODEX-DOCUMENTATION

#  Documentación de Avance del Proyecto FOODEX  
**Fase:** Entrega Inicial / Definición de Alcance  
**Periodo Cubierto:** Semanas 1 a 3  
**Documentador Responsable:** 

Backend oficial del proyecto FOODEX, un sistema académico para la gestión de recetas, ingredientes, técnicas, etapas, talleres y usuarios dentro de un entorno gastronómico educativo.
Construido con Django + Django REST Framework, conectado a PostgreSQL en Azure y documentado con Swagger.

Características Principales

API REST modular y segura

Autenticación con JWT (SimpleJWT)

Conexión a base de datos externa PostgreSQL

Endpoints limpios y estandarizados

Documentación automática (Swagger / Redoc)

Control de acceso por roles (Administrador / Profesor / Alumno)

Seeds automáticos para roles, semestres y talleres

Basado en ModelViewSet para CRUD escalables

Estructura del Proyecto
ProyectoIntegrado_Back/
│── core/
│   ├── models/                 # Modelos conectados a BD Azure
│   ├── serializers/            # Serializadores DRF
│   ├── views/                  # Lógica de endpoints (ViewSets)
│   ├── permissions.py          # Control de acceso por roles
│   ├── seeds.py                # Datos iniciales (roles, semestres, talleres)
│   ├── signals.py              # Ejecuta seeds después de migraciones
│   ├── swagger_urls.py         # Rutas de Swagger/Redoc
│   ├── urls.py                 # Enrutamiento interno del módulo
│   └── admin.py                # Registro de modelos en Django Admin
│
│── foodex/
│   ├── asgi.py                 # Configuración ASGI
│   ├── settings.py             # Configuración principal (BD, JWT, CORS)
│   ├── urls.py                 # Rutas globales: admin, JWT, Swagger, API
│   └── wsgi.py                 # Despliegue clásico WSGI
│
│── manage.py                   # Punto de entrada del proyecto Django
│── requirements.txt            # Dependencias del backend
│── README.md                   # Documentación principal

Autenticación

La API usa JWT.

Obtener token

POST /api/token/


Refrescar token

POST /api/token/refresh/


Enviar token

Authorization: Bearer <token>

Documentación API

Disponible automáticamente:

Swagger UI:
/api/docs/

Redoc:
/api/redoc/

JSON Schema:
/api/swagger.json

Endpoints Principales
| Endpoint	| Método	| Función |
| --------	| -----------	| ----------------- |
| /api/v1/usuarios |	GET / POST	| Listar o crear usuarios |
| /api/v1/roles/ |	GET	| Listar roles |
| /api/v1/recetas/ |	GET / POST |	CRUD de recetas |
| /api/v1/ingredientes/ |	GET / POST |	CRUD de ingredientes | 
| /api/v1/etapas/ |	GET / POST |	CRUD de etapas |
| /api/v1/tecnicas/ |	GET / POST |	CRUD de técnicas |
| /api/v1/talleres/	| GET / POST |	CRUD de talleres |
| /api/v1/semestres/	| GET / POST |	CRUD de semestres |

Roles y Permisos:
| Rol	| Permisos |
| ------	| ------	|
| Administrador |	CRUD completo |
| Profesor	| Lectura + creación/edición limitada |
| Alumno	| Solo lectura |

Declarados en permissions.py.

Instalación
1) Clonar repositorio
git clone <url>
cd ProyectoIntegrado_Back

2) Crear entorno virtual
python -m venv venv
venv\Scripts\activate    # Windows

3) Instalar dependencias
pip install -r requirements.txt

4) Configurar variables de entorno

Crear archivo .env:

DJANGO_SECRET_KEY=xxxxx
DB_NAME=xxxx
DB_USER=xxxx
DB_PASS=xxxx
DB_HOST=xxxx
DB_PORT=5432
DB_SSLMODE=require

5) Ejecutar servidor
python manage.py runserver

Seeds Automáticos

Cada vez que se ejecuta una migración, se crean automáticamente:

Roles: Administrador, Profesor, Alumno

Semestres iniciales

Talleres base

Definido en:
✔ core/seeds.py
✔ core/signals.py

Cambios Técnicos Recientes

Eliminación de Channels y WebSockets (no usados en producción)

Limpieza completa de archivos duplicados (User/Usuario, Role/Rol, etc.)

Normalización de serializers y ViewSets

Estándar final de rutas con DefaultRouter

Corrección de seeds y duplicados de roles

Reestructuración del módulo core en formato profesional

Eliminación de canasta (fuera del alcance real del sistema)

Estado Actual

✔ Backend funcional

✔ Endpoints estandarizados

✔ BD compatible con Azure

✔ Swagger operativo

✔ Seguridad JWT activa

Pendiente: controles de seguridad adicionales (validaciones ampliadas)
