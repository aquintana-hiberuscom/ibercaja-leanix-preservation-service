# Plan de arranque del microservicio SAP LeanIX para Ibercaja

## SDD híbrido con GitHub Copilot Spaces y baseline reutilizable de Planificación Sucesoria

**Fecha:** 27 de octubre de 2026
**Estado:** Plan inicial de arranque, pendiente de incorporar y contrastar la nueva wiki de Ibercaja.
**Fuente funcional inicial:** `LeanIX_Ibercaja_Contexto_Completo_2026-10-23.md`.

---

# 1. Decisión recomendada

Se creará directamente el repositorio real del nuevo microservicio LeanIX. No se hará una copia directa de Spec Kit ni del proyecto de Planificación Sucesoria. Se construirá un baseline SDD híbrido, ligero y versionado, que:

- reutilice solo las reglas transversales válidas de Planificación Sucesoria;
- use la nueva wiki de Ibercaja como autoridad para seguridad, trazabilidad, OpenShift, observabilidad y CI/CD;
- mantenga especificación, arquitectura, decisiones, trazabilidad y evidencia dentro de Git;
- use GitHub Copilot Spaces para distribuir conocimiento, no como única fuente de verdad;
- preserve de Spec Kit las piezas que aportan control: Clarify, Analyze, Readiness, trazabilidad, handoff y gates;
- implemente por vertical slices con pruebas y evidencia.

La elección no es entre Spec Kit o Copilot Spaces. La propuesta es:

```text
SDD ligero y versionado
+ baseline depurado de Planificación Sucesoria
+ nueva wiki Ibercaja como autoridad
+ Copilot Spaces para distribuir conocimiento
+ instrucciones, prompts y agentes para ejecutar el proceso
+ gates automáticos para verificarlo
```

---

# 2. Principios de trabajo

## 2.1. Git es la fuente de verdad

Los artefactos que definen el proyecto deben vivir en el repositorio y versionarse junto con el código. Los Spaces facilitan el acceso al conocimiento, pero no sustituyen los documentos, ADR, pruebas ni gates.

## 2.2. Separar cuatro capas

1. **Baseline corporativo de Ibercaja:** seguridad, trazabilidad, OpenShift, observabilidad y CI/CD.
2. **Baseline transversal de modernización:** SDD, equivalencia, gates, evidencia y handoff.
3. **Especificación particular de LeanIX:** P1, P2, P3, contratos, datos, decisiones y riesgos.
4. **Código y validaciones:** .NET, Cosmos DB, Blob Storage, tests, despliegue y pipeline.

## 2.3. No copiar Planificación Sucesoria mecánicamente

Antes de reutilizar nada de PS se debe clasificar como:

- reutilizable sin cambios funcionales relevantes;
- reutilizable tras contrastarlo con la nueva wiki;
- patrón útil que debe volver a justificarse;
- no reutilizable por pertenecer al dominio sucesorio o a decisiones históricas de PS.

## 2.4. La IA guía, pero no certifica

Copilot, sus instrucciones, agentes y Spaces ayudan a extraer, diseñar e implementar. La validez la determinan los contratos, tests, analizadores, pipeline, evidencias y revisiones.

---

# 3. Estructura propuesta del repositorio

```text
leanix-preservation-service/
├── .github/
│   ├── copilot-instructions.md
│   ├── instructions/
│   │   ├── dotnet.instructions.md
│   │   ├── architecture.instructions.md
│   │   ├── security.instructions.md
│   │   ├── testing.instructions.md
│   │   └── documentation.instructions.md
│   ├── prompts/
│   │   ├── extract-requirements.prompt.md
│   │   ├── clarify.prompt.md
│   │   ├── analyze-consistency.prompt.md
│   │   ├── plan-slice.prompt.md
│   │   ├── implement-slice.prompt.md
│   │   └── review-readiness.prompt.md
│   └── agents/
│       ├── specification-reviewer.agent.md
│       ├── ibercaja-architecture-reviewer.agent.md
│       └── leanix-implementation.agent.md
├── docs/
│   ├── baseline/
│   │   ├── BASELINE_SCOPE.md
│   │   ├── ARCHITECTURE_BASELINE.md
│   │   ├── SECURITY_BASELINE.md
│   │   ├── OBSERVABILITY_BASELINE.md
│   │   ├── TESTING_BASELINE.md
│   │   ├── DELIVERY_BASELINE.md
│   │   └── DEFINITION_OF_DONE.md
│   ├── specification/
│   │   ├── SPECIFICATION.md
│   │   ├── ACCEPTANCE_CRITERIA.md
│   │   ├── OUT_OF_SCOPE.md
│   │   ├── OPEN_QUESTIONS.md
│   │   ├── DECISION_LOG.md
│   │   └── TRACEABILITY.md
│   ├── architecture/
│   │   ├── ARCHITECTURE_TARGET.md
│   │   ├── DOMAIN_AND_PROCESSES.md
│   │   ├── DATA_ARCHITECTURE.md
│   │   ├── INTEGRATION_CONTRACTS.md
│   │   └── adr/
│   ├── delivery/
│   │   ├── IMPLEMENTATION_PLAN.md
│   │   ├── TASKS.md
│   │   ├── READINESS.md
│   │   └── HANDOFF.md
│   └── sources/
│       ├── SOURCE_CATALOG.md
│       └── leanix-context.md
├── src/
├── tests/
├── scripts/
├── deploy/
├── Directory.Build.props
├── Directory.Packages.props
├── global.json
├── README.md
└── AGENTS.md
```

Los dos documentos centrales serán:

- `SPECIFICATION.md`: qué debe hacer el microservicio.
- `ARCHITECTURE_TARGET.md`: cómo debe construirse.

No bastan por sí solos. Deben acompañarse, como mínimo, de catálogo de fuentes, fuera de alcance, preguntas abiertas, contratos, criterios de aceptación, decisiones, readiness, trazabilidad y handoff.

---

# 4. Copilot Spaces propuestos

## 4.1. Space corporativo: `Ibercaja Engineering Baseline`

Debe responder: **¿Cómo debe desarrollarse un microservicio para Ibercaja?**

Fuentes previstas:

- wiki de arquitectura y seguridad;
- normas .NET;
- reglas de OpenShift;
- observabilidad corporativa;
- trazabilidad;
- CI/CD;
- ejemplos aprobados;
- baseline transversal depurado de PS.

No debe contener reglas funcionales de LeanIX.

## 4.2. Space específico: `LeanIX Preservation Service`

Debe responder: **¿Qué debe hacer este microservicio y qué decisiones se han tomado?**

Fuentes previstas:

- repositorio del microservicio;
- dossier LeanIX;
- contratos OpenAPI;
- ejemplos reales saneados;
- decisiones confirmadas por Ibercaja;
- ADR, issues, especificación y preguntas abiertas.

## 4.3. Reglas de gobernanza

- No incluir secretos, tokens, credenciales ni datos productivos sin sanear.
- Diferenciar decisiones confirmadas, hipótesis y preguntas abiertas.
- No considerar aprobada una decisión por aparecer en un Space.
- Mantener las decisiones y evidencias importantes versionadas en Git.
- Revisar permisos y visibilidad de todas las fuentes incorporadas.

## 4.4. Jerarquía de fuentes

En caso de contradicción:

1. Decisiones confirmadas por Ibercaja y contratos ejecutables vigentes.
2. Wiki corporativa aplicable.
3. ADR aprobados.
4. Especificación versionada.
5. OpenAPI y respuestas reales saneadas.
6. Código y pruebas de caracterización.
7. Documentos informativos y ejemplos.

Toda contradicción debe registrarse en `DECISION_LOG.md` u `OPEN_QUESTIONS.md`.

---

# 5. Extracción del baseline de Planificación Sucesoria

## 5.1. Potencialmente reutilizable

- configuración base de .NET;
- gestión centralizada de paquetes;
- nullability;
- tratamiento de errores;
- logs estructurados y correlación;
- configuración por entorno;
- Docker;
- análisis estático;
- gestión de secretos;
- estructura de pruebas;
- scripts de validación;
- trazabilidad;
- handoff;
- readiness y gates.

## 5.2. Reutilizable solo tras contrastarlo con la nueva wiki

- autenticación y autorización;
- cabeceras corporativas;
- campos obligatorios de logs;
- imágenes base;
- hardening;
- OpenShift;
- políticas de retención;
- datos sensibles;
- checks de seguridad;
- pipeline corporativo;
- integración con la plataforma de observabilidad.

## 5.3. Patrones que deben volver a justificarse

- Clean Architecture;
- Vertical Slices;
- Result pattern;
- repositories;
- workers y schedulers;
- resiliencia;
- health checks;
- exclusión mutua;
- versionado o migración de datos.

## 5.4. No reutilizable

- dominio sucesorio y fiscal;
- modelo familiar;
- parametría territorial;
- TESTATE/INTESTATE;
- migraciones PostgreSQL de PS;
- componentes Angular de PS;
- tests del motor sucesorio;
- decisiones y excepciones históricas específicas de PS.

## 5.5. Matriz obligatoria de extracción

Para cada elemento de PS registrar:

- elemento;
- ruta u origen;
- categoría de reutilización;
- cambios necesarios;
- nueva fuente de autoridad;
- evidencia de aplicabilidad;
- gate o prueba que lo valida;
- decisión final: copiar, adaptar, reescribir o descartar.

---

# 6. Descomposición inicial del dossier LeanIX

El dossier no debe copiarse entero como `SPECIFICATION.md`, porque mezcla alcance, arquitectura, contratos, riesgos, decisiones y cuestiones abiertas.

## 6.1. `SPECIFICATION.md`

Debe recoger:

- un único microservicio en OpenShift;
- tres responsabilidades lógicas independientes;
- P1: carga inicial y proyección viva de Fact Sheets en Cosmos DB;
- PUSH principal y PULL alternativo;
- borrado lógico y reconciliación;
- P2: exports o snapshots en Blob Storage;
- P3: diagramas XML compatibles con Draw.io, metadatos, versiones y Navigation;
- idempotencia, checkpoints, reanudación y fallos parciales;
- ejecución y resultado independientes por proceso.

## 6.2. `OUT_OF_SCOPE.md`

Debe dejar fuera explícitamente:

- restaurador de LeanIX;
- reconstrucción automática del workspace;
- garantía contractual de reconstrucción;
- histórico funcional completo en Cosmos DB;
- plataforma propia de observabilidad;
- garantía de conservar estados intermedios entre snapshots.

## 6.3. `OPEN_QUESTIONS.md`

Debe contener, entre otras:

- snapshot completo frente a baseline más incrementales;
- tamaños reales, frecuencia y retención;
- renovación de baselines;
- bajas en incrementales;
- marcador exacto de borrado lógico;
- cobertura de Fact Sheets, relaciones y metamodelo;
- cobertura y retención de versiones de diagramas;
- permisos de Diagrams V2 y Navigation;
- cruce de IDs entre servicios;
- modelo definitivo de control de ejecuciones;
- idempotencia, deduplicación y exclusión mutua;
- procedimiento al superar la retención de eventos;
- formato de logs corporativos;
- acceso desde local y OpenShift a LeanIX productivo.

## 6.4. `ARCHITECTURE_TARGET.md`

Decisiones ya conocidas:

- un microservicio .NET;
- despliegue en OpenShift;
- Cosmos DB para la proyección viva de Fact Sheets;
- Blob Storage para snapshots, diagramas y manifiestos;
- PUSH como mecanismo principal y PULL como alternativa;
- control técnico de ejecuciones;
- logs estructurados para la plataforma corporativa;
- sin Prometheus, Grafana ni observabilidad propia.

Decisiones que todavía no deben inventarse:

- versión exacta de .NET;
- estructura final de proyectos;
- particionado de Cosmos DB;
- topología de pods, workers y jobs;
- scheduling;
- identidad técnica y secretos;
- retries, timeouts y circuit breakers;
- health checks;
- concurrencia y exclusión mutua;
- configuración y versionado;
- tratamiento de cambios de metamodelo.

## 6.5. `INTEGRATION_CONTRACTS.md`

Separar contratos para:

- Pathfinder o GraphQL;
- Webhooks API;
- Diagrams V2;
- Navigation;
- OAuth 2.0 client credentials;
- Cosmos DB;
- Blob Storage;
- plataforma corporativa de logs.

## 6.6. `ACCEPTANCE_CRITERIA.md`

### P1

- carga inicial de todos los Fact Sheets visibles;
- proyección consultable en Cosmos DB;
- PUSH para altas, modificaciones y bajas;
- PULL intercambiable sin cambiar la persistencia;
- borrado lógico;
- idempotencia;
- checkpoints y reanudación segura;
- no avanzar el cursor antes de persistir lote y checkpoint.

### P2

- generación y almacenamiento del artefacto esperado;
- fecha, tipo, tamaño, checksum y versión de contrato;
- estrategia full/incremental documentada;
- retención configurable.

### P3

- inventario de diagramas accesibles;
- XML comprobable con Draw.io para una muestra representativa;
- Fact Sheets referenciados;
- metadatos, versiones y organización cuando los permisos lo permitan;
- fallos parciales registrados y reintentables.

---

# 7. SDD reducido propuesto

```text
Context
  ↓
Specify
  ↓
Clarify
  ↓
Architecture
  ↓
Analyze
  ↓
Readiness
  ↓
Plan por vertical slice
  ↓
Implement
  ↓
Verify
  ↓
Handoff
```

Equivalencia con el flujo usado en PS:

- `spec.md` → `SPECIFICATION.md`;
- Clarify → `OPEN_QUESTIONS.md` + `DECISION_LOG.md`;
- checklist → comprobación verificable de completitud;
- `plan.md` → `IMPLEMENTATION_PLAN.md`;
- `tasks.md` → `TASKS.md` o issues;
- Analyze → agente de consistencia;
- `readiness.md` → `READINESS.md`;
- Implement → Copilot Agent por vertical slice;
- handoff → `HANDOFF.md` + `TRACEABILITY.md`.

Se conserva:

- aclaración de dudas;
- consistencia;
- readiness;
- trazabilidad requisito-código-prueba;
- evidencia de fuentes;
- gates de calidad y seguridad;
- handoff final.

Se simplifica:

- repetición documental;
- prompts excesivamente largos;
- burocracia entre fases;
- repetición de reglas corporativas;
- ejecución de suites globales detrás de cada tarea pequeña.

---

# 8. Plan operativo paso a paso

## Paso 1. Crear el repositorio real

Crear el repositorio y añadir solo el esqueleto documental inicial. No generar aún toda la solución .NET.

## Paso 2. Incorporar fuentes saneadas

Añadir a `docs/sources/`:

- dossier LeanIX;
- inventario de OpenAPI;
- ejemplos reales saneados;
- documentos aplicables de Ibercaja;
- inventario del baseline de PS.

No añadir secretos, tokens, credenciales ni datos productivos sin anonimizar.

## Paso 3. Crear `SOURCE_CATALOG.md`

Para cada fuente registrar:

- nombre;
- tipo;
- origen o propietario;
- versión o fecha, si consta;
- alcance;
- nivel de autoridad;
- restricciones de acceso;
- datos sensibles;
- artefactos derivados;
- estado: confirmada, provisional o superada.

## Paso 4. Extraer el baseline de PS

Crear la matriz de clasificación. No copiar archivos antes de decidir si cada elemento se copia, adapta, reescribe o descarta.

## Paso 5. Analizar la nueva wiki de Ibercaja

Para cada norma extraer:

- requisito;
- alcance;
- componentes afectados;
- evidencia exigida;
- control automático posible;
- gate asociado;
- excepciones permitidas;
- responsable, si está definido.

El objetivo no es resumir la wiki. Es convertirla en controles verificables.

## Paso 6. Generar el paquete documental inicial

Orden recomendado:

1. `SOURCE_CATALOG.md`
2. `BASELINE_SCOPE.md`
3. `SPECIFICATION.md`
4. `OUT_OF_SCOPE.md`
5. `OPEN_QUESTIONS.md`
6. `ACCEPTANCE_CRITERIA.md`
7. `ARCHITECTURE_TARGET.md`
8. `INTEGRATION_CONTRACTS.md`
9. `TRACEABILITY.md`
10. `READINESS.md`

No crear el plan detallado ni las tareas de implementación hasta tener suficientemente cerrados alcance, dudas críticas y arquitectura.

## Paso 7. Crear instrucciones, prompts y agentes

Definir:

- instrucciones generales del repositorio;
- instrucciones específicas para arquitectura, seguridad, tests y documentación;
- agente revisor de especificación;
- agente revisor de arquitectura Ibercaja;
- agente de implementación LeanIX;
- prompts cortos para Clarify, Analyze, Readiness y ejecución por vertical slice.

Reglas comunes:

- no inventar requisitos;
- no cerrar preguntas pendientes;
- seguir la jerarquía de fuentes;
- registrar contradicciones;
- relacionar cambios con requisito y prueba;
- no modificar contratos públicos sin decisión aprobada;
- no implementar capacidades fuera de alcance.

## Paso 8. Crear los Copilot Spaces

Crear primero el Space corporativo y después el específico de LeanIX. Hacerlo cuando las fuentes estén saneadas y la jerarquía de autoridad esté documentada.

## Paso 9. Ejecutar readiness documental

Comprobar:

- alcance y fuera de alcance separados;
- P1, P2 y P3 definidos;
- decisiones abiertas identificadas;
- contratos disponibles o registrados como pendientes;
- wiki analizada;
- reglas de PS justificadas o descartadas;
- arquitectura aprobable;
- estrategia de pruebas definida;
- gestión de secretos definida;
- trazabilidad preparada;
- riesgos de acceso a LeanIX productivo controlados.

El resultado de `READINESS.md` debe ser explícito:

- `READY`;
- `NOT_READY`;
- `READY_WITH_CONDITIONS`.

## Paso 10. Generar la solución .NET

Solo después del readiness. La estructura final debe derivarse de `ARCHITECTURE_TARGET.md`, no copiarse mecánicamente de PS.

## Paso 11. Implementar por vertical slices

Orden inicial propuesto, sujeto al plan definitivo:

1. autenticación y cliente LeanIX;
2. control de ejecuciones;
3. carga inicial de Fact Sheets;
4. persistencia en Cosmos DB;
5. webhook PUSH;
6. contingencia PULL;
7. exportaciones y snapshots;
8. diagramas y versiones;
9. Navigation y estructura organizativa;
10. reconciliación;
11. despliegue y operación.

Cada vertical slice debe cerrar:

```text
Contrato
+ implementación
+ persistencia
+ logs
+ resiliencia
+ pruebas
+ trazabilidad
```

## Paso 12. Verificar y cerrar

En checkpoints y al cierre:

- build reproducible;
- tests focalizados;
- pruebas de integración;
- contract tests;
- análisis estático y de seguridad;
- secret scanning;
- validación de imagen y despliegue;
- trazabilidad actualizada;
- handoff actualizado.

---

# 9. Checklist de readiness inicial

## Alcance

- [ ] P1, P2 y P3 están definidos.
- [ ] El fuera de alcance está separado.
- [ ] Existe catálogo de fuentes.
- [ ] Decisiones confirmadas, provisionales y abiertas están diferenciadas.

## Baseline

- [ ] Se ha clasificado el baseline de PS.
- [ ] Cada regla reutilizada tiene fuente de autoridad.
- [ ] No se han copiado reglas de dominio de PS.
- [ ] La nueva wiki de Ibercaja se ha contrastado.

## Arquitectura

- [ ] Versión de .NET decidida.
- [ ] Modelo de procesos y scheduling decidido.
- [ ] Particionado de Cosmos DB decidido.
- [ ] Identidad técnica y secretos definidos.
- [ ] Retries, timeouts y concurrencia definidos.
- [ ] Estrategia PUSH/PULL definida.

## Integraciones

- [ ] Pathfinder/GraphQL catalogado.
- [ ] Webhooks catalogado.
- [ ] Diagrams V2 catalogado.
- [ ] Navigation catalogado.
- [ ] OAuth validado.
- [ ] Ejemplos reales saneados.

## Seguridad y operación

- [ ] No hay secretos en Git ni en Spaces.
- [ ] Formato corporativo de logs definido.
- [ ] Campos de correlación definidos.
- [ ] Modelo de OpenShift confirmado.
- [ ] Cierre seguro y rollback definidos.

## Pruebas y evidencia

- [ ] Cada requisito tiene criterios de aceptación.
- [ ] Cada regla crítica tiene una prueba asociada.
- [ ] Existe plan de pruebas de integración.
- [ ] Existe plan para PUSH y PULL.
- [ ] Existe plan para fallos parciales y reanudación.
- [ ] Existe trazabilidad requisito-código-prueba-evidencia.

## Autorización

- [ ] No quedan dudas funcionales o arquitectónicas bloqueantes.
- [ ] Los riesgos residuales están documentados y aceptados.
- [ ] `READINESS.md` autoriza explícitamente el inicio.

---

# 10. Definición de hecho propuesta

Una vertical slice no está terminada porque Copilot haya generado código. Está terminada cuando:

- cumple la especificación aprobada;
- contratos y configuración están versionados;
- pruebas focalizadas y gates aplicables pasan;
- fallos parciales y reintentos están cubiertos;
- logs y correlación cumplen el estándar;
- no se han introducido secretos;
- trazabilidad y handoff están actualizados;
- no quedan cambios no documentados en contratos públicos o arquitectura.

---

# 11. Riesgos principales y mitigaciones

## Copiar demasiado de PS

**Mitigación:** inventario, matriz de clasificación y contraste con la nueva wiki.

## Convertir el Space en la única verdad

**Mitigación:** documentos versionados, jerarquía de fuentes y gates.

## Implementar decisiones pendientes

**Mitigación:** `OPEN_QUESTIONS.md`, estados explícitos y Readiness.

## Traducir código en lugar de preservar comportamiento

**Mitigación:** especificación neutral, contratos, pruebas de caracterización y validación de efectos observables.

## Inferir seguridad por usar Copilot Enterprise

**Mitigación:** políticas de organización, permisos, control de MCP, saneamiento de fuentes, secret scanning y pipeline.

## Depender solo de instrucciones Markdown

**Mitigación:** convertir reglas críticas en tests, analizadores, checks y pipelines bloqueantes.

---

# 12. Decisiones que deben seguir abiertas

- versión exacta de .NET;
- arquitectura interna definitiva;
- particionado de Cosmos DB;
- modelo de ejecución de P1, P2 y P3;
- planificación independiente;
- retries, timeouts y concurrencia;
- identidad técnica y secretos;
- formato corporativo de logs;
- estrategia full o baseline más incrementales;
- retención de snapshots y versiones;
- cobertura de diagramas de la identidad técnica;
- contrato de observabilidad corporativa;
- reconciliación y recuperación;
- cursor y checkpoints para PULL.

---

# 13. Próximo hito

Construir el inventario del baseline de Planificación Sucesoria y contrastarlo con la nueva wiki de Ibercaja. En paralelo, descomponer el dossier LeanIX en los artefactos iniciales del repositorio.

No generar todavía el código completo del microservicio hasta que:

- el baseline esté depurado;
- la wiki haya sido analizada;
- las dudas bloqueantes estén tratadas;
- `ARCHITECTURE_TARGET.md` sea aprobable;
- `READINESS.md` autorice expresamente el comienzo de la implementación.
