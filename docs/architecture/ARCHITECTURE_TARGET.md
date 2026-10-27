# Arquitectura objetivo inicial

## Decisiones confirmadas

- Un microservicio .NET.
- Despliegue objetivo en OpenShift.
- Cosmos DB para la proyeccion viva P1.
- Blob Storage para snapshots, diagramas y manifiestos.
- Logs estructurados hacia la plataforma corporativa.
- Sin plataforma propia de observabilidad.

## Decisiones todavia abiertas

- version exacta de .NET;
- Aspire, AppHost y ServiceDefaults;
- estructura de proyectos;
- particionado y consistencia de Cosmos DB;
- topologia de pods, workers y jobs;
- scheduling y exclusion mutua;
- identidad tecnica y secretos;
- retries, timeouts y circuit breaker;
- health checks y cierre seguro;
- despliegue independiente de P1, P2 y P3;
- tratamiento de cambios de metamodelo.

## Fronteras previstas

- Host/API para webhook y administracion, si se confirma.
- Application para casos de uso y coordinacion.
- Domain para reglas y estados internos.
- Adaptadores LeanIX, Cosmos DB, Blob Storage y observabilidad en Infrastructure.
