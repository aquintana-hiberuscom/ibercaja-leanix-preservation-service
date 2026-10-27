---
applyTo: "tests/**,src/**/*.Tests/**,docs/testing/**"
---
# Instrucciones de testing

- Elegir la prueba mas barata que demuestre el comportamiento.
- Usar contract tests para contratos externos y publicados.
- Probar persistencia, serializacion, idempotencia, concurrencia y reintentos con tecnologia real, emulador aprobado o doble contractual.
- No duplicar el mismo comportamiento en varios niveles sin una razon de riesgo.
- Registrar comandos ejecutados, resultado y limitaciones.
- No afirmar que una validacion paso si no se ejecuto.
