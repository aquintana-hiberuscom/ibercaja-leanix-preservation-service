# Ibercaja LeanIX Preservation Service

Microservicio .NET para extraer, proyectar y preservar informacion procedente de SAP LeanIX.

> Estado: preparacion documental. No se ha autorizado todavia la generacion de la solucion .NET.

## Capacidades previstas

- **P1:** carga inicial y proyeccion viva de Fact Sheets en Cosmos DB.
- **P2:** snapshots o exportaciones en Azure Blob Storage.
- **P3:** preservacion de diagramas, XML, metadatos, versiones y estructura Navigation.

## Principios

- PUSH como mecanismo principal y PULL como contingencia, pendientes de cerrar contractual y operativamente.
- Idempotencia, checkpoints, reconciliacion y tratamiento de fallos parciales.
- Git como fuente de verdad; Copilot Spaces como distribucion de contexto.
- Aplicacion provisional de `ibercaja-dotnet-service-baseline` version `0.1.0-provisional`.
- Ninguna decision pendiente se presenta como aprobada.

## Documentacion

- `docs/sources`: fuentes y autoridad.
- `docs/baseline`: baseline aplicado y extraccion de PS.
- `docs/specification`: alcance, criterios, decisiones y trazabilidad.
- `docs/architecture`: arquitectura, datos e integraciones.
- `docs/delivery`: readiness, plan, tareas y handoff.
