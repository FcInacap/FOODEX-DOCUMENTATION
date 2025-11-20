# FOODEX-DOCUMENTATION
# Documentación - Control de Calidad (QA)

**Documentador responsable:** ABRIL CASTRO  
**Fase:** Entrega Inicial / Definición de Alcance y Roles (Semana 1)

---

## 1. Estrategia y Enfoque Inicial del Equipo de QA

### ¿Por qué se definió este alcance inicial para QA?

El equipo de QA ha definido un conjunto de responsabilidades clave que serán la base para toda la fase de desarrollo del prototipo. La estrategia inicial se centra en establecer procesos y definir requisitos de calidad para los demás equipos.

| Responsabilidad Clave (¿Qué haremos?) | Decisión y Justificación (¿Cómo y Por qué en esta fase inicial?) |
|---------------------------------------|-------------------------------------------------------------------|
| Asegurar la calidad del producto final. | **Decisión:** Priorizar la definición de la calidad y el alcance de las pruebas antes de la ejecución.  **Justificación:** Asegura que todos los miembros del equipo comprendan el estándar de calidad esperado antes de escribir la primera línea de código o crear el primer diseño de base de datos. |
| Definir criterios de aceptación y casos de prueba. | **Decisión:** Los criterios de aceptación serán la primera documentación de QA.  **Justificación:** Esto establece un contrato funcional claro con Desarrollo (Back y Front) sobre lo que constituye un feature "terminado" y minimiza el riesgo de malentendidos en las historias de usuario. |
| Realizar pruebas funcionales, de integración, de interfaz y unitarias (si corresponde). | **Decisión:** En esta fase, solo se define el tipo de prueba a realizar.  **Justificación:** Se tomó la decisión de que la estrategia principal será la Integración para el MVP, mientras que las pruebas Unitarias se delegan al proceso interno de cada equipo de desarrollo. |
| Levantar y hacer seguimiento de bugs en una herramienta adecuada. | **Decisión:** Seleccionar y configurar una herramienta para el seguimiento de bugs desde el día cero.  **Justificación:** Es esencial tener un flujo de trabajo formalizado y trazable para gestionar los defectos tan pronto como comiencen a aparecer en las entregas parciales. |
| Asegurar la cobertura de pruebas antes de cada entrega parcial. | **Decisión:** Establecer el concepto de "Puerta de Calidad". **Justificación:** Esto asegura que la calidad no es una actividad de último minuto, sino un requisito continuo. Se definirá un checklist básico. |

---

## 2. Plan de Colaboración y Requisitos Iniciales de QA

### ¿Cómo se decidió colaborar con los otros equipos para esta fase de planificación?

Se definió un conjunto de requisitos de entrada para cada equipo. Estos requisitos son la base sobre la cual QA construirá sus casos de prueba, y deben ser entregados en la primera interacción.

| Equipo Asociado | Requisito Inicial de QA (¿Qué se espera?) | Justificación (¿Para qué se necesita en la planificación?) |
|-----------------|--------------------------------------------|------------------------------------------------------------|
| Base de Datos (Sergio) | Diseñar el modelo de datos; Entregar Diagramas ER y Aclarar campos obligatorios/restricciones. | **Propósito:** El E-R es fundamental. QA necesita conocer las restricciones de datos para planificar las pruebas de validación de Backend. Es un punto de partida para el diseño de pruebas de seguridad y de límite. |
| 🖥️ Backend (Franco) | Desarrollar la lógica de negocio; Implementar y entregar documentación de endpoints (e.g., Swagger). | **Propósito:** La documentación de endpoints es el contrato funcional para la integración. QA la necesita para planificar cómo se harán las pruebas headless (con herramientas como Postman, sin mostrar intefaz gráfica) en cuanto los primeros endpoints estén listos. |
| 🎨 Frontend (Jacques) | Construcción de la UI en base a prototipos; Entregar builds utilizables o entornos de staging (similar al original). | **Propósito:** La entrega de un entorno estable es el requisito básico de testing. QA necesita saber cómo accederá a la aplicación para planificar los smoke tests y las pruebas de Interfaz de Usuario. |
| 📄 Documentación | Manual de usuario y Documentación de decisiones clave. | **Propósito:** Esta documentación sirve a QA para asegurar la trazabilidad de los requisitos. Es esencial para verificar que el producto final coincida con la intención de diseño y los requisitos del stakeholder. |

---

# Documentación – Backend Foodex

**Responsable de documentación:** Franco Cicerelli  
**Fase:** Desarrollo del Backend / 2da Entrega

---

## 1. Contexto general

El backend de Foodex implementa la lógica de negocio, gestión de datos y seguridad del sistema.  
Durante la semana del 15/11/2025 se realizó una reestructuración completa orientada a mejorar modularidad, mantenibilidad y escalabilidad.

### Tecnologías principales:

| Componente | Tecnología |
|------------|------------|
| Backend | Django 5 + DRF |
| Auth | JWT (SimpleJWT) |
| Documentación | Swagger (drf-yasg) |
| Base de datos | PostgreSQL (producción) / SQLite (desarrollo) |
| WebSockets | Django Channels + Redis |
| Seeds automáticos | Signals + seeds.py |
| Control de roles | Admin / Profesor / Alumno |

---

## 2. Objetivos del Back-End

### 2.1 Objetivo general
Implementar la lógica de negocio, seguridad, roles y manejo centralizado de recetas en un entorno escalable y seguro.

### 2.2 Objetivos específicos
- Gestionar recetas, pasos, ingredientes y roles.
- Implementar endpoints REST con autenticación JWT.
- Garantizar sincronización en tiempo real mediante WebSockets.
- Centralizar información en la nube y eliminar uso de medios físicos.

---

## 3. Enfoque del Equipo Back End

| Responsabilidad | Decisión | Justificación |
|-----------------|----------|---------------|
| Gestión de recetas, pasos, ingredientes | Modelo relacional normalizado en PostgreSQL | Integridad referencial y precisión de datos |
| Comunicación en tiempo real | Django Channels + Redis | Interacción sincrónica profesor–estudiante |
| Seguridad y acceso | JWT + Roles diferenciados | Autenticación robusta y permisos segmentados |
| Arquitectura general | MVC de Django, separación de capas | Escalabilidad y mantenibilidad |

---

## 4. Arquitectura y Organización

### 4.1 Estructura general – Arquitectura MVC
- **Modelos:** estructura de datos  
- **Vistas (ViewSets):** lógica y endpoints  
- **Serializers:** conversión modelo ⇄ JSON  

### 4.2 Reestructuración del 15–18 noviembre

foodex-backend/
├── core/
│ ├── models/
│ ├── serializers/
│ ├── views/
│ ├── permissions.py
│ ├── services.py
│ ├── seeds.py
│ ├── signals.py
│ └── urls.py
│
├── foodex/
│ ├── settings.py
│ ├── urls.py
│ ├── wsgi.py
│ └── asgi.py
│
├── manage.py
└── README.md


**Beneficios:**
- Código más limpio y fácil de navegar.
- Permite que varios integrantes trabajen sin generar conflictos.
- Favorece buenas prácticas de Django y DRF.
- Facilita testing, QA y documentación.

---

## 5. Modelos del Sistema

| Modelo / Entidad | Campos clave | Relaciones | Decisiones de diseño / Rol |
|------------------|--------------|------------|-----------------------------|
| Role | id_rol, nombre_rol | 1–N con User | Gestiona roles del sistema |
| User | id_usuario, correo, nombre, apellido, rut | FK a Role | Usuario personalizado para autenticación |
| UserManager | — | — | Centraliza reglas de creación de usuarios |
| CategoriaIngrediente | id_categoria, nombre | 1–N con Ingrediente | Clasificación estándar |
| Ingrediente | id_ing, nombre, unidad, precio, calorías | FK a Categoria | Base para recetas y stock |
| Canasta | ingrediente, cantidad | FK a Ingrediente | Punto central del inventario |
| Técnica | id, nombre, descripción | N–N con Receta | Agrupa conocimientos culinarios |
| Etapa | id, nombre, descripción, tiempo | — | Pasos lógicos de recetas |
| Receta | id, nombre, tipo, tiempo, porciones | FK a User; N–N | Entidad central del sistema |
| RecetaEtapa | receta, etapa, orden | N–N | Define flujo de preparación |
| RecetaIngrediente | receta, ingrediente, cantidad | N–N | Base para cálculo y porciones |
| EtapaIngrediente | etapa, ingrediente, cantidad | N–N | Ingredientes por etapa |
| RecetaTecnica | receta, técnica | N–N | Técnicas aplicadas |

---

## 6. Inicio del Proyecto

### 6.1 Preparar entorno

python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt


### 6.2 Variables de entorno
Crear `.env`:

SECRET_KEY=super-secreto
DEBUG=1

### 6.3 Migraciones y seeds

python manage.py makemigrations
python manage.py migrate
python manage.py shell
from core.seeds import seed_roles, seed_admin
seed_roles(); seed_admin()
python manage.py createsuperuser


### 6.4 Levantar servidor

python manage.py runserver


### 6.5 Probar la API

| Herramienta | Uso |
|-------------|------|
| Swagger | http://127.0.0.1:8000/api/docs/ |
| Postman | Enviar JWT en Authorization: Bearer <token> |

Obtener token:


POST /api/v1/auth/login/
{
"correo_electronico": "admin@foodex.cl
",
"password": "admin123"
}


### Endpoints principales

| Endpoint | Método | Función |
|----------|--------|---------|
| /api/v1/usuarios/ | GET/POST | Listar o crear usuarios |
| /api/v1/roles/ | GET | Listar roles |
| /api/v1/ingredientes/ | GET/POST | CRUD ingredientes |
| /api/v1/categorias/ | GET/POST | CRUD categorías |
| /api/v1/recetas/ | GET/POST | CRUD recetas |
| /api/v1/recetas/{id}/detalle_completo/ | GET | Receta con ingredientes y etapas |
| /api/v1/recetas/{id}/recalcular | GET | Ajuste por porciones |
| /api/v1/recetas/buscar | GET | Buscar recetas |
| /api/v1/canasta/ | GET/POST | Stock ingredientes |

---

## 7. Permisos de Rol

| Rol | Permisos |
|------|----------|
| Admin | CRUD completo |
| Profesor | Lectura + crear recetas + edición limitada |
| Alumno | Solo lectura |

---

## 8. Avances Técnicos (15–18 de Noviembre)

### 8.1 Reordenamiento de vistas  
✔ Separación en archivos dentro de /views/

### 8.2 Modelos y relaciones  
✔ Corrección de relaciones  
✔ Migraciones limpias  

### 8.3 Serializers  
✔ Archivos individuales  
✔ Swagger mejorado  

### 8.4 Funcionalidades nuevas

| Función | Descripción |
|---------|-------------|
| Permisos por rol | Admin/Profesor/Alumno |
| detalle_completo | Receta con todos los datos |
| calcular | Ajustar porciones |
| filtros | Técnica/Categoría |

### 8.5 Seeds + Signals  
✔ Roles y admin automáticos  
✔ Seeds corren tras migraciones

---

## 9. Puntos realizados

| Tarea | Estado |
|-------|--------|
| Reestructuración backend | ✔ |
| Separación de modelos, serializers y vistas | ✔ |
| Corrección relaciones | ✔ |
| Endpoints avanzados | ✔ |
| Seeds + Signals | ✔ |
| Swagger | ✔ |
| Eliminación de reconciliar_con_inventario | ✔ |

---

## 10. Pendientes

| Pendiente | Descripción |
|-----------|-------------|
| Testing unificado | Validar rutas/permisos/serializers |
| Controles de seguridad | Restricciones adicionales |

---

# Documentación – Front End

**Documentador responsable:** Jacques Lapierre  
**Fase:** Entrega Inicial (Semana 1)

---

## 1. Contexto general

La sección reporta el progreso semanal del Front End del Proyecto FOODEX, orientado a tablets y gestión digital de recetas.

---

## 2. Metas semanales

| Semana | Fechas | Foco | Objetivo |
|--------|--------|--------|----------|
| 1 | 10/11–17/11 | Setup | Entorno listo |
| 2 | 17/11–24/11 | Vista Alumno | Leer recetas |
| 3 | 24/11–01/12 | Interacción | Crear/editar recetas |
| 4 | 01/12–08/12 | Vista Profesor | Sincronización |
| 5 | 08/12–15/12 | Demo final | App estable |

---

## 3. Avances Semana 1

- Mockup funcional aprobado  
- Diseño base presentado  
- Navegación entre pantallas  
- Conexión a repositorio: Pendiente

---

## 4. Pendientes Semana 2

- Finalizar mockups  
- Conexión a GitHub  
- Definir estructura JSON  
- Implementar vista Alumno  
- Validación en tablets  

---

## 5. Proyecciones Semanas 3–5
- Formularios
- Vista Profesor
- Pruebas finales
- Demo

---

## 6. Tabla de seguimiento semanal

| Semana | Foco | Avances | Pendientes | Estado |
|--------|-------|----------|-------------|---------|
| 1 | Setup | Mockup, estilo, navegación | GitHub, mockups finales | En proceso |
| 2 | Vista Alumno | Vistas lectura | JSON, pruebas | Pendiente |
| 3 | Interacción | Formularios | Pruebas | Pendiente |
| 4 | Vista Profesor | Sincronización | Ajustes visuales | Pendiente |
| 5 | Demo Final | Correcciones | — | Pendiente |

---

# Avances Equipo Front End

## 1. Contexto general
Desarrollo de interfaz responsiva enfocada en tablets, con navegación, login, dashboard y gestión de recetas.

---

## 2. Resumen ejecutivo
Avance sólido en navegación, estructura, login, dashboard, vistas de detalle y creación rápida de recetas.  
Pendientes: conexión con Back-End y validaciones finales.

---

## 3. Avances principales

### 3.1 Funcionalidades implementadas
- Login / Logout con estado global  
- Dashboard con tarjetas  
- Vista de detalle con pestañas  
- Modal de creación rápida  
- Compatibilidad validada en tablets  
- Navegación completa

---

## 4. Progreso Trello

### 4.1 Completado
- Diseño tablet  
- Navegación fluida  
- Vista detalle  
- Proyecto funcionando  
- Flujo principal

### 4.2 En proceso
- Lista de recetas  
- Optimización visual

### 4.3 Pendientes
- Conexión Back-End  
- Wireframes finales  
- Validaciones  
- Integración profesor  
- Documentación de errores  

---

## 5. Aspectos técnicos

### Arquitectura y Librerías
- Create React App  
- React 18.2  
- Tailwind CSS  
- Radix UI  
- lucide-react  
- sonner  
- react-day-picker  
- Testing Library  

### Estructura del código
- App.js → estado global  
- Dashboard.js → recetas  
- NewRecipeModal.js → nuevas recetas  
- DocViewerDialog.js → archivos  
- RecipeView.js → vista detallada  
- /ui → componentes reutilizables  

---

## 6. Pendientes críticos
- Conexión con Back-End  
- JSON final  
- Validaciones  
- Implementación de pasos/imágenes  
- Vista profesor

# Documentación – Base de Datos

## 1. Contexto General

El equipo de Base de Datos es responsable del diseño, implementación y mantenimiento del modelo de datos del sistema **FOODEX**.  
El objetivo principal es garantizar integridad, consistencia, escalabilidad y seguridad, apoyando tanto al backend como al flujo general del sistema (recetas, ingredientes, usuarios, roles, etapas, inventario).

---

## 2. Infraestructura y Herramientas Utilizadas

| Herramienta           | Uso                               | Beneficios                                                        |
|-----------------------|------------------------------------|------------------------------------------------------------------|
| PostgreSQL (Azure)    | Motor de base de datos principal   | Entorno profesional, estable y sin costo (cuenta educativa)      |
| PgAdmin               | Administración visual              | Creación de tablas, ejecución de scripts, gestión de relaciones, monitoreo |
| GitHub                | Control de versiones               | Historial, colaboración con Backend, trazabilidad completa       |

---

## 3. Estructura de la Base de Datos

### 3.1 Cantidad y Naturaleza de las Tablas

La base de datos está compuesta por aprox. **10 tablas**, diseñadas según los requerimientos definidos en el PRON.

**Entre las entidades principales se incluyen:**

- recetas  
- receta_tecnica  
- receta_etapa  
- receta_ingrediente  
- ingredientes  
- usuarios  
- rol  
- categorias_ingrediente  
- etapas  
- etapa_ingrediente  

---

### 3.2 Relaciones entre Tablas

El modelo emplea relaciones **1:N** y **M:N** según el dominio gastronómico.

| Tabla M:N           | Descripción                                                                 | Propósito                                      |
|---------------------|------------------------------------------------------------------------------|------------------------------------------------|
| receta_ingrediente  | Una receta tiene muchos ingredientes, y un ingrediente puede estar en muchas recetas | Evita duplicación y asegura combinaciones únicas |
| receta_etapa        | Una receta contiene múltiples etapas; una etapa puede reutilizarse           | Flexibilidad en la secuenciación de pasos      |

**Objetivo general:** mantener integridad referencial y un modelo extensible.

---

## 4. Flujo de Trabajo del Equipo de Base de Datos

| Etapa                         | Descripción                                                       |
|-------------------------------|-------------------------------------------------------------------|
| 1. Definición del modelo      | Revisión de requerimientos y solicitudes del backend/profesor     |
| 2. Creación de scripts SQL    | Cambios documentados y versionados en GitHub                      |
| 3. Aplicación en PgAdmin      | Ejecución directa en el servidor Azure PostgreSQL                 |
| 4. Validación                 | Revisión y ajustes con el equipo Backend                          |

---

## 5. Iteración Continua

- Validación permanente con Backend.  
- Ajuste de relaciones M:N según cambios del proyecto.  
- Actualización de restricciones e integridad.  
- Revisión periódica de la evolución del modelo.  

---

## 6. Estado Actual y Observaciones Finales

| Aspecto                    | Estado        |
|---------------------------|---------------|
| Motor PostgreSQL en Azure | ✔ Operativo   |
| Estructura principal      | En refinamiento |
| Tablas M:N                | En refinamiento |
| Repositorio GitHub        | ✔ Actualizado y en uso activo |
| Coordinación con Backend  | ✔ Fluida y continua |




