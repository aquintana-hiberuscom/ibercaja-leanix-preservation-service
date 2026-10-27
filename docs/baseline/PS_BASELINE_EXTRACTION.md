# Clasificacion del baseline de Planificacion Sucesoria para reutilizacion en Ibercaja y LeanIX

**Fecha:** 2026-10-27
**Fuente analizada:** `origin/main` de Planificacion Sucesoria, commit `cab98f14f0f0c62e15c59f00381d835cdd36e852`.
**Objetivo:** separar reglas reutilizables, reglas sujetas a validacion corporativa, patrones que deben reescribirse y elementos que no deben trasladarse al nuevo proyecto LeanIX.

## 1. Conclusion ejecutiva

Planificacion Sucesoria contiene un baseline tecnico y de gobierno valioso, pero no debe copiarse como una plantilla cerrada. Mezcla reglas transversales con decisiones propias de Planificacion Sucesoria, PostgreSQL, Angular, Service Fabric, Azure, contratos concretos de Ibercaja y excepciones historicas de User Stories.

La estrategia aprobada es:

1. Reutilizar el modelo de autoridad, trazabilidad, seguridad, contratos, observabilidad, pruebas, readiness y handoff.
2. Convertir las reglas tecnicas concretas en decisiones provisionales hasta contrastarlas con la nueva wiki de Ibercaja.
3. Sustituir el flujo Spec Kit completo por un SDD reducido, manteniendo Specify, Clarify, Analyze, Readiness, Implement, Verify y Handoff.
4. No copiar datos de infraestructura, secretos, identificadores, endpoints, Service Fabric, PostgreSQL, Angular ni excepciones de PS.
5. Mantener Git como fuente de verdad y utilizar Copilot Spaces como mecanismo de distribucion de contexto.

## 2. Matriz de clasificacion

| Area | Decision | Destino | Tratamiento |
|---|---|---|---|
| Jerarquia de fuentes y precedencia | COPIAR Y ADAPTAR | Baseline generico | Mantener una unica jerarquia canonica, sustituyendo referencias de PS por fuentes corporativas y ADR del nuevo proyecto. |
| `AGENTS.md` como punto de entrada | COPIAR Y SIMPLIFICAR | Ambos repositorios | Mantener lectura obligatoria, limites de autoridad, prohibicion de inventar decisiones y protocolo de cierre. |
| `.github/copilot-instructions.md` | COPIAR Y SIMPLIFICAR | Ambos repositorios | Debe enlazar a los documentos autoritativos, no duplicarlos. |
| Instrucciones por ruta | COPIAR COMO PATRON | Baseline generico | Conservar ficheros separados para .NET, arquitectura, seguridad, testing y documentacion. |
| .NET 10 / `net10.0` | VALIDAR | Baseline generico | Es una decision vigente en PS, pero LeanIX debe confirmarla con la nueva wiki y el soporte corporativo. |
| `global.json` | ADAPTAR | Baseline generico | Crear solo cuando la version exacta de SDK este aprobada. |
| `Directory.Build.props` | COPIAR COMO PATRON | Baseline generico | Nullable, implicit usings, warnings as errors y analizadores son reutilizables, sujetos a validacion. |
| `Directory.Packages.props` | COPIAR COMO PATRON | Baseline generico | Mantener gestion centralizada, sin copiar la lista completa de paquetes ni sus versiones. |
| Minimal APIs | VALIDAR | Baseline generico | Mantener como propuesta arquitectonica, no como hecho corporativo hasta aprobar `ARCHITECTURE_TARGET.md`. |
| Endpoints delgados | COPIAR | Baseline generico | Routing, binding, autorizacion declarativa y delegacion; sin negocio ni acceso directo a persistencia. |
| Request/handler y dispatcher interno | REEVALUAR | LeanIX | Es un ADR especifico de PS. Puede ser util, pero no debe imponerse sin evaluar la complejidad del microservicio. |
| MediatR prohibido por defecto | NO COPIAR COMO REGLA | LeanIX | Solo registrar que no se incorpora sin una necesidad aprobada. |
| AppHost y ServiceDefaults | VALIDAR | LeanIX | .NET Aspire puede ser util, pero no debe copiarse sin confirmar compatibilidad con OpenShift y la arquitectura Ibercaja. |
| ProblemDetails | COPIAR COMO PATRON | Baseline generico | Aplicable a APIs HTTP si existen endpoints de negocio o administracion. |
| OpenAPI y versionado | COPIAR COMO PATRON | Ambos | Versionar contratos y evitar cambios incompatibles sin estrategia explicita. |
| Contratos externos inmutables | COPIAR | Baseline generico | Conservar OpenAPI original, hash, metadata y manifiesto separado de operaciones permitidas. |
| `operations.yml` | COPIAR COMO PATRON | LeanIX | Adaptar para Pathfinder/GraphQL, Webhooks, Diagrams V2 y Navigation. |
| Capa anticorrupcion de integraciones | COPIAR | Baseline generico | Clientes y DTO externos en Infrastructure; Application usa puertos y modelos internos. |
| Backend-to-backend | ADAPTAR | LeanIX | Aplicable a LeanIX, pero OAuth, cliente, scopes, endpoints y permisos deben quedar pendientes hasta confirmacion. |
| No inferir autorizacion del OpenAPI | COPIAR | Baseline generico | La disponibilidad del contrato no autoriza operaciones ni conectividad. |
| Secretos fuera de Git | COPIAR | Baseline generico | Mantener siempre. |
| Azure Key Vault | VALIDAR | LeanIX | PS lo usa para Azure. LeanIX se despliega en OpenShift y debe usar el mecanismo corporativo aprobado. |
| Managed Identity | NO PRESUPONER | LeanIX | No aplica automaticamente a OpenShift. Registrar mecanismo de identidad y secretos como decision pendiente. |
| Observabilidad estructurada | COPIAR | Baseline generico | Separar logs, trazas, auditoria de negocio, auditoria de seguridad y explicaciones. |
| `Activity` y correlacion | ADAPTAR | LeanIX | Reutilizar si la plataforma .NET y los headers corporativos lo permiten; no inventar cabeceras. |
| Prohibicion de payloads y secretos en logs | COPIAR | Baseline generico | Aplicacion directa a tokens, respuestas LeanIX, XML, Fact Sheets y datos de configuracion. |
| Catalogo de eventos | COPIAR COMO PLANTILLA | LeanIX | Los eventos deben derivarse de criterios de aceptacion de P1, P2 y P3. |
| Threat modeling focalizado | COPIAR | Baseline generico | Aplicabilidad, activos, actores, fronteras, amenazas, controles, pruebas negativas y riesgo residual. |
| Estados de seguridad | ADAPTAR | Baseline generico | Mantener estados equivalentes a APPLICABLE, NOT_APPLICABLE, BLOCKED y EXCEPTION_REQUIRED. |
| Test mas barato que demuestra comportamiento | COPIAR | Baseline generico | Unitario, funcional/contrato o E2E segun el riesgo. |
| Cobertura global del 80 % | VALIDAR | LeanIX | Es politica de PS; no trasladarla como obligatoria sin validar la politica del nuevo proyecto. |
| Gherkin por criterio observable | COPIAR COMO PATRON | LeanIX | Util para P1, P2 y P3, sin obligar a duplicar escenarios entre runners. |
| Pruebas contractuales | COPIAR | LeanIX | Necesarias para APIs LeanIX y almacenamiento cuando exista contrato verificable. |
| Testcontainers PostgreSQL | DESCARTAR | LeanIX | LeanIX usa Cosmos DB y Blob Storage; deben definirse emuladores, dobles o recursos de prueba adecuados. |
| EF Core/Npgsql/PostgreSQL | DESCARTAR | LeanIX | Especifico de PS. |
| Cosmos DB y Blob Storage | DEFINIR | LeanIX | Modelo, particionado, consistencia, idempotencia, retencion y pruebas quedan por decidir. |
| Readiness con evidencia | COPIAR Y REDUCIR | Ambos | Mantener PASS/FAIL/N/A/NOT_EVALUATED, hallazgos y excepciones gobernadas, pero no copiar los controles ligados a PS. |
| Excepcion AB-1103 | DESCARTAR | Ambos | Es historica y exclusiva de PS. |
| Excepciones controladas de PS | DESCARTAR | Ambos | No trasladar excepciones ni fingerprints. |
| Analyze no destructivo | COPIAR | Ambos | Revisar consistencia entre especificacion, arquitectura, plan, tareas, contratos y trazabilidad. |
| Clarify | COPIAR | Ambos | Resolver ambiguedades materiales y registrar respuestas en artefactos versionados. |
| Handoff obligatorio | COPIAR Y REDUCIR | Ambos | Mantener hechos, archivos, contratos, pruebas, decisiones, riesgos, reutilizacion y validaciones. |
| Gate integral | COPIAR COMO CONCEPTO | Baseline generico | El comando y suites reales deben definirse para LeanIX. |
| Docker reproducible | COPIAR COMO OBJETIVO | LeanIX | No copiar `compose.yml`; diseñar entorno para .NET, Cosmos/Blob o emuladores aprobados. |
| Dockerfiles de PS | REESCRIBIR | LeanIX | Conservar multi-stage, usuario no root, imagen fijada y sin secretos. |
| Azure Pipelines de PS | DESCARTAR COMO IMPLEMENTACION | LeanIX | Contiene recursos y despliegue Service Fabric especificos. Solo extraer patrones de stages y publicacion de evidencia. |
| Service Fabric PRE | DESCARTAR | LeanIX | LeanIX se orienta a OpenShift. |
| Angular, Playwright frontend e IberFront | DESCARTAR INICIALMENTE | LeanIX | No hay frontend confirmado. |
| Lenguaje ubicuo sucesorio | DESCARTAR | LeanIX | Crear vocabulario propio de LeanIX si resulta necesario. |
| Un Work Item, rama, feature, handoff y PR | ADAPTAR | Baseline generico | Mantener trazabilidad si encaja con el flujo del equipo y la herramienta de gestion usada. |
| No auto-commit, auto-push o merge | COPIAR | Ambos | Las acciones reservadas siguen bajo autorizacion humana. |

## 3. Contenido que debe ir al repositorio generico

Repositorio: `ibercaja-dotnet-service-baseline`.

Debe incluir:

- jerarquia de fuentes;
- entrada universal mediante `AGENTS.md`;
- instrucciones de Copilot por ambito;
- baseline .NET provisional;
- APIs y contratos;
- integraciones externas;
- secretos y configuracion;
- observabilidad y auditoria;
- seguridad y threat modeling;
- estrategia de pruebas;
- SDD reducido;
- readiness basado en evidencia;
- Definition of Done;
- handoff compacto;
- validadores genericos que no dependan del dominio ni del proveedor de persistencia.

No debe incluir:

- nombres de proyectos `EstatePlanning.*`;
- Planificacion Sucesoria;
- Angular e IberFront;
- PostgreSQL/Npgsql como obligacion;
- Cosmos DB o LeanIX como obligacion;
- Service Fabric, recursos Azure o endpoints PRE;
- AB-1103, AB-9908 u otras excepciones;
- IDs, credenciales, suscripciones o infraestructura concreta;
- contratos Alfabético Personas o Posicion Global.

## 4. Contenido que debe ir al repositorio LeanIX

Repositorio: `ibercaja-leanix-preservation-service`.

Debe incluir:

- fuentes y jerarquia de autoridad especificas;
- especificacion de P1, P2 y P3;
- fuera de alcance;
- contratos LeanIX y operaciones permitidas;
- OAuth y provision como decisiones separadas;
- Cosmos DB y Blob Storage;
- webhooks PUSH y contingencia PULL;
- idempotencia, checkpoints y reconciliacion;
- tratamiento y retencion de snapshots y versiones;
- diagramas, XML y Navigation;
- observabilidad segura;
- arquitectura OpenShift;
- decision sobre .NET, Aspire, estructura y testing;
- matriz de trazabilidad;
- readiness y handoff.

## 5. SDD reducido que se conservara

```text
Context
  -> Specify
  -> Clarify
  -> Architecture
  -> Analyze
  -> Readiness
  -> Plan by vertical slice
  -> Implement
  -> Verify
  -> Handoff
```

Reglas:

- las decisiones viven en el repositorio;
- las preguntas abiertas no se convierten en supuestos;
- Analyze es no destructivo;
- Readiness solo modifica su evaluacion;
- Implement no comienza con bloqueos materiales no gobernados;
- cada vertical slice incluye contrato, codigo, persistencia, observabilidad, pruebas y trazabilidad;
- las pruebas focalizadas preceden al gate integral;
- commit y push requieren autorizacion humana.

## 6. Proximo entregable

El siguiente paso es poblar una primera version documental de ambos repositorios, sin generar todavia la solucion .NET. La version inicial debe marcar como `PROVISIONAL` cualquier decision pendiente de la nueva wiki, especialmente:

- version de .NET;
- uso de Aspire;
- OpenShift;
- identidad y secretos;
- OAuth y permisos LeanIX;
- plataforma de logs;
- Cosmos DB y Blob Storage;
- CI/CD;
- cobertura y gates;
- retencion, RTO/RPO y operacion productiva.
