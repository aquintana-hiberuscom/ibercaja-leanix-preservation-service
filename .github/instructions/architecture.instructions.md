---
applyTo: "docs/architecture/**,src/**"
---
# Instrucciones de arquitectura

- `docs/architecture/ARCHITECTURE_TARGET.md` gobierna las decisiones del servicio consumidor.
- Distinguir decisiones confirmadas, provisionales y pendientes.
- Mantener endpoints delgados y las integraciones externas tras puertos o adaptadores.
- Versionar contratos y registrar cambios incompatibles.
- No introducir dependencia de transporte, persistencia o proveedor en el dominio sin decision explicita.
- Registrar decisiones materiales en ADR y actualizar trazabilidad.
