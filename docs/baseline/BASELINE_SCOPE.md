# Alcance del baseline aplicado

## Aplicado provisionalmente

- jerarquia de fuentes;
- gobierno de decisiones y contradicciones;
- endpoints delgados y fronteras de integracion;
- contratos versionados;
- secretos fuera de Git;
- observabilidad estructurada y minimizada;
- testing por riesgo;
- SDD reducido;
- readiness, trazabilidad y handoff;
- prohibicion de commit, push, merge o despliegue sin autorizacion.

## Requiere validacion corporativa

- version y soporte de .NET;
- arquitectura OpenShift;
- identidad tecnica y secretos;
- OAuth, scopes y permisos de LeanIX;
- logs y observabilidad corporativa;
- Cosmos DB y Blob Storage;
- CI/CD, cobertura y gates;
- retencion, recuperacion y operacion.

## Excluido del baseline de PS

Angular, PostgreSQL, EF Core/Npgsql, Service Fabric, contratos funcionales de PS, excepciones historicas y configuracion concreta de infraestructura.
