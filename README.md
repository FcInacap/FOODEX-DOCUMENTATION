# FOODEX-DOCUMENTATION

#  Documentación de Avance del Proyecto FOODEX  
**Fase:** Entrega Inicial / Definición de Alcance  
**Periodo Cubierto:** Semanas 1 a 3  
**Documentador Responsable:** Abril Castro  

---

# 1. Contexto del Proyecto y Resumen Ejecutivo

## 1.1. Contexto General
Foodex es una plataforma diseñada para modernizar la gestión de fichas técnicas y recetas en aulas de gastronomía. El propósito principal es digitalizar completamente el material que actualmente se maneja impreso, y mostrarlo en **tablets instaladas en los mesones de las cocinas**.

El sistema debe asegurar:
- Acceso seguro y diferenciado por rol (Profesor, Estudiante, Administrador).  
- Flujo ordenado entre etapas, ingredientes, técnicas y procedimientos.  
- Reducción del uso de papel y mejora en trazabilidad y organización.

---

## 1.2. Resumen Ejecutivo (Avance Semanas 1–3)

| Equipo       | Logros Principales | Pendientes Críticos |
|--------------|---------------------|----------------------|
| **Base de Datos (BD)** | Modelo relacional diseñado (PostgreSQL), incluidas relaciones M:M como *etapas_ingredientes*. | Revisión final de estructura, ajuste de “Receta Técnica” al “Ingrediente”, generación de scripts definitivos. |
| **Back End (BE)** | Arquitectura definida (Django + DRF, Channels + Redis). Modelos implementados. Endpoints críticos listos. | Validaciones adicionales, restricciones, revisión de endpoints sensibles y controles de seguridad. |
| **Front End (FE)** | Mockup funcional aprobado. Vistas principales de lectura creadas con datos mock. | Corrección de bugs UI/UX, formalizar GitHub e iniciar integración con Backend. |
| **Control de Calidad (QA)** | Estrategia de pruebas definida. Primeros bugs detectados. | Planificar y ejecutar pruebas headless en Postman cuando Backend libere endpoints. |

---

# 2. Avance Detallado: Equipo de Base de Datos (BD)

**Motor:** PostgreSQL (Azure)  
**Responsables:** Abril  

## 2.1. Objetivos y Flujo de Trabajo
El objetivo del equipo de BD es crear un modelo normalizado y extensible que asegure la integridad referencial. El flujo de trabajo implica la revisión continua de requerimientos y la validación permanente con el Back End antes de la aplicación de scripts en PgAdmin (herramienta necesaria para gestionar las BD en postgreSQL.

---

## 2.2. Modelo Relacional: Estructura y Entidades
El modelo se basa en la definición de entidades principales y un conjunto de tablas intermedias (M:M) para gestionar la complejidad de una receta técnica:
### A. Entidades y Relaciones 1:N

| Entidad Principal       | Relación 1:N con… | Lógica de Integridad |
|-------------------------|--------------------|-----------------------|
| roles                   | usuarios           | Cada usuario pertenece a un rol. |
| usuarios                | recetas            | Un usuario puede ser autor de múltiples recetas. |
| unidades                | ingredientes       | Todo ingrediente debe tener una unidad definida. |
| categoria_ingrediente   | ingredientes       | Cada ingrediente se clasifica en una categoría. |

---

### B. Relaciones M:N – Núcleo Gastronómico
Estas tablas gestionan cómo los componentes (ingredientes, etapas, técnicas) se combinan para formar una receta.

| Tabla relacional     | Relaciones              | Atributo Clave     | Función Crítica |
|----------------------|--------------------------|---------------------|-----------------|
| receta_ingrediente   | Receta ↔ Ingrediente     | cantidad_total     | Lista y cantidad total del ingrediente. |
| receta_etapa         | Receta ↔ Etapa           | orden_etapa        | Flujo secuencial de la receta. |
| receta_tecnica       | Receta ↔ Técnica         | N/A                | Registro de técnicas aplicadas. |
| etapa_ingrediente    | Etapa ↔ Ingrediente      | cantidad_etapa     | Distribución del ingrediente por etapa. |

---

## 2.3. Observaciones y Ajustes Pendientes
Receta Técnica: Se definió que la receta técnica debe ir asociada al Ingrediente, no a la Receta (ej. "Técnica de corte Braseado") para mayor precisión y consistencia técnica.
Etapas: El modelo inicial no detallaba las etapas. Se ha incorporado la tabla etapas y la tabla M:M receta_etapa para resolver este punto.
---

# 3. Avance Detallado: Equipo Back End (BE)

**Framework:** Django + Django REST Framework  
**Responsable:** Franco Cicerelli 

El Equipo Backend se centró en la lógica del sistema de manera que los usuarios, los roles,  las recetas, los ingredientes, los pasos y todo lo relacionado con la gestión interna, se estructuré para hacerlo más ordenado, escalable y fácil de mantener.

## 3.1. Arquitectura y Stack Tecnológico
El Back End se ha diseñado bajo un enfoque basado en la Arquitectura MVC (Modelo–Vista–Controlador) con separación de capas, para garantizar escalabilidad, precisión de datos y sincronización.

| Componente                 | Tecnología         | Propósito |
|---------------------------|--------------------|-----------|
| Lógica de Negocio         | Django + DRF       | CRUD, vistas y serialización a JSON. |
| Comunicación en Tiempo Real | Channels + Redis | WebSockets para anotaciones en aula. |
| Seguridad                 | JWT (SimpleJWT)    | Acceso seguro por rol. |
| Persistencia de Datos     | PostgreSQL         | Integridad y manejo de cantidades precisas. |

---

## 3.2. Modelos Lógicos Implementados

El equipo de Back End en base a los requerimientos de BD, los ha transformado a modelos de Django, asegurando la lógica de negocio:
Modelo User: Extiende el modelo base de Django para compatibilidad con autenticación nativa. Incluye campos educativos (semestre, carrera) y relación protegida con Role (on_delete=PROTECT).
Modelo Receta: Incluye campos de trazabilidad (creado_en, actualizado_en) para auditoría, y campos numéricos clave (porciones_base, tiempo_preparacion_min).
Modelo PasoProcedimiento: Asegura la secuencia con un campo orden_procedimiento y una restricción unique_together para evitar pasos duplicados en la misma receta.


---

## 3.3. Endpoints REST y Permisos por Rol

### Endpoints principales
Se han definido los siguientes endpoints API que son clave para la propuesta de valor de Foodex:

| Endpoint | Método | Función |
|----------|--------|---------|
| `/api/v1/usuarios/` | GET / POST | Listar o crear usuarios |
| `/api/v1/roles/` | GET | Listar roles |
| `/api/v1/ingredientes/` | GET / POST | CRUD básico de ingredientes |
| `/api/v1/categorias/` | GET / POST | Categorías de ingredientes |
| `/api/v1/recetas/` | GET / POST | CRUD de recetas |
| `/api/v1/recetas/{id}/detalle_completo/` | GET | Receta con ingredientes y etapas |
| `/api/v1/recetas/{id}/recalcular?porciones=x` | GET | Ajuste automático de cantidades |
| `/api/v1/recetas/buscar?q=texto` | GET | Búsqueda rápida |
| `/api/v1/recetas/por_categoria?id_categoria=x` | GET | Filtrar por categoría |
| `/api/v1/recetas/por_tecnica?id_tecnica=x` | GET | Filtrar por técnica |
| `/api/v1/canasta/` | GET / POST | Gestión de stock |

### Permisos por Rol
En cuanto los permisos de roles están establecidos de la siguiente forma:


| Rol      | Permisos |
|----------|-----------|
| Admin    | CRUD completo |
| Profesor | Lectura + creación + edición limitada |
| Alumno   | Solo lectura |

---

# 4. Avance Detallado: Equipo Control de Calidad (QA)

**Estrategia:** Enfoque en Pruebas de Integración (MVP) y la definición de una Puerta de Calidad continua.  
**Responsable:** Abril Castro  

## 4.1. Estrategia y Requisitos de Entrada
El equipo de QA ha definido su alcance basándose en la necesidad de establecer un "contrato funcional" claro con los desarrolladores.

| Equipo      | Requisito de Entrada | Propósito de QA |
|-------------|-----------------------|------------------|
| Back End    | Documentación Swagger | Iniciación de Pruebas Headless (pruebas de navegador sin interfaz, directamente con la API enviando solicitudes HTTP) para validar la lógica y las reglas de negocio antes de la integración con el Front End. |
| Base de Datos | Diagramas y restricciones | Diseño de Pruebas de Validación y Límite (ej. valores nulos, longitud de campos) para garantizar la integridad. |
| Front End   | Builds o staging | Ejecución de Smoke Tests y pruebas de Interfaz/Usabilidad en el dispositivo objetivo (Tablet). |

---

## 4.2. Bugs Reportados (Semana 2)

| ID          | Componente        | Descripción del Bug | Impacto |
|-------------|-------------------|----------------------|---------|
| QA-UI-001   | Vista Estudiantes | Texto no se ajusta correctamente al cuadro. | Estético/Usabilidad |
| QA-UI-002   | Detalle de Receta | Botón de salida sin ícono visible. | Navegación |

---

## 4.3. Próximos Pasos
- Verificar corrección de QA-UI-001 y QA-UI-002.  
- Preparar casos para endpoints de Escalado y Recálculo.  

---

# 5. Avance Detallado: Equipo Front End (FE)

**Objetivo:** Interfaz para tablets  
**Responsable:** Jacques Lapierre  

## 5.1. Progreso Semanal

| Semana | Foco Principal          | Avances Concretos | Estado |
|--------|--------------------------|--------------------|--------|
| 1 | Planificación y Setup | Se creó el proyecto local y se aprobó el mockup inicial. Se definió la paleta de colores y la tipografía, y se estableció la navegación base. Además, el diseño fue adaptado correctamente para tablets del instituto, asegurando una navegación fluida entre las pantallas principales. El repositorio quedó configurado y funcionando sin errores. | Completado |
| 2 | Desarrollo Core Alumno | Se implementó la vista de lista de recetas y la vista de detalle utilizando datos mock. El diseño fue validado en tablet y se completó el flujo de navegación entre inicio, lista, detalle y creación, asegurando una experiencia coherente. | Completado |
| 3 | Interacción (Crear/Editar) | Se inició el desarrollo de los formularios para crear y editar recetas, los cuales están diseñados para integrarse posteriormente con los endpoints del Backend. | En Proceso |

---

## 5.2. Tareas Pendientes Críticas

- Conexión estable con Back-End (API aún no disponible).  
- Validaciones completas del formulario.  
- Wireframes finales por revisar.  
- Implementar pasos e imágenes en formulario.  
- Documentar errores detectados.  
- Integrar Vista de Profesor.  

