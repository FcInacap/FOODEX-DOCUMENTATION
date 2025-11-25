# ProyectoIntegrado_DOCS

# Documentación - Backend
**Documentador responsable:** FRANCO CICERELLI  
**Fase:** Desarrollo del Backend / 3era Entrega

## 1. Contexto general:
El Equipo Backend se centró en la lógica del sistema de manera que los usuarios, los roles,  las recetas, los ingredientes, los pasos y todo lo relacionado con la gestión interna, se estructuré para hacerlo más ordenado, escalable y fácil de mantener.

---

## 2. Objetivos del Back-End:

### 2.1 Objetivo general:
Proveer una API REST segura, ordenada y escalable para gestionar recetas, ingredientes, usuarios, roles, talleres y demás recursos del sistema Foodex.

### 2.2 Objetivos específicos:
- Gestionar usuarios y roles mediante autenticación JWT.
- Administrar recetas académicas completas con ingredientes, etapas y técnicas.
- Asegurar integración estable con PostgreSQL en Azure.
- Estandarizar documentación y pruebas mediante Swagger y DRF.
- Mantener consistencia de datos base usando seeds automáticos.
- Exponer endpoints limpios, normalizados y reutilizables para el frontend.

---

## 3. Arquitectura del Sistema 
| Componente | Tecnología |
|------------|------------|
| Lenguaje |	Python 3.10+ |
| Framework |	Django 4.2.7 |
| API |	Django REST Framework |
| Autenticación |	JWT (SimpleJWT) |
| Base de Datos |	PostgreSQL (Azure) |
| Documentación |	Swagger + drf-yasg |
| Seguridad |	CORS, SSL, Roles |
| Configuración |	Variables .env |
| Despliegue |	Compatible con Azure Web Services |

---

## 4. Estructura del Proyecto:
```
ProyectoIntegrado_Back/
│
│── core/                        # Aplicación principal del dominio Foodex
│   │
│   ├── migrations/              # Historial de migraciones (deshabilitadas en producción)
│   │
│   ├── models/                  # Mapeo ORM hacia tablas reales de PostgreSQL
│   │
│   ├── serializers/             # Serializadores DRF por entidad y relaciones
│   │
│   ├── views/                   # ViewSets REST que exponen funcionalidades de la API
│   │
│   ├── __init__.py
│   ├── admin.py                 # Registro de modelos en Django Admin
│   ├── apps.py                  # Configuración interna de la app Core
│   ├── permissions.py           # Permisos basados en roles (Admin, Profesor, Alumno)
│   ├── seeds.py                 # Datos iniciales (roles, talleres, semestres)
│   ├── signals.py               # post_migrate: ejecuta seeds automáticamente
│   ├── swagger_urls.py          # Configuración extendida para documentación Swagger
│   └── urls.py                  # Ruteo REST con DefaultRouter
│
│── foodex/                      # Configuración global del proyecto Django
│   ├── __init__.py
│   ├── asgi.py                  # Configuración ASGI (sin Channels)
│   ├── requirements.txt         # Dependencias del entorno productivo
│   ├── settings.py              # Configuración principal (DB, JWT, CORS, Swagger)
│   ├── urls.py                  # Enrutamiento raíz: admin, JWT, Swagger, API v1
│   └── wsgi.py                  # Configuración WSGI para despliegues tradicionales
│
│── .gitattributes               # Normalización de formato en repositorio
│── .gitignore                   # Archivos y rutas excluidas del control de versiones
│── CONTRIBUTING.md              # Guía oficial de colaboración del proyecto
│── README.md                    # Documentación base del repositorio en GitHub
│── manage.py                    # Punto de ejecución y CLI de Django
│── requirements.txt             # Dependencias exactas del entorno local
```

**Beneficios:**
- Código más limpio y fácil de navegar.
- Permite que varios integrantes trabajen sin generar conflictos.
- Favorece buenas prácticas de Django y DRF.
- Facilita testing, QA y documentación.

---

## 5. Roles y Permisos:

| Rol	          | Permisos                             |
| -------------	| -----------------------------------  |
| Administrador |	CRUD completo                        |
| Profesor	    | Lectura, creación y edición limitada |
| Alumno	      | Solo lectura (semestres iniciales)   |

---

## 6. Modelos Lógicos y Funcionalidad:
Cada modelo refleja una entidad real del dominio, permitiendo consultas eficientes, trazabilidad y escalabilidad.

### 6.1 Modelos Principales:
| Modelo                   | Funcionalidad                                                                        |
| ------------------------ | ------------------------------------------------------------------------------------ |
| **Usuario**              | Representa a cualquier persona dentro del sistema (administrador, profesor, alumno). |
| **Rol**                  | Define permisos y nivel de acceso — base del sistema de seguridad.                   |
| **UsuarioRol**           | Permite asignar uno o varios roles a un mismo usuario.                               |
| **Receta**               | Entidad central del sistema; describe una preparación gastronómica académica.        |
| **Ingrediente**          | Catálogo de insumos utilizados en recetas.                                           |
| **CategoriaIngrediente** | Agrupa ingredientes para organización, filtrado y control.                           |
| **Etapa**                | Define pasos secuenciales dentro de una receta.                                      |
| **Técnica**              | Representa métodos culinarios aplicados durante la preparación.                      |
| **Taller**               | Identifica el taller o sección académica al que pertenece la receta.                 |
| **Semestre**             | Indica el periodo formativo asociado al estudiante o taller.                         |
| **Unidad**               | Define unidades de medida utilizadas por ingredientes (g, ml, unidad, etc.).         |

#### 6.2 Comportamiento del Sistema
Gestión de Usuarios y Roles:

- Autenticación mediante JWT.
- Roles controlan permisos sobre endpoints.
- Usuarios pueden pertenecer a múltiples roles.

Administración de Recetas:

- CRUD completo desde la API.
- Cada receta posee ingredientes, etapas, técnicas, taller y semestre asociado.
- Endpoints permiten obtener información completa en un solo request.

Organización de Ingredientes:

- Clasificados por categoría y unidad de medida.
- Evita duplicaciones y facilita búsqueda y filtrado.

---

## 7. Endpoints Principales:

| Endpoint                | Método     | Función                             |
| ----------------------- | ---------- | ----------------------------------- |
| `/api/v1/usuarios/`     | GET / POST | Listar o crear usuarios             |
| `/api/v1/roles/`        | GET        | Listar roles                        |
| `/api/v1/recetas/`      | GET / POST | Listar o crear recetas              |
| `/api/v1/ingredientes/` | GET / POST | Listar o crear ingredientes         |
| `/api/v1/etapas/`       | GET / POST | Listar o crear etapas               |
| `/api/v1/tecnicas/`     | GET / POST | Listar o crear técnicas culinarias  |
| `/api/v1/talleres/`     | GET / POST | Listar o crear talleres             |
| `/api/v1/semestres/`    | GET / POST | Listar o crear semestres académicos |


