---
applyTo: "src/**,tests/**,deploy/**,docs/security/**,.github/workflows/**"
---
# Instrucciones de seguridad

- No escribir secretos, tokens, credenciales, certificados, datos productivos ni payloads sensibles.
- Aplicar minimo privilegio, minimizacion y fail-closed.
- Registrar aplicabilidad, amenazas, controles, pruebas negativas y riesgo residual.
- No presentar SAST, cobertura o tests funcionales como certificacion completa.
- Toda excepcion debe tener alcance, propietario, caducidad, mitigacion y aprobacion.
- Detener el trabajo ante secreto expuesto o decision corporativa imprescindible pendiente.
