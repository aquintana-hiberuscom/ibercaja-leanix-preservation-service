# Contenido inicial para los repositorios Ibercaja Baseline y LeanIX

**Fecha:** 2026-10-27
**Estado:** Preparacion documental previa a implementacion.

## 1. Objetivo inmediato

Sustituir los ficheros Markdown vacios por una primera version util y honesta. No se generara todavia la solucion .NET. Las decisiones no confirmadas quedaran como provisionales o abiertas.

## 2. Repositorio `ibercaja-dotnet-service-baseline`

### 2.1. `README.md`

Debe explicar:

- proposito del baseline;
- que contiene y que no contiene;
- como se aplica a un servicio consumidor;
- que Git es la fuente de verdad;
- que un Copilot Space distribuye contexto pero no certifica cumplimiento;
- version y estado del baseline;
- flujo para proponer cambios reutilizables.

### 2.2. `BASELINE_VERSION.md`

Primera version recomendada:

```text
Version: 0.1.0-provisional
Estado: Provisional
Origen: baseline depurado de Planificacion Sucesoria
Validacion pendiente: nueva wiki de Ibercaja
```

### 2.3. `docs/sources/SOURCE_CATALOG.md`

Registrar al menos:

- snapshot `origin/main` de PS en commit `cab98f14f0f0c62e15c59f00381d835cdd36e852`;
- inventarios PS 01 a 05;
- nueva wiki Ibercaja, cuando se incorpore;
- ADR corporativos o de proyecto;
- estado: informativo, provisional, aprobado o superado.

### 2.4. `docs/architecture/ARCHITECTURE_BASELINE.md`

Incluir provisionalmente:

- servicios por capacidad;
- composicion separada de negocio;
- endpoints delgados;
- contratos explicitos;
- Application independiente del transporte y adaptadores;
- Infrastructure como frontera de persistencia e integraciones;
- decisiones concretas de .NET, Aspire, Minimal APIs y dispatcher pendientes de ratificacion.

### 2.5. `docs/security/SECURITY_BASELINE.md`

Conservar:

- clasificacion de aplicabilidad;
- activos, actores, fronteras y entradas;
- minimizacion;
- fail-closed;
- secretos fuera de Git;
- pruebas negativas;
- supply chain;
- excepciones gobernadas;
- riesgo residual;
- no asumir que SAST, cobertura o tests certifican seguridad.

### 2.6. `docs/observability/OBSERVABILITY_BASELINE.md`

Conservar:

- separacion entre logs, trazas y auditoria;
- logging estructurado;
- correlacion desde el contexto disponible;
- no registrar tokens, secretos, headers o payloads completos;
- catalogo por proyecto;
- destinos y retencion pendientes de fuente corporativa.

### 2.7. `docs/testing/TESTING_BASELINE.md`

Conservar:

- test mas barato que demuestra el comportamiento;
- contract tests para cambios de contrato;
- pruebas relacionales o de proveedor sobre tecnologia real o emulada de forma aprobada;
- aceptacion observable;
- prohibicion de declarar validaciones no ejecutadas;
- cobertura como gate pendiente de politica corporativa.

### 2.8. `docs/delivery/DELIVERY_BASELINE.md`

Incluir:

- feedback focalizado durante implementacion;
- gates amplios en checkpoints y cierre;
- evidencia publicada;
- no auto-commit, push, approval o merge;
- pipeline concreto pendiente de plataforma corporativa.

### 2.9. `docs/delivery/DEFINITION_OF_DONE.md`

Una vertical slice termina cuando:

- cumple especificacion y arquitectura aprobadas;
- contratos y configuracion estan versionados;
- pruebas aplicables pasan;
- seguridad y observabilidad estan cubiertas;
- trazabilidad esta actualizada;
- validaciones no ejecutadas quedan declaradas;
- handoff esta actualizado;
- no introduce secretos ni decisiones no aprobadas.

### 2.10. `docs/traceability/TRACEABILITY_BASELINE.md`

Cadena minima:

```text
Fuente -> requisito -> decision -> contrato -> implementacion -> prueba -> evidencia -> handoff
```

### 2.11. `.github/copilot-instructions.md`

Debe ser breve y obligar a:

- leer `AGENTS.md`;
- respetar jerarquia de fuentes;
- no inventar decisiones;
- registrar contradicciones;
- usar vertical slices;
- aplicar seguridad y testing;
- actualizar trazabilidad y handoff;
- no hacer commit o push sin autorizacion.

### 2.12. `AGENTS.md`

Debe definir:

- orden de lectura;
- precedencia;
- modo de rechazo ante conflicto;
- protocolo antes, durante y despues;
- criterio de parada;
- limites de escritura por fase.

## 3. Repositorio `ibercaja-leanix-preservation-service`

### 3.1. `docs/sources/SOURCE_CATALOG.md`

Incluir inicialmente:

- dossier LeanIX de 2026-10-23;
- plan de arranque de 2026-10-27;
- clasificacion del baseline de PS;
- contratos LeanIX cuando se incorporen;
- nueva wiki Ibercaja;
- ejemplos reales saneados;
- decisiones confirmadas y responsables cuando se documenten.

### 3.2. `docs/baseline/PS_BASELINE_EXTRACTION.md`

Incluir la matriz de clasificacion completa y el commit fuente de PS.

### 3.3. `docs/baseline/BASELINE_SCOPE.md`

Declarar:

- baseline aplicado provisionalmente;
- reglas reutilizadas;
- reglas que requieren validacion;
- elementos descartados;
- diferencias esperadas por OpenShift, Cosmos DB, Blob Storage y ausencia de frontend.

### 3.4. `docs/baseline/BASELINE_APPLIED.md`

Contenido inicial:

```text
Baseline: ibercaja-dotnet-service-baseline
Version: 0.1.0-provisional
Estado: pendiente de validacion con wiki Ibercaja
Origen parcial: PS origin/main cab98f14f0f0c62e15c59f00381d835cdd36e852
```

### 3.5. `docs/specification/SPECIFICATION.md`

Separar:

- P1: proyeccion viva de Fact Sheets en Cosmos DB;
- P2: snapshots o exports en Blob Storage;
- P3: diagramas, XML, versiones, metadatos y Navigation;
- carga inicial;
- PUSH principal;
- PULL alternativo;
- bajas logicas;
- reconciliacion;
- idempotencia;
- checkpoints;
- fallos parciales;
- ejecucion independiente.

### 3.6. `docs/specification/OUT_OF_SCOPE.md`

Incluir:

- restaurador de LeanIX;
- reconstruccion automatica del workspace;
- garantia contractual de restauracion;
- historico funcional completo en Cosmos DB;
- plataforma propia de observabilidad;
- capacidades no respaldadas por contratos o decisiones.

### 3.7. `docs/specification/OPEN_QUESTIONS.md`

Mantener separadas las decisiones abiertas sobre:

- .NET y Aspire;
- identidad, OAuth, scopes y permisos;
- endpoints y limites;
- full frente a baseline e incrementales;
- tamaños, frecuencia y retencion;
- borrado logico;
- cobertura del metamodelo;
- versiones de diagramas;
- cruce de IDs;
- Cosmos DB;
- Blob Storage;
- concurrencia y exclusión mutua;
- OpenShift;
- logs corporativos;
- RTO/RPO, recuperacion y operacion.

### 3.8. `docs/specification/ACCEPTANCE_CRITERIA.md`

Definir criterios verificables para P1, P2 y P3. Cada criterio debe identificar propietario de prueba: unit, contract, integration, E2E tecnico o manual gobernado.

### 3.9. `docs/specification/DECISION_LOG.md`

Estados recomendados:

- `PROPOSED`;
- `CONFIRMED`;
- `REJECTED`;
- `SUPERSEDED`;
- `CORPORATE_INPUT_REQUIRED`;
- `REQUIRES_ADR`.

### 3.10. `docs/specification/TRACEABILITY.md`

Matriz minima:

| Requisito | Fuente | Decision | Contrato | Componente | Prueba | Evidencia | Estado |
|---|---|---|---|---|---|---|---|

### 3.11. `docs/architecture/ARCHITECTURE_TARGET.md`

Decisiones conocidas:

- microservicio .NET;
- despliegue en OpenShift;
- Cosmos DB para P1;
- Blob Storage para P2 y P3;
- PUSH principal y PULL alternativo;
- logs estructurados hacia plataforma corporativa;
- sin plataforma de observabilidad propia.

No cerrar aun:

- version exacta de .NET;
- Aspire;
- estructura de proyectos;
- particionado de Cosmos;
- scheduler;
- pods y jobs;
- identidad y secretos;
- resiliencia;
- health checks;
- concurrencia;
- despliegue independiente de procesos.

### 3.12. `docs/architecture/INTEGRATION_CONTRACTS.md`

Crear secciones para:

- Pathfinder/GraphQL;
- Webhooks API;
- Diagrams V2;
- Navigation;
- OAuth 2.0;
- Cosmos DB;
- Blob Storage;
- logs corporativos.

Para cada contrato registrar:

- fuente y version;
- hash cuando aplique;
- operaciones evaluadas;
- operaciones permitidas;
- autenticacion;
- limites;
- errores;
- datos sensibles;
- test double;
- estado de conectividad.

### 3.13. `docs/delivery/READINESS.md`

Usar controles reducidos en estas categorias:

1. alcance;
2. decisiones funcionales;
3. seguridad;
4. contratos externos;
5. arquitectura;
6. datos y persistencia;
7. plan y pruebas;
8. operacion y despliegue;
9. trazabilidad;
10. bloqueos y excepciones.

Estados:

- `PASS`;
- `FAIL`;
- `NOT_APPLICABLE`;
- `NOT_EVALUATED`.

Resultado:

- `READY`;
- `NOT_READY`;
- `READY_WITH_CONDITIONS`.

### 3.14. `docs/delivery/HANDOFF.md`

Mantener una plantilla compacta con:

- metadatos;
- alcance y no incluido;
- archivos;
- contratos;
- persistencia;
- integraciones;
- pruebas;
- seguridad;
- observabilidad;
- decisiones;
- reutilizacion;
- validaciones ejecutadas y no ejecutadas;
- riesgos y deuda;
- resumen compacto.

## 4. Orden de trabajo recomendado

1. Copiar la clasificacion del baseline a LeanIX.
2. Rellenar `SOURCE_CATALOG.md` en ambos repositorios.
3. Rellenar README, version y alcance del baseline generico.
4. Crear first draft de arquitectura, seguridad, observabilidad, testing, delivery y trazabilidad genericos.
5. Crear instrucciones iniciales de Copilot y `AGENTS.md`.
6. Descomponer el dossier LeanIX en especificacion, fuera de alcance, preguntas, criterios y contratos.
7. Incorporar y analizar la nueva wiki.
8. Revisar y cambiar decisiones provisionales.
9. Ejecutar readiness documental.
10. Solo entonces generar la solucion .NET.

## 5. Regla de commit inicial

El primer commit debe contener documentos con contenido real, no solo esqueletos vacios. Mensajes sugeridos:

```text
chore: initialize reusable Ibercaja .NET service baseline
```

```text
chore: initialize LeanIX preservation service documentation
```

No hacer push hasta revisar:

- que no hay secretos;
- que ningun dato de infraestructura de PS se ha copiado;
- que las decisiones pendientes aparecen como pendientes;
- que el repositorio generico no contiene conceptos de LeanIX o sucesiones;
- que el repositorio LeanIX registra el origen y version del baseline aplicado.
