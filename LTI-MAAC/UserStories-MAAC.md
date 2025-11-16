# Indice
1. User Personas y Requisitos (Completos)
2. Requisitos MVP (Candidato)
3. User Stories MVP (Candidato)
3.1. US-01: Listar vacantes publicadas
3.2. US-02: Ver detalle de vacante
4. Tickets de trabajo
4.1. Principales tickets de trabajo de US-01: Listar vacantes publicadas
5. Documento de especificación para BDD
6. Plan de pruebas
7. Backlog de Producto (MVP Candidato)
---

# 1. User Personas y Requisitos (Completos)
## 1.1. User Personas
1. Candidato (visitante / logueado)
2. Reclutador
3. Hiring Manager
4. Admin de Cuenta (Configuración / Permisos / Branding)
5. Entrevistador

## 1.2. Requisitos (extraídos):
- R01 Crear, aprobar y publicar vacantes (multi-canal interno/externo)
- R02 Gestionar ciclo completo del candidato (postulación, etapas, estado)
- R03 Parsing de CV y enriquecimiento de perfil
- R04 Matching y sugerencias inteligentes desde Talent Pool
- R05 Colaboración interna (comentarios, scorecards, aprobaciones)
- R06 Automatizaciones configurables (workflows visuales, reglas por evento)
- R07 Analítica y dashboards (embudos, tiempos, fuentes, predicciones)
- R08 Integraciones plug-and-play (HRIS, calendarios, email, video, job boards, CV parsing)
- R09 Talent Pool unificado con etiquetas dinámicas y reengagement
- R10 Portal del candidato con seguimiento en tiempo real y notificaciones
- R11 Seguridad, permisos granulares, auditoría y cumplimiento (GDPR/CCPA)
- R12 Personalización y employer branding (portal adaptable)
- R13 Arquitectura escalable (multi-tenant, módulos, cache, event bus)
- R14 Movilidad (app móvil para acciones críticas)
- R15 API pública y webhooks para extensiones
- R16 Gestión de consentimiento y cifrado datos sensibles
- R17 Soporte multi-equipo / multi-rol / multi-unidad / multi-marca
- R18 Observabilidad (logging estructurado, tracing)
- R19 Alto rendimiento (cache, CDN, desacoplamiento asíncrono)
- R20 Marketplace / modularidad futura de extensiones
---
# 2. Requisitos MVP (Candidato)
- R01 Publicación y aplicación a vacantes (entrada al sistema)
- R02 Seguimiento del ciclo de postulación y estado (transparencia mínima)
- R16 Consentimiento y protección de datos personales (cumplimiento básico)
---
# 3. User Stories MVP (Candidato)

## 3.1. US-01: Listar vacantes publicadas
- Título: Ver listado de vacantes publicadas
- Actor: Candidato (no autenticado o autenticado)
- Objetivo: Explorar oportunidades disponibles y decidir si profundizar/postular.
- Valor: Facilita entrada al embudo de reclutamiento.

- Alcance mínimo (MVP): Listado paginado con título, ubicación, tipo, fecha publicación, estado abierto. Filtro básico por palabra clave y ubicación.

- Precondiciones: Existen vacantes activas con estado Publicada.
- Flujo principal:
    -Candidato accede a /jobs
    -Sistema consulta vacantes activas ordenadas por fecha desc.
    -Muestra página 1 con metadatos y contador total.
    -Candidato filtra o pagina (opcional).
- Campos mostrados MVP: jobId, título, ubicación (ciudad / remoto), tipo (full-time/part-time), fechaPublicación.
- Excluidos MVP: salario exacto (placeholder), etiquetas avanzadas, ranking IA.

- Criterios de aceptación (Gherkin):
    - Dado vacantes activas cuando el candidato visita /jobs entonces ve un listado con al menos título y ubicación.
    - Dado no hay vacantes activas cuando visita /jobs entonces ve mensaje “No hay vacantes disponibles”.
    - Dado más de 20 vacantes cuando visita /jobs entonces se muestra paginación siguiente/anterior.
    - Dado aplica filtro por palabra clave cuando envía búsqueda entonces resultados contienen solo vacantes cuyo título incluye el término (case-insensitive).

- Errores / Edge Cases: sin resultados filtro, tiempo de respuesta >2s, datos incompletos (omitidos).
- No funcional: Tiempo de carga < 1.5s para 20 vacantes, accesibilidad WCAG AA (semánticos básicos).
- Métricas: CTR a detalle de vacante, tasa de rebote, tiempo en página.

- Riesgos abiertos: internacionalización, ordenación configurable, caché invalida.
- Dependencias: R01 (publicación), R19 (rendimiento), R12 (branding superficial).

## 3.2. US-02: Ver detalle de vacante
- Título: Ver detalle de una vacante
- Actor: Candidato (no autenticado o autenticado)
- Objetivo: Conocer requisitos y beneficios para decidir postular.
- Valor: Mejora conversión al aportar información clave.
- 
- Alcance MVP: Página /jobs/{id} con título, ubicación, tipo, descripción, fecha publicación, estado abierto y botón “Postular”.
- Precondiciones: Vacante con estado Publicada.
- Flujo principal:
    - Candidato abre /jobs/{id}
    - Sistema busca vacante publicada por id
    - Muestra datos y CTA “Postular”
- Campos MVP: title, location, employmentType, remote, publishedAt, description (HTML seguro/sanitizado).
- Excluidos MVP: rango salarial exacto, beneficios detallados, vacantes relacionadas.
- Criterios de aceptación (Gherkin):
    - Dado una vacante publicada cuando visito /jobs/{id} entonces veo título, ubicación, tipo, fecha, descripción y CTA Postular.
    - Dado una vacante no publicada cuando visito /jobs/{id} entonces recibo 404 y la UI muestra “Vacante no encontrada”.
    - Dado contenido HTML en descripción cuando la página renderiza entonces se sanitiza y conserva formato básico (p, ul, li, strong).
- Errores/Edge: id inválido, vacante cerrada (404), red lenta (skeleton).
- No funcional: TTFB < 800 ms, accesibilidad WCAG AA (semántica y foco).
- Métricas: CTR “Postular”, tiempo en página, tasa de 404.
- Dependencias: R01 (publicación), R19 (rendimiento).
---

# 4. Tickets de trabajo

## 4.1. Principales tickets de trabajo de US-01: Listar vacantes publicadas

- ID: TDT-001
Criterios de aceptación:
- GET /api/jobs devuelve 200 con items [id, title, location, employmentType, remote, publishedAt] y meta {page, pageSize, total}
- Solo retorna vacantes con status=Publicada; orden por publishedAt desc
- Filtros: q (contains, case-insensitive) y location (prefijo); page/pageSize (por defecto 1/20; máx 50)
- 400 para parámetros inválidos
- Prioridad: Alta
- Estimación de esfuerzo (con metología de tamaño de camisetas): M
- Tareas Técnicas:
- Definir contrato y DTOs; validar y sanear query params
- Implementar handler/servicio/repositorio con orden y filtros
- Manejo de errores y códigos; paginación consistente
- Pruebas unitarias del endpoint
- Notas: Considerar i18n en búsqueda y normalizar mayúsculas/minúsculas.

- ID: TDT-002
Criterios de aceptación:
- Esquema Jobs con campos requeridos e índices en (status, published_at desc), title y location
- Migración aplicable y reversible; datos seed (3–5 vacantes)
- Prioridad: Alta
- Estimación de esfuerzo (con metología de tamaño de camisetas): S
- Tareas Técnicas:
- Definir tabla/colección y constraints
- Crear migraciones up/down e índices
- Semillas para entorno de desarrollo
- Notas: Asegurar compatibilidad con nulos en location y published_at.

- ID: TDT-003
Criterios de aceptación:
- Página /jobs muestra 20 vacantes con campos mínimos; estados: vacío, carga y error
- Integración con GET /api/jobs; debounce búsqueda 300 ms
- Persistencia de filtros en URL (?q=&location=&page=) y soporte back/forward
- Paginación siguiente/anterior y navegación por teclado (a11y)
- Prioridad: Alta
- Estimación de esfuerzo (con metología de tamaño de camisetas): M
- Tareas Técnicas:
- Construir layout, tarjeta de vacante y componente de paginación
- Cliente API y hook de datos con abort/cancel; sincronizar estado <-> querystring
- Tests de integración de la vista
- Notas: Optimizar TTI y manejar reintento en errores de red.

## 4.2. Principales tickets de trabajo de US-02: Ver detalle de vacante
ID: TDT-004
Criterios de aceptación:
- GET /api/jobs/{id} devuelve 200 con {id, title, location, employmentType, remote, publishedAt, descriptionHtml}
- 404 cuando id no existe o status ≠ Publicada
- Validación de id (UUID) y manejo de errores
- Prioridad: Alta
- Estimación de esfuerzo (con metología de tamaño de camisetas): S
- Tareas Técnicas:
- Handler/servicio/repositorio con filtro por estado
- Validación de ruta/parámetros y mapeo de DTO
- Contrato OpenAPI y ejemplo de respuesta
- Notas: Alinear esquema con listado y registrar 404 sin filtrar existencia.

ID: TDT-005
Criterios de aceptación:
- Sanitización de description: permite p, ul, ol, li, strong, em, a(href, rel="nofollow noopener"), br
- Elimina scripts, estilos, iframes y atributos on*
- Pruebas que demuestran que XSS no se renderiza
- Prioridad: Alta
- Estimación de esfuerzo (con metología de tamaño de camisetas): S
- Tareas Técnicas:
- Integrar librería de sanitización con lista blanca
- Configurar política de atributos y URL seguras
- Unit tests del sanitizador con payloads maliciosos
- Notas: Preferir sanitización en servidor y aplicar CSP en FE.

- ID: TDT-006
Criterios de aceptación:
- Página /jobs/{id} muestra title, location, employmentType, remote, publishedAt, description (HTML seguro) y CTA “Postular”
- Skeleton mientras carga; 404 muestra “Vacante no encontrada”
- Accesibilidad: encabezados semánticos, foco gestionado, navegación por teclado
- Prioridad: Alta
- Estimación de esfuerzo (con metología de tamaño de camisetas): M
- Tareas Técnicas:
- Vista de detalle y componente de descripción segura
- Llamada a API, estados loading/error/not-found
- Enlace del CTA a flujo de postulación
- Notas: Preparar i18n y formateo local de fechas.
---

# 5. Documento de especificación para BDD
## 5.1. Especificación BDD para US-01: Listar vacantes publicadas
Contexto:
- Actor: Candidato (no autenticado o autenticado)
- Dependencias: TDT-001, TDT-002, TDT-003

```gherkin
Feature: Ver listado de vacantes publicadas
  Como Candidato
  Quiero ver un listado de vacantes activas
  Para decidir si profundizar y postular

  Background:
    Given existen vacantes con estado "Publicada"

  Scenario: Mostrar listado básico
    When el candidato visita /jobs
    Then ve hasta 20 vacantes ordenadas por fecha de publicación descendente
    And cada vacante muestra título, ubicación, tipo, remoto y fecha de publicación

  Scenario: Estado vacío
    Given no existen vacantes con estado "Publicada"
    When el candidato visita /jobs
    Then ve el mensaje "No hay vacantes disponibles"

  Scenario: Paginación
    Given existen más de 20 vacantes publicadas
    When el candidato visita /jobs?page=2
    Then ve la segunda página de resultados
    And puede navegar a la página anterior y siguiente

  Scenario: Búsqueda por palabra clave y ubicación
    When el candidato busca con q="ingeniero" y location="Mad"
    Then el listado solo contiene vacantes cuyo título contiene "ingeniero" (sin distinguir mayúsculas/minúsculas)
    And la ubicación comienza por "Mad"
```
## 5.2. Especificación BDD para US-02: Ver detalle de vacante
Contexto:
- Actor: Candidato (no autenticado o autenticado)
- Dependencias: TDT-004, TDT-005, TDT-006

```gherkin
Feature: Ver detalle de una vacante
  Como Candidato
  Quiero ver el detalle de una vacante publicada
  Para decidir si postular

  Background:
    Given existe una vacante con id válido y estado "Publicada"

  Scenario: Visualización de detalle
    When el candidato visita /jobs/{id}
    Then ve título, ubicación, tipo, remoto, fecha de publicación y descripción formateada
    And ve un botón "Postular"

  Scenario: Vacante no publicada o inexistente
    Given la vacante no existe o su estado no es "Publicada"
    When el candidato visita /jobs/{id}
    Then recibe 404 y la UI muestra "Vacante no encontrada"

  Scenario: Sanitización de HTML en descripción
    Given la descripción contiene HTML con etiquetas permitidas y contenido malicioso
    When se renderiza la descripción
    Then solo se conservan p, ul, ol, li, strong, em, a con href y rel="nofollow noopener", br
    And no se ejecuta ningún script ni se muestran iframes
```

# 6. Plan de pruebas

### Alcance:
- User Stories: US1 Listado, US2 Detalle
- Tickets: TDT-001, TDT-002, TDT-003, TDT-004, TDT-005, TDT-006

### Estrategia de prueba:
- Unit: lógica de endpoints, sanitización, repositorios (TDT-001, TDT-002, TDT-004, TDT-005)
- Integración: API + DB + migraciones + contratos OpenAPI (TDT-001, TDT-002, TDT-004)
- E2E: flujos /jobs y /jobs/{id} en navegador con mocks cuando aplique (TDT-003, TDT-006)
- Seguridad: validación de inputs, XSS (TDT-005)
- Rendimiento (mínimo MVP): p95 < 1.5s en /jobs con 20 ítems; p95 < 1.2s en /jobs/{id}

### Casos de prueba (muestra):
- TC-US1-001 (Integración, Alta): GET /api/jobs sin filtros devuelve 200, 20 ítems, orden desc por publishedAt. (TDT-001)
- TC-US1-002 (Unit, Media): Filtro q case-insensitive y location prefijo. (TDT-001)
- TC-US1-003 (Integración, Media): page/pageSize con límites; 400 para params inválidos. (TDT-001)
- TC-US1-004 (DB, Media): índices creados y utilizados (plan de ejecución verifica IX_jobs_status_publishedAt). (TDT-002)
- TC-US1-005 (E2E, Alta): /jobs renderiza listado, paginación, estado vacío y error de red con retry. (TDT-003)
- TC-US2-001 (Integración, Alta): GET /api/jobs/{id} 200 con campos esperados; 404 si no publicada/inexistente. (TDT-004)
- TC-US2-002 (Unit, Alta): Sanitizador elimina scripts/ifram es y conserva etiquetas permitidas. (TDT-005)
- TC-US2-003 (E2E, Alta): /jobs/{id} muestra detalle, skeleton, 404 “Vacante no encontrada”, CTA “Postular” visible. (TDT-006)

### Criterios de entrada:
- Migraciones aplicadas en entorno de prueba (TDT-002)
- Endpoints implementados (TDT-001, TDT-004)
- Build de FE listo (TDT-003, TDT-006)

### Criterios de salida:
- 100% de casos críticos (Alta) aprobados; >=90% Media; documentados los Low
- Cobertura >80% en módulos de endpoints y sanitización
- Sin vulnerabilidades XSS detectadas en pruebas

### Ambientes y datos:
- Entorno: QA con DB aislada
- Datos seed: 5 vacantes (3 publicadas, 1 borrador, 1 cerrada) con descripciones con HTML seguro y payloads maliciosos controlados

### Reporting:
- CI publica resultados de unit/integración (JUnit XML) y E2E (screenshots/videos)
- Métricas: tasa de éxito, p95 latencia, errores por tipo (4xx/5xx)

### Riesgos y mitigaciones:
- XSS en descripción: sanitización en servidor + CSP en FE (TDT-005)
- Rendimiento de listados: índices y paginación estricta (TDT-001, TDT-002)

# 7. Backlog de Producto (MVP Candidato)
| ID    | Epic                 | User Story                                                                                     | Prioridad | Estimación | Criterios de Aceptación (resumen)                                                                                               | Dependencias         | Estado   | Sprint/Release |
|-------|----------------------|-------------------------------------------------------------------------------------------------|-----------|------------|-------------------------------------------------------------------------------------------------------------------------------|----------------------|----------|----------------|
| US-01 | Portal del Candidato | Como Candidato, quiero ver un listado de vacantes publicadas, para explorar y decidir postular | Alta      | M          | Listado paginado ordenado por fecha desc; muestra título, ubicación, tipo, remoto, fecha; estado “sin vacantes”; filtros q y location; paginación siguiente/anterior | R01, R19, R12        | Por hacer | MVP-1          |
| US-02 | Portal del Candidato | Como Candidato, quiero ver el detalle de una vacante publicada, para decidir si postular       | Alta      | M          | Detalle con título, ubicación, tipo, remoto, fecha, descripción sanitizada y CTA “Postular”; 404 si no publicada/inexistente; sanitización HTML segura              | R01, R19             | Por hacer | MVP-1          |