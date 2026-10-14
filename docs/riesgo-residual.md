# Riesgo residual y mejoras

[Inicio](../README.md) · [Informe](informe-tecnico.md)

## 1. Matriz de seguimiento

Esta matriz es cualitativa. No se asignan puntuaciones ni probabilidades numéricas que el laboratorio no midió.

| Riesgo | Control documentado | Estado del caso evaluado | Pendiente |
|---|---|---|---|
| Equipo no registrado alcanza el gateway | Clientes conocidos y Static ARP | Configuración y fallo posterior observados; atribución causal no concluyente | Confirmar backend, rutas, reglas y resistencia a suplantación MAC |
| Envenenamiento ARP del gateway | Asociaciones estáticas | Cambio y protección reportados | Probar repetidamente y separar efecto ARP de problemas de ruta |
| Envenenamiento entre clientes LAN | Ninguno de switch administrable | No resuelto | Evaluar controles de acceso de capa 2 |
| Lectura de credenciales Telnet/HTTP | Filtrado Telnet por origen y migración SSH/HTTPS | Carga cifrada visible; también permiso Telnet desde `.100` en el log | Aclarar excepción Telnet, inventariar servicios y validar TLS/certificados |
| Acceso SSH desde origen no permitido | Filtro por IP | Rechazo y registros conservados | Probar todos los caminos e interfaces pertinentes |
| Abuso desde estación administrativa comprometida | Claves y fail2ban | Bloqueo tras fallos mostrado | Evaluar credenciales válidas, tasa distribuida y privilegios |
| DoS por intermediario sin reenvío | No mitigado en el trabajo | Riesgo abierto | Probar disponibilidad y mitigaciones L2 |
| Vulnerabilidades en Metasploitable2/DVWA | No corregidas | Riesgo deliberadamente conservado | No exponer; mantener aislamiento del laboratorio |
| Criptografía legacy | Excepciones por compatibilidad | Limitación reconocida | Sustituir objetivos o versiones para una implementación moderna |
| Auditoría y RDP Windows | Descritos en la narrativa | No validados con evidencia específica | Capturar políticas, reglas, pruebas positivas/negativas y eventos |

## 2. Mejoras propuestas, no ejecutadas

### Prioridad A — Calidad de evidencia

- Confirmar la versión de cada componente y registrar una línea base.
- Resolver `.2` frente a `.100` en inventario, reglas y procedimientos.
- Repetir pruebas con direcciones y rutas controladas: `Network is unreachable` no prueba por sí solo un bloqueo de pfSense.
- Conservar salidas y registros con fecha, origen, destino y criterio de éxito, sin secretos.
- Añadir pruebas negativas de root y contraseña para SSH.
- Incorporar la evidencia pendiente de Windows.

### Prioridad B — Límite del segmento local

- Evaluar DHCP Snooping, Dynamic ARP Inspection y controles de acceso del switch, según las capacidades del entorno.
- Probar explícitamente MAC spoofing y comunicación entre clientes.
- Documentar si existe un camino que no atraviesa el firewall.

Estas opciones aparecen como mejoras en el trabajo; no se implementaron ni se verificó que el equipo virtual de conmutación las soporte.

### Prioridad C — Servicios y mantenimiento

- Reemplazar servicios legacy al pasar de demostración académica a una configuración productiva.
- Validar certificado y nombre de destino, no solamente que la carga sea ilegible.
- Evitar perfiles SSH de compatibilidad global; acotar cualquier excepción de laboratorio y retirarla al terminar.
- Revisar reglas permitidas, registros y privilegios, además de actualizar software.

RFC 8996 establece que TLS 1.0 y 1.1 no deben usarse. La evidencia legacy se conserva para explicar el ejercicio, no para recomendar su reutilización.

## 3. Lo que este repositorio no demuestra

- Que todos los paquetes de un intruso se descarten.
- Que una MAC autorizada equivalga a una identidad autenticada.
- Que el cifrado evite todos los ataques MitM sin validación del servidor.
- Que los logs locales sean inalterables.
- Que un bloqueo en red siempre produzca un evento 4625 en Windows.
- Que fail2ban impida ataques desde todas las fuentes o con credenciales válidas.
- Que la configuración sea restaurable sin un respaldo operativo separado.

## 4. Criterio de cierre

El miniproyecto cumple un objetivo documental: explicar decisiones y mostrar evidencia seleccionada con límites. El cierre del laboratorio no equivale a cerrar todos sus riesgos.

La eliminación de VM es una decisión separada. No se ha eliminado ninguna máquina. Si se borran, se pierde su estado salvo que exista otro respaldo; el informe y las capturas no lo reemplazan.
