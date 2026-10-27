# Especificacion inicial

## Objetivo

Preservar informacion relevante de SAP LeanIX mediante un unico microservicio .NET desplegable en OpenShift y tres procesos logicos independientes.

## P1: proyeccion viva de Fact Sheets

- Ejecutar una carga inicial de los Fact Sheets visibles para la identidad tecnica.
- Mantener una proyeccion transformada y consultable en Cosmos DB.
- Procesar altas, modificaciones y bajas mediante PUSH como mecanismo principal.
- Disponer de PULL como alternativa sin cambiar el modelo de persistencia.
- Representar bajas mediante borrado logico.
- Soportar idempotencia, checkpoints, reanudacion y reconciliacion.
- No confirmar un cursor PULL antes de persistir el lote y su checkpoint.

## P2: snapshots o exportaciones

- Obtener y almacenar en Blob Storage el artefacto original disponible.
- Registrar fecha, tipo, tamano, checksum y version de contrato.
- Aplicar una estrategia full o baseline mas incrementales cuando sea decidida.
- Aplicar retencion configurable cuando se confirme la norma aplicable.

## P3: diagramas y estructura organizativa

- Enumerar los diagramas accesibles.
- Preservar XML comprobable con Draw.io cuando la API y permisos lo permitan.
- Mantener metadatos, versiones y referencias a Fact Sheets.
- Preservar informacion Navigation o estructura organizativa disponible.
- Registrar fallos parciales y permitir reintentos.

## Requisitos transversales

- Ejecucion y resultado independientes para P1, P2 y P3.
- OAuth 2.0 client credentials sujeto a provision y permisos confirmados.
- Logs estructurados hacia la plataforma corporativa.
- Sin secretos ni payloads productivos en Git, Spaces o logs.
