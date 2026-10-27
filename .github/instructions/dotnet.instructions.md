---
applyTo: "**/*.cs,**/*.csproj,global.json,Directory.*.props"
---
# Instrucciones .NET

- No fijes version de SDK, framework o paquetes sin una decision aprobada.
- Mantener nullability, warnings y analizadores coherentes con el baseline ratificado.
- Centralizar versiones de paquetes cuando el servicio adopte `Directory.Packages.props`.
- Mantener transporte, aplicacion, dominio e infraestructura desacoplados segun `ARCHITECTURE_TARGET.md`.
- No incorporar frameworks o patrones por similitud con otro proyecto; justificar el cambio mediante ADR cuando sea material.
- Ejecutar la prueba focalizada responsable antes de ampliar validaciones.
