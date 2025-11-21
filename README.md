# FOODEX-DOCUMENTATION

# 📘 Documentación de Avance del Proyecto FOODEX  
**Fase:** Entrega Inicial / Definición de Alcance  
**Periodo Cubierto:** Semanas 1 a 3 (10/11/2025 – 01/12/2025)  
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
El objetivo es construir un modelo normalizado, robusto y extensible que asegure integridad referencial.  
El flujo actual incluye:

1. Revisión de requerimientos.  
2. Definición del modelo lógico.  
3. Implementación de scripts SQL en PgAdmin.  
4. Validación continua con Backend.  
5. Ajustes e iteración permanente.  

---

## 2.2. Modelo Relacional: Estructura y Entidades

### A. Entidades y Relaciones 1:N

| Entidad Principal       | Relación 1:N con… | Lógica de Integridad |
|-------------------------|--------------------|-----------------------|
| roles                   | usuarios           | Cada usuario pertenece a un rol. |
| usuarios                | recetas            | Un usuario puede ser autor de múltiples recetas. |
| unidades                | ingredientes       | Todo ingrediente debe tener una unidad definida. |
| categoria_ingrediente   | ingredientes       | Cada ingrediente se clasifica en una categoría. |

---

### B. Relaciones M:N – Núcleo Gastronómico

| Tabla relacional     | Relaciones              | Atributo Clave     | Función Crítica |
|----------------------|--------------------------|---------------------|-----------------|
| receta_ingrediente   | Receta ↔ Ingrediente     | cantidad_total     | Lista y cantidad total del ingrediente. |
| receta_etapa         | Receta ↔ Etapa           | orden_etapa        | Flujo secuencial de la receta. |
| receta_tecnica       | Receta ↔ Técnica         | N/A                | Registro de técnicas aplicadas. |
| etapa_ingrediente    | Etapa ↔ Ingrediente      | cantidad_etapa     | Distribución del ingrediente por etapa. |

---

## 2.3. Observaciones y Ajustes Pendientes
- La **Receta Técnica** se vincula al **Ingrediente** para mayor precisión, no a la Receta.  
- Incorporación de entidad **etapas** y tabla M:N **receta_etapa**.  

---

# 3. Avance Detallado: Equipo Back End (BE)

**Framework:** Django + Django REST Framework  
**Responsable:** Franco Cicerelli  

## 3.1. Arquitectura y Stack Tecnológico

| Componente                 | Tecnología         | Propósito |
|---------------------------|--------------------|-----------|
| Lógica de Negocio         | Django + DRF       | CRUD, vistas y serialización a JSON. |
| Comunicación en Tiempo Real | Channels + Redis | WebSockets para anotaciones en aula. |
| Seguridad                 | JWT (SimpleJWT)    | Acceso seguro por rol. |
| Persistencia de Datos     | PostgreSQL         | Integridad y manejo de cantidades precisas. |

---

## 3.2. Modelos Lógicos Implementados

- **User**: Compatible con autenticación nativa. Campos de carrera/semestre. Relación protegida con Role.  
- **Receta**: Trazabilidad completa con timestamps, porciones base, tiempos de preparación.  
- **PasoProcedimiento**: Control secuencial mediante `orden_procedimiento` y `unique_together`.  

---

## 3.3. Endpoints REST y Permisos por Rol

### Endpoints principales

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

| Rol      | Permisos |
|----------|-----------|
| Admin    | CRUD completo |
| Profesor | Lectura + creación + edición limitada |
| Alumno   | Solo lectura |

---

# 4. Avance Detallado: Equipo Control de Calidad (QA)

**Estrategia:** Pruebas de Integración + Puerta de Calidad  
**Responsable:** Abril Castro  

## 4.1. Estrategia y Requisitos de Entrada

| Equipo      | Requisito de Entrada | Propósito de QA |
|-------------|-----------------------|------------------|
| Back End    | Documentación Swagger | Validar la lógica via pruebas headless (Postman). |
| Base de Datos | Diagramas y restricciones | Pruebas de integridad, límites y nulos. |
| Front End   | Builds o staging | Smoke tests + pruebas de usabilidad. |

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
| 1 | Planificación y Setup | Proyecto local creado, mockup aprobado, navegación base lista, diseño adaptado a tablets, repositorio configurado. | Completado |
| 2 | Desarrollo Core Alumno | Lista y detalle de recetas funcionando con mock data; flujo completo entre pantallas. | Completado |
| 3 | Interacción (Crear/Editar) | Formularios de creación/edición iniciados y diseñados para integrarse con Backend. | En Proceso |

---

## 5.2. Tareas Pendientes Críticas

- Conexión estable con Back-End (API aún no disponible).  
- Validaciones completas del formulario.  
- Wireframes finales por revisar.  
- Implementar pasos e imágenes en formulario.  
- Documentar errores detectados.  
- Integrar Vista de Profesor.  

