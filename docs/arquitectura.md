# Arquitectura y direccionamiento

[Inicio](../README.md) · [Informe](informe-tecnico.md) · [Evidencias](evidencias.md)

## 1. Zonas de confianza

El informe describe VMware Workstation Pro con tres interfaces en pfSense:

| Interfaz pfSense | Red virtual | Segmento | Función |
|---|---|---|---|
| `em0` / WAN | NAT de VMware | Dirección obtenida por DHCP | Conectividad externa simulada |
| `em1` / LAN | VMnet2 | `192.168.20.0/24` | Gestión y auditoría |
| `em2` / OPT1–CORPORATIVA | VMnet3 | `192.168.30.0/24` | Servicios de laboratorio |

Para VMnet2 y VMnet3 se documenta la desactivación del adaptador virtual del anfitrión. El objetivo es que el tráfico entre las zonas siga el camino definido a través del firewall, sin introducir al host como otro participante de esas redes.

La separación de redes crea el punto de control; su eficacia depende además de las reglas, interfaces y rutas efectivas. Dos clientes de la misma subred pueden comunicarse sin pasar por pfSense.

## 2. Inventario declarado frente a pruebas

| Activo | Inventario y diagrama inicial | Dirección visible en pruebas | Observación |
|---|---|---|---|
| Gateway LAN | `192.168.20.1/24` | `192.168.20.1` | Coincide |
| Gateway corporativo | `192.168.30.1/24` | `192.168.30.1` | Coincide |
| Kali administrativa | `192.168.20.2/24` | `192.168.20.100` | Hay variación entre diseño y evidencias |
| Metasploitable2 | `192.168.30.2/24` | `192.168.30.100` | Hay variación entre diseño y evidencias |
| Windows 10 Pro | `192.168.30.3/24` | No validado en las capturas seleccionadas | Mantener como activo declarado |
| Cliente no registrado | No fijado en el inventario inicial | `192.168.20.30` en un intento de asignación manual | Dirección de una prueba, no servidor permanente |

Además, la narrativa original menciona una única MAC autorizada en una fase, mientras la captura de Static ARP muestra mapeos `.100` y `.101`. No se determina si corresponden a fases distintas ni se identifica `.101` como atacante.

No se determina si las variaciones corresponden a un cambio de fase, DHCP o una modificación del laboratorio. Se conservan ambas referencias para evitar una falsa reconstrucción de su estado final. Antes de reproducir, confirmar los hosts, leases, mapeos y reglas activos.

## 3. Diagrama original

![Arquitectura declarada en el informe](../assets/evidencias/arquitectura-original.png)

El gráfico identifica pfSense como versión **2.9.0**, dato que también declara el informe. No se contrastó esa versión con una instancia en ejecución ni con un inventario exportado. Se registra como versión declarada, no como versión verificada por este repositorio.

Los símbolos de switch representan conectividad dentro del diagrama: no son evidencia de que existiera un switch administrable con DAI o Port Security. El trabajo indica que no dispuso de ese componente.

## 4. Flujos evaluados

- Cliente LAN → gateway y servidor corporativo: conectividad inicial y fallo posterior observado; la captura no determina por sí sola su causa.
- LAN → servidor Telnet: conexión en texto claro descrita en el estado inicial y filtrado posterior por origen. El log conserva bloqueo desde `.30` y permiso desde `.100`; no acredita eliminación total del servicio.
- Estación administrativa → servidor SSH: acceso por clave y filtro por origen.
- Cliente → aplicación web: comparación HTTP/HTTPS mediante captura.
- Origen permitido → SSH: intentos fallidos y reacción de fail2ban.

La documentación no incorpora una exportación completa de reglas; no permite afirmar que se evaluaron todos los puertos, protocolos o caminos de red.

## 5. Condiciones para reconstrucción

Esta sección es una lista propuesta, no una declaración de pruebas nuevas:

1. Crear las redes virtuales y confirmar que no unen el laboratorio a la LAN física.
2. Identificar claramente WAN, LAN y OPT1 y sus gateways.
3. Resolver las diferencias `.2` / `.100` antes de configurar reglas por IP.
4. Documentar versión exacta de pfSense, backend DHCP y sistemas invitados.
5. Revisar reglas automáticas y explícitas, su orden y alcance.
6. Mantener los sistemas vulnerables sin publicación de puertos hacia redes no confiables.
7. Registrar una nueva línea base y pruebas posteriores si se vuelve a implementar.

El informe no es un respaldo restaurable. No contiene imágenes de VM ni `config.xml` completo.
