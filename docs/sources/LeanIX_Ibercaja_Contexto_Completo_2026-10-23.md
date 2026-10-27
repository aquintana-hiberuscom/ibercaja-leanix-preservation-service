# Dossier consolidado del proyecto SAP LeanIX para Ibercaja

**Fecha de consolidación:** 23 de octubre de 2026
**Autor del contexto:** Arturo Quintana Quijada
**Estado:** Documento vivo de contexto, decisiones, evidencias y puntos abiertos
**Objetivo:** Preservar en un único documento todo el conocimiento reunido sobre el alcance, arquitectura, APIs, decisiones y validaciones del proyecto, para poder retomarlo sin depender de conversaciones anteriores.

---

## 1. Resumen ejecutivo

La solución prevista consiste en **un único microservicio desplegado en OpenShift dentro de los sistemas de Ibercaja**, con tres responsabilidades lógicas independientes:

1. Mantener en **Cosmos DB** una proyección viva, transformada y consultable de los Fact Sheets de SAP LeanIX.
2. Generar y almacenar en **Azure Blob Storage** exportaciones o snapshots del modelo de LeanIX, quedando pendiente seleccionar la estrategia final entre capturas completas o baseline más incrementales.
3. Extraer y almacenar en **Azure Blob Storage** todos los diagramas accesibles del workspace, en XML compatible con Draw.io, junto con sus metadatos, versiones y organización en colecciones y carpetas cuando la identidad técnica tenga los permisos necesarios.

La actualización ordinaria de Cosmos DB se plantea mediante **webhook PUSH como mecanismo principal** y **webhook PULL como alternativa**. Se realizará una carga inicial completa y se valorará una reconciliación completa periódica como protección ante eventos perdidos o incidencias prolongadas.

Cosmos DB mantendrá el último estado conocido de los Fact Sheets. No será un histórico funcional completo. Las bajas se gestionarán mediante **borrado lógico**, según la recomendación de Aitor.

La solución no incluye Prometheus, Grafana, una plataforma propia de observabilidad, un restaurador de LeanIX ni una garantía de reconstrucción automática. El microservicio emitirá logs estructurados para la plataforma corporativa de Ibercaja.

---

## 2. Origen del proyecto y documentos analizados

Se han contrastado las aclaraciones de una reunión con Aitor, arquitecto de Ibercaja, frente a los documentos y contratos técnicos disponibles:

- `Preservación y reconstrucción del modelo de SAP LeanIX.docx`: especificación inicial.
- `LeanIX_Contexto_Consolidado_Tres_Procesos_v2.md`: consolidación posterior del planteamiento en tres procesos.
- `LeanIX_Estimacion_Microservicio_V1.xlsx`: estimación del proyecto y desglose del microservicio.
- `leanix-pathfinder-openapi.json`: contrato Pathfinder entregado inicialmente.
- Swagger/OpenAPI del entorno de Ibercaja para Pathfinder, Webhooks, Diagrams V2 y Navigation.
- JSON reales obtenidos mediante llamadas REST:
  - `get_bookmarks.json`
  - `get_bookmark_id.json`
  - `get_bookmark_id_versions.json`
- Respuesta por correo de Aitor a la solicitud de confirmación de alcance.
- Pruebas manuales realizadas contra LeanIX productivo, incluida la apertura correcta en Draw.io de un XML de diagrama real.

---

## 3. Arquitectura funcional acordada

### 3.1. Un único microservicio

La solución se implementará como un único microservicio desplegado en OpenShift. Dentro del microservicio habrá tres procesos o responsabilidades lógicas independientes, con configuración, planificación, ejecución y resultado propios.

La independencia significa que el fallo de una responsabilidad no debería presentarse como éxito global ni impedir necesariamente que las demás se ejecuten.

### 3.2. P1: estado vivo de Fact Sheets en Cosmos DB

Objetivo:

- Realizar una carga inicial completa.
- Transformar los datos de LeanIX a una estructura consultable y explotable.
- Mantener el último estado conocido de los Fact Sheets.
- Conservar las relaciones y demás datos devueltos por los endpoints incluidos en alcance.
- Gestionar la evolución del metamodelo si se producen cambios futuros.

Volumetría comunicada durante las conversaciones: aproximadamente 5.000 Fact Sheets de distintos tipos. Esta cifra debe tratarse como volumetría comunicada, no como medición técnica certificada.

Actualización ordinaria:

- PUSH webhook como mecanismo preferente.
- PULL webhook como alternativa si PUSH no puede habilitarse por infraestructura, permisos o conectividad.
- Posible reconciliación completa periódica como salvaguarda.

Bajas:

- Aitor recomienda borrado lógico para no perder información.
- El documento en Cosmos debería reflejar que el elemento ya no está activo o ya no existe en LeanIX.
- Debe definirse el campo exacto de baja y el tratamiento de relaciones asociadas durante el diseño técnico.

Cosmos DB no almacenará:

- diagramas como contenido funcional;
- cadena histórica completa de cambios;
- snapshots históricos del workspace.

### 3.3. P2: exportaciones o snapshots en Blob Storage

Objetivo:

- Solicitar periódicamente una exportación o snapshot de LeanIX.
- Almacenar el artefacto original en Azure Blob Storage.
- Mantener una planificación independiente.

Alternativas pendientes de comparar:

#### Alternativa A: snapshots completos

- Cada ejecución conserva una fotografía completa.
- Consulta, explotación y recuperación conceptual más sencillas.
- Mayor transferencia y almacenamiento.
- No garantiza conservar estados intermedios ocurridos entre capturas.

#### Alternativa B: baseline completo más incrementales

- Se conserva una captura completa inicial.
- Después se almacenan snapshots incrementales.
- Potencial reducción de almacenamiento y transferencia.
- Mayor complejidad de cadena, bajas, orden, integridad y reconstrucción de un punto histórico.
- Si se elige esta alternativa, no se recomienda una cadena incremental indefinida: deberían renovarse baselines completos periódicamente.

Aitor pidió valorar la diferencia de tamaño entre snapshot completo e incremental antes de seleccionar la estrategia definitiva.

### 3.4. P3: extracción de diagramas

Objetivo confirmado:

- Conservar todos los diagramas accesibles del workspace.
- Almacenarlos en Azure Blob Storage.
- Utilizar un formato compatible con Draw.io.
- Mantener metadatos, versiones y relaciones con Fact Sheets.
- Conservar también la organización de LeanIX por grupos, colecciones y carpetas cuando la API y permisos lo permitan.

---

## 4. Diferencias frente al documento Word inicial

El Word original queda parcialmente superado en varios puntos.

### 4.1. Diagramas en Cosmos DB

El documento inicial incluía diagramas dentro de la representación de Cosmos DB. El criterio actual es:

- Cosmos DB: estado vivo transformado de Fact Sheets.
- Blob Storage: diagramas y artefactos históricos.

### 4.2. Baseline y deltas obligatorios

El Word planteaba una cadena de baseline y snapshots incrementales como diseño establecido. Tras la reunión y la respuesta de Aitor, la estrategia todavía debe decidirse comparando capturas completas frente a baseline más incrementales.

### 4.3. Histórico exhaustivo

Las capturas periódicas no garantizan conservar todos los estados intermedios. Por ejemplo, un elemento creado y eliminado entre dos capturas podría no aparecer en ninguna fotografía completa.

PUSH/PULL mantiene Cosmos actualizado, pero no convierte automáticamente Blob en un histórico exhaustivo de eventos.

### 4.4. Formato canónico propio

El Word hablaba de un formato canónico histórico. El planteamiento actual distingue:

- artefacto original en Blob;
- estructura transformada y consultable en Cosmos.

No se ha confirmado la necesidad de crear un segundo formato canónico para los artefactos de Blob.

### 4.5. Borrado lógico

En la primera revisión se consideró una decisión abierta. Aitor recomienda ya utilizar borrado lógico.

### 4.6. Restauración y reconstrucción

Aitor confirmó que no se espera ninguna garantía de reconstrucción porque está fuera de alcance. Es suficiente conservar un modelo entendible.

No se incluye:

- restaurador;
- reconstrucción automática de un workspace;
- prueba integral de restauración;
- garantía contractual de reconstrucción.

### 4.7. Observabilidad

La aplicación deberá generar logs estructurados para las herramientas corporativas de Ibercaja. No se deben desplegar ni mantener:

- Prometheus;
- Grafana;
- Application Insights propio;
- alertas propias;
- una plataforma de observabilidad propia.

---

## 5. Webhooks: PUSH y PULL

### 5.1. Decisión funcional

- PUSH será el mecanismo principal.
- PULL será la alternativa.
- Ibercaja deberá configurar el PUSH para las fichas o eventos requeridos.

### 5.2. Contrato separado

Los webhooks no forman parte del contrato Pathfinder. LeanIX publica una API independiente:

```text
/services/webhooks/v1
```

Swagger del entorno:

```text
https://ibercaja.leanix.net/openapi-explorer/?urls.primaryName=Webhooks
```

Versión observada:

```text
Webhooks 2.0.119
```

Autenticación:

```text
OAuth 2.0 client credentials
Token endpoint: /services/mtm/v1/oauth2/token
```

### 5.3. Operaciones PULL relevantes

Listar suscripciones:

```http
GET /services/webhooks/v1/subscriptions
```

Crear una suscripción:

```http
POST /services/webhooks/v1/subscriptions
```

Obtener el siguiente lote:

```http
GET /services/webhooks/v1/subscriptions/{id}/events
```

Confirmar los eventos consumidos:

```http
PUT /services/webhooks/v1/subscriptions/{id}/cursor
Content-Type: application/json

{
  "offset": 182
}
```

Consultar suscripción:

```http
GET /services/webhooks/v1/subscriptions/{id}
```

### 5.4. Semántica de consumo PULL

Flujo recomendado:

1. Obtener eventos.
2. Procesarlos.
3. Consultar Pathfinder o GraphQL si el payload no contiene todos los datos necesarios.
4. Transformar y persistir en Cosmos DB.
5. Registrar el checkpoint técnico.
6. Solo después, avanzar el cursor en LeanIX.

No se recomienda `autoCommit=true`, porque renuncia a la garantía de entrega al menos una vez y podría confirmar un lote antes de comprobar su persistencia correcta.

### 5.5. Restricciones documentadas

- Los eventos se retienen durante 30 días.
- Una suscripción PULL se desactiva tras 30 días sin pulls ni commits.
- LeanIX rechaza el procesamiento concurrente de la misma suscripción.
- Debe existir una suscripción PULL diferente por consumidor.
- `maxBatchSize` tiene valor predeterminado de 512 KiB y es un límite aproximado.

### 5.6. Implicaciones de diseño

- Una sola instancia activa por suscripción o exclusión mutua distribuida.
- No compartir suscripción entre local, PRE y producción.
- Monitorizar tiempo desde el último consumo correcto.
- Reconciliación completa si se supera la ventana de retención o se sospecha pérdida de eventos.
- Idempotencia ante reentregas.
- Confirmación manual del cursor.

---

## 6. APIs de diagramas

### 6.1. Pathfinder / bookmarks

Operaciones conocidas:

```http
GET /services/pathfinder/v1/bookmarks
GET /services/pathfinder/v1/bookmarks/{id}
GET /services/pathfinder/v1/bookmarks/{id}/versions
```

Los diagramas aparecen como bookmarks con:

```json
"type": "VISUALIZER"
```

El contenido XML se encuentra en:

```text
data.state.graphXml
```

El JSON real demuestra que el XML usa estructuras Draw.io/mxGraph:

```xml
<mxGraphModel>
  <root>
    <mxCell />
    <mxGeometry />
  </root>
</mxGraphModel>
```

Campos relevantes observados:

- `id`
- `userId`
- `lastUpdateUserId`
- `name`
- `type`
- `groupKey`
- `description`
- `createdAt`
- `updatedAt`
- `predefined`
- `readonly`
- `permittedReadUserIds`
- `permittedWriteUserIds`
- `referencedFactSheetIds`
- `views`
- `temporary`
- `version`
- `message`
- `state.graphXml`

Valores de `groupKey` observados:

- `freedraw`
- `dataflow`

`groupKey` representa el tipo/modalidad de diagrama, no la colección organizativa.

### 6.2. Diagrams V2

Base URL:

```text
/services/diagrams/v2
```

Versión observada:

```text
LeanIX Diagrams Service V2 1.0.1443
```

Operaciones principales:

```http
GET /diagrams
GET /diagrams/{id}
GET /diagrams/{id}/versions
GET /diagrams/{id}/versions/{version}
GET /diagrams/{id}/shares
```

`GET /diagrams` devuelve las últimas versiones de los diagramas que la identidad puede leer y admite filtro por:

```text
groupKey = freedraw | dataflow | lucidchart | target-architecture
```

Versiones:

```http
GET /diagrams/{id}/versions?includeState=true
```

permite solicitar el estado de cada versión.

```http
GET /diagrams/{id}/versions/{version}
```

recupera una versión concreta mediante `DiagramVersionDTO`, que incluye `state`.

Esto resuelve la duda técnica sobre la recuperación del XML histórico: la API dispone de una operación específica para obtener el estado de una versión concreta.

### 6.3. Evidencia real disponible

Para el diagrama probado se observaron cuatro versiones, numeradas del 1 al 4, con fecha de actualización y usuario asociado.

El diagrama actual contenía:

- XML en `state.graphXml`;
- versión actual;
- ID y nombre;
- descripción;
- Fact Sheets referenciados;
- metadatos de permisos.

Se realizó una prueba manual abriendo el XML en Draw.io y el diagrama se visualizó correctamente.

### 6.4. Relación con Fact Sheets

Cada diagrama incluye:

```json
"referencedFactSheetIds": ["..."]
```

Además, el XML puede contener:

```xml
<object
  type="factSheet"
  factSheetType="Estrategia"
  factSheetId="...">
```

Esto permite relacionar:

```text
Diagrama → Fact Sheets representados
```

Si se necesita la relación inversa:

```text
Fact Sheet → Diagramas en los que aparece
```

se puede construir un índice recorriendo `referencedFactSheetIds`. No es necesario que el endpoint de Fact Sheets contenga explícitamente los IDs de diagramas.

---

## 7. Organización por grupos, colecciones y carpetas

La API correcta es `Navigation`.

Base URL:

```text
/services/navigation/v1
```

La estructura disponible es:

```text
Collection Group
└── Collection
    └── Folder
        └── Navigation Item
            └── VISUALIZER
```

Un diagrama es un `Navigation Item` de tipo `VISUALIZER`.

### 7.1. Grupos y colecciones

```http
GET /collectionGroups
```

Devuelve grupos de colecciones y sus colecciones, con nombre, posición e `itemsCount`, que puede incluir recuentos de `VISUALIZER`.

### 7.2. Colecciones de un diagrama

```http
GET /collections?navigationItemId={diagramId}
```

El parámetro está documentado como filtro de colecciones que contienen el elemento de navegación.

### 7.3. Carpetas

```http
GET /collections/{collectionId}/folders
```

La respuesta incluye:

- `id`
- `name`
- `description`
- `parentId`
- `collectionId`
- `breadcrumbs`

`parentId` permite reconstruir la jerarquía.

### 7.4. Buscar diagramas dentro de Navigation

```http
GET /navigationItems/search?type=VISUALIZER
```

Filtros relevantes:

- `collectionId`
- `folderId`
- `factSheetId`
- `type=VISUALIZER`
- `adminCanQueryAll=true`
- `page`
- `pageSize`
- `permissions`
- `ownedByIds`

La respuesta `ResultPageWithBreadcrumbs` incluye elementos y breadcrumbs.

### 7.5. Cobertura y permisos

El contrato demuestra que la funcionalidad existe, pero durante la implementación debe validarse:

- que la identidad técnica pueda leer Navigation;
- que tenga visibilidad completa sobre los diagramas requeridos;
- que los IDs de Navigation coincidan con los IDs de Diagrams/Pathfinder;
- que `adminCanQueryAll=true` esté disponible para la identidad autorizada si se necesita cobertura global.

Esto es una validación de implementación, no un bloqueo para continuar el diseño.

---

## 8. Estrategia propuesta de almacenamiento de diagramas

Estructura conceptual en Blob:

```text
diagrams/
  {collectionGroup}/
    {collection}/
      {folderPath}/
        {diagramId}/
          metadata.json
          current/
            diagram.xml
          versions/
            1.xml
            2.xml
            3.xml
```

La ruta puede simplificarse físicamente usando IDs y guardar los nombres en metadatos para evitar problemas por renombrados:

```text
diagrams/{diagramId}/metadata.json
diagrams/{diagramId}/current/diagram.xml
diagrams/{diagramId}/versions/{version}.xml
```

En `metadata.json` convendría conservar:

```json
{
  "id": "...",
  "name": "...",
  "description": "...",
  "groupKey": "freedraw",
  "createdAt": "...",
  "updatedAt": "...",
  "version": 4,
  "predefined": false,
  "temporary": false,
  "referencedFactSheetIds": [],
  "collectionGroup": {},
  "collection": {},
  "folderPath": [],
  "permissions": {},
  "extraction": {}
}
```

Recomendación de diseño:

- El ID del diagrama debe ser la clave estable.
- Los nombres de grupo, colección, carpeta o diagrama pueden cambiar.
- La estructura organizativa de Navigation debe conservarse como metadato aunque también se refleje en la ruta de Blob.
- El XML actual y las versiones históricas deben guardarse por separado.
- Debe guardarse la respuesta original o un manifiesto técnico suficiente para trazabilidad.

---

## 9. Control técnico de ejecuciones

Aitor considera interesante conservar el estado e histórico de ejecuciones en Blob o Cosmos DB. La recomendación técnica consolidada es:

- Blob Storage para artefactos: snapshots, diagramas y manifiestos.
- Cosmos DB para control operativo consultable y recuperación de procesos.
- Logs corporativos para diagnóstico detallado.

Contenedor técnico propuesto en Cosmos DB:

- `executionId`
- tipo de proceso: P1, P2 o P3
- fecha de inicio y fin
- estado
- número de elementos procesados
- cursores o checkpoints
- último evento confirmado
- errores y reintentos
- referencias a blobs
- versión de contrato o metamodelo
- identidad técnica utilizada
- resultado de reconciliación

Este contenedor técnico no constituye histórico funcional del modelo LeanIX.

---

## 10. Observabilidad y trazabilidad

Responsabilidad del microservicio:

- logs estructurados;
- correlación;
- contexto de ejecución;
- errores y reintentos;
- contadores técnicos;
- integración con el formato corporativo.

Identificadores recomendados:

- `executionId`
- `correlationId`
- `traceId`
- `subscriptionId`
- `batchCursor`
- `diagramId`
- `factSheetId`

`userId` y `sessionId` solo se incluirán cuando exista realmente un actor humano y el estándar corporativo autorice su registro. Las ejecuciones programadas utilizarán identidad técnica.

Pendiente de confirmar con Ibercaja:

- nombre exacto de la plataforma mencionada verbalmente como “Inside”;
- formato de ingestión;
- campos obligatorios;
- retención;
- gestión de dashboards y alertas;
- tratamiento de datos sensibles.

---

## 11. Identidad técnica, permisos y entorno

Entorno:

- LeanIX productivo es el único origen disponible comunicado.
- Se utilizará una identidad técnica de lectura para pruebas y ejecución.

“Solo lectura” debe entenderse funcionalmente:

- no crear, modificar, archivar ni eliminar elementos del modelo;
- sí permitir operaciones POST que crean jobs de exportación sin modificar Fact Sheets;
- sí permitir lectura de Pathfinder, Webhooks, Diagrams y Navigation según alcance.

Permisos necesarios:

- obtener token OAuth;
- leer Fact Sheets y relaciones;
- solicitar y descargar exports;
- leer suscripciones/eventos PULL y avanzar cursor;
- acceder a diagramas y versiones;
- leer colecciones y carpetas;
- leer metamodelo y datos de contexto acordados.

La administración o creación de webhooks puede requerir una identidad o procedimiento distinto.

---

## 12. Estimación disponible

El Excel de estimación incluye partidas para:

- modelo Cosmos;
- carga inicial y transformación;
- sincronización incremental;
- bajas, reconciliación y recuperación;
- exportación global y descarga original;
- archivo Blob e integridad de capturas;
- extracción completa de diagramas;
- ejecución independiente de procesos;
- logs estructurados;
- configuración OpenShift.

Valores identificados en la revisión:

- total central sin IA: 670 horas-persona;
- total central con IA: 428,8 horas-persona;
- bloque de microservicio central sin IA: 414,1 horas;
- bloque de microservicio central con IA: 265,024 horas;
- reducción configurada: 36 %.

La estimación debe revisarse al cerrar:

- estrategia full versus incremental;
- retención;
- periodicidad;
- cobertura de versiones de diagramas;
- permisos de Navigation;
- reconciliación completa;
- modelo de ejecuciones y checkpoints.

---

## 13. Decisiones confirmadas

1. Un único microservicio en OpenShift.
2. Tres responsabilidades lógicas independientes.
3. Cosmos DB mantendrá el estado vivo y transformado.
4. Cosmos DB no almacenará los diagramas.
5. Cosmos DB no será un histórico funcional completo.
6. PUSH será el mecanismo principal.
7. PULL será el mecanismo alternativo.
8. Las bajas se gestionarán mediante borrado lógico.
9. La actualización ordinaria no consistirá en descargar diariamente todos los Fact Sheets.
10. Se realizará una carga inicial completa.
11. Se valorará una reconciliación completa periódica.
12. Los artefactos históricos se almacenarán en Blob Storage.
13. Todos los diagramas accesibles deberán conservarse.
14. El formato de diagramas es XML compatible con Draw.io.
15. El XML real probado se abrió correctamente en Draw.io.
16. Los diagramas se relacionan con Fact Sheets mediante `referencedFactSheetIds` y el XML.
17. LeanIX mantiene versiones de diagramas.
18. Diagrams V2 permite recuperar una versión concreta.
19. Navigation expone grupos, colecciones y carpetas.
20. El estado técnico de ejecuciones no debe residir únicamente en logs.
21. No se desplegarán Prometheus ni Grafana.
22. No se implementará un restaurador.
23. No existe garantía de reconstrucción dentro del alcance.
24. Es suficiente conservar un modelo y artefactos entendibles y explotables.

---

## 14. Puntos abiertos o validaciones de implementación

### Funcionales y de diseño

- Elegir snapshots completos o baseline más incrementales.
- Medir tamaño real de ambos tipos.
- Definir frecuencia y retención.
- Definir periodicidad de renovación del baseline si se usan incrementales.
- Confirmar tratamiento exacto de bajas en snapshots incrementales.
- Definir formato del marcador de borrado lógico en Cosmos.
- Definir cobertura exacta de Fact Sheets, relaciones, metamodelo, etiquetas y taxonomías.

### Diagramas

- Verificar que la identidad técnica ve todos los diagramas requeridos.
- Confirmar permisos sobre Diagrams V2 y Navigation.
- Verificar el cruce real de IDs entre servicios.
- Decidir si se conservan todas las versiones o solo la actual.
- Definir retención de versiones.
- Decidir si la estructura de colecciones se refleja físicamente en rutas o solo en metadatos.

### Operación

- Elegir Cosmos DB o combinación Cosmos/Blob para el registro técnico definitivo.
- Definir idempotencia y deduplicación de eventos.
- Definir exclusión mutua del consumidor PULL.
- Definir procedimiento si se superan 30 días de retención.
- Definir observabilidad corporativa y alertas.
- Definir acceso desde local y OpenShift al entorno productivo.

---

## 15. Riesgos principales

1. **Cobertura incompleta por permisos:** la API devuelve los diagramas que la identidad puede leer, no garantiza por sí sola cobertura global.
2. **Pérdida de eventos PULL:** los eventos caducan a los 30 días.
3. **Confirmación anticipada:** `autoCommit=true` puede perder eventos tras fallos de procesamiento.
4. **Cadena incremental extensa:** aumenta complejidad y riesgo de rotura.
5. **Cambios de metamodelo:** requieren detección, versionado y adaptación de transformación.
6. **Relaciones rotas:** un diagrama puede referenciar Fact Sheets eliminados o dados de baja.
7. **Renombrado organizativo:** colecciones y carpetas pueden cambiar; los IDs deben ser la clave estable.
8. **Ambigüedad en la promesa de reconstrucción:** debe mantenerse explícitamente fuera de alcance.
9. **Exposición de datos productivos:** las pruebas usan LeanIX productivo y requieren controles de seguridad.
10. **Dependencia de APIs internas o con permisos específicos:** Navigation y administración de colecciones pueden necesitar permisos adicionales.

---

## 16. Propuesta de apartado de diagramas para comunicación con Ibercaja

> Hemos comprobado que los diagramas pueden recuperarse en XML compatible con Draw.io y relacionarse con los Fact Sheets mediante los identificadores incluidos en la respuesta. Las APIs disponibles permiten también consultar versiones históricas y obtener la organización por grupos, colecciones y carpetas. Por tanto, almacenaremos en Blob Storage los XML junto con sus metadatos, versiones y referencias organizativas. Durante la implementación verificaremos que la identidad técnica dispone de visibilidad completa sobre los diagramas y permisos de lectura sobre las APIs Diagrams y Navigation.

---

## 17. Propuesta de arquitectura de alto nivel

```text
                            SAP LeanIX Productivo
                                     │
             ┌───────────────────────┼────────────────────────┐
             │                       │                        │
        Pathfinder / GraphQL      Webhooks API           Diagrams V2
             │                  PUSH / PULL                  │
             │                       │                        │
             └───────────────┬───────┴───────────┬────────────┘
                             │                   │
                    Microservicio OpenShift      │
                             │                   │
              ┌──────────────┼──────────────┐    │
              │              │              │    │
             P1             P2             P3   Navigation API
              │              │              │    │
          Cosmos DB     Blob snapshots   Blob XML + metadatos
              │                             + versiones
              │
       Estado vivo + control técnico
```

---

## 18. Flujo técnico propuesto para P1

```text
Carga inicial completa
        ↓
Transformación
        ↓
Upsert en Cosmos DB
        ↓
Activación de PUSH
        ↓
Recepción y validación de evento
        ↓
Consulta de detalle cuando sea necesaria
        ↓
Transformación e idempotencia
        ↓
Upsert o borrado lógico
        ↓
Registro técnico y logs
```

Contingencia PULL:

```text
Scheduler
   ↓
GET eventos
   ↓
Procesar lote
   ↓
Persistir en Cosmos
   ↓
Registrar checkpoint
   ↓
PUT cursor
```

---

## 19. Flujo técnico propuesto para P3

```text
GET /services/diagrams/v2/diagrams
        ↓
Inventario de diagramas accesibles
        ↓
Por cada diagramId:
    GET /diagrams/{id}
    GET /diagrams/{id}/versions
    GET /diagrams/{id}/versions/{version}
        ↓
Cruce con Navigation:
    GET /collections?navigationItemId={diagramId}
    GET /collections/{collectionId}/folders
    GET /navigationItems/search?type=VISUALIZER
        ↓
Extraer graphXml + metadatos + referencedFactSheetIds
        ↓
Persistir XML, versiones y metadata.json en Blob
        ↓
Registrar ejecución y checksums
```

---

## 20. Criterios de aceptación sugeridos

### P1

- La carga inicial procesa todos los Fact Sheets visibles para la identidad autorizada.
- Cosmos contiene una proyección consultable.
- PUSH actualiza altas, modificaciones y bajas.
- PULL puede sustituir a PUSH sin cambiar el modelo de persistencia.
- Las bajas se representan mediante borrado lógico.
- El procesamiento es idempotente.
- Los checkpoints permiten reanudar sin pérdida ni doble aplicación funcional.

### P2

- Se genera y almacena el artefacto esperado.
- Se registra fecha, tipo, tamaño, checksum y versión de contrato.
- La estrategia full/incremental queda documentada.
- La retención se aplica según configuración.

### P3

- Se enumeran todos los diagramas accesibles.
- Cada XML almacenado puede abrirse en Draw.io para una muestra representativa.
- Se conservan los Fact Sheets referenciados.
- Se conservan metadatos y versiones conforme al alcance.
- Se conserva la organización por colecciones y carpetas cuando los permisos lo permiten.
- Los fallos parciales quedan registrados y son reintentables.

---

## 21. Glosario

- **Fact Sheet:** entidad del modelo de LeanIX.
- **Visualizer:** tipo de elemento de navegación que representa un diagrama.
- **Bookmark:** representación histórica usada por Pathfinder para informes y diagramas.
- **groupKey:** modalidad del diagrama, como `freedraw` o `dataflow`; no equivale a colección.
- **Collection Group:** agrupación superior de colecciones en Navigation.
- **Collection:** agrupación de elementos navegables.
- **Folder:** jerarquía interna dentro de una colección.
- **Navigation Item:** elemento organizable, como un diagrama `VISUALIZER`.
- **graphXml:** XML mxGraph/Draw.io que contiene la representación del diagrama.
- **PUSH webhook:** LeanIX envía eventos al microservicio.
- **PULL webhook:** el microservicio consulta periódicamente eventos pendientes.
- **Cursor:** offset que marca hasta qué evento se ha confirmado el consumo.
- **Baseline:** captura completa usada como base.
- **Incremental:** captura que contiene cambios respecto de una referencia.
- **Borrado lógico:** conservar el documento marcándolo como inactivo o eliminado.

---

## 22. Próximo criterio de trabajo

Con el conocimiento actual se puede continuar con diseño y estimación sin solicitar más pruebas inmediatas al compañero.

Las comprobaciones restantes deben integrarse como validaciones tempranas de implementación:

1. autenticación de la identidad técnica;
2. permisos sobre Pathfinder, Webhooks, Diagrams y Navigation;
3. cobertura global de diagramas;
4. cruce de IDs;
5. descarga de versiones históricas;
6. comparación real de snapshots completos e incrementales.

Este documento debe actualizarse cuando Ibercaja confirme la estrategia de snapshots, retención, permisos y contrato de observabilidad.
