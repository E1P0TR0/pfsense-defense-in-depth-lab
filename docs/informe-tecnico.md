# Informe técnico

## Defensa en profundidad con pfSense en un laboratorio virtualizado

[Inicio](../README.md) · [Arquitectura](arquitectura.md) · [Evidencias](evidencias.md) · [Riesgo residual](riesgo-residual.md)

## 1. Resumen

El proyecto estudió dos problemas relacionados: la conectividad de un dispositivo no autorizado y la exposición de credenciales en comunicaciones sin cifrado. Se construyó un entorno con pfSense, una red de gestión y una red corporativa, y se compararon condiciones anteriores y posteriores a la aplicación de controles.

La estrategia combinó protección del gateway, filtrado de servicios, autenticación SSH por clave, cifrado web y controles locales de autenticación. La selección de capturas permite revisar parte de los cambios y sus resultados; las afirmaciones del informe original que no tienen evidencia suficiente se mantienen como **declaradas**, no como verificadas.

Esta edición se basa en el informe corregido aportado por el usuario, que sustituye la fuente seleccionada para la primera publicación. Es una adaptación profesional del trabajo académico, no un acta de ejecución nueva. No se iniciaron máquinas, escáneres ni servicios al elaborarlo.

## 2. Objetivos y alcance

### Objetivo general

Documentar cómo controles complementarios pueden reducir riesgos de acceso no autorizado e interceptación en una red virtual, identificando qué protege cada medida y qué permanece fuera de su alcance.

### Objetivos específicos

- Separar gestión y servicios mediante segmentos conectados a través de pfSense.
- Comparar el acceso de un cliente registrado y uno no registrado.
- Revisar cambios de las tablas ARP en el escenario de prueba.
- Restringir Telnet y el acceso SSH según origen.
- Comparar el transporte de información antes y después de incorporar cifrado.
- Documentar autenticación por clave y respuesta local a intentos fallidos.
- Mantener trazabilidad de evidencias, limitaciones y riesgo residual.

### Fuera del alcance

No se evaluaron todos los servicios, no se corrigieron las vulnerabilidades de Metasploitable2/DVWA y no se implementaron controles de switch administrable. Windows, RDP y eventos de auditoría Windows no se incluyen como componentes ni controles del desarrollo corregido. La documentación no demuestra una política completa de filtrado saliente ni una validación exhaustiva de todas las reglas.

## 3. Entorno

La arquitectura declarada utiliza VMware Workstation Pro. pfSense conecta WAN por NAT, LAN–Gestión en VMnet2 (`192.168.20.0/24`) y CORPORATIVA en VMnet3 (`192.168.30.0/24`). Kali representa las estaciones de administración/auditoría y de prueba no autorizada; Metasploitable2 es el servidor corporativo descrito en el cuerpo del informe y en el diagrama corregido.

El informe identifica pfSense como 2.9.0. Se registra esa versión como declarada por el trabajo, sin contrastarla contra una instancia activa. El nuevo diagrama usa `192.168.20.50` para la estación administrativa, `192.168.20.3` para la no autorizada y `192.168.30.50` para el servidor. La tabla del anexo 2 aún usa `.2` y conserva una fila Windows; varias capturas usan `.100` y `.30`. La [documentación de arquitectura](arquitectura.md) distingue esos niveles de evidencia sin inventar un estado final.

## 4. Escenario de riesgo

### 4.1. Dispositivo no autorizado

La línea base descrita permitía que un cliente adicional obtuviera conectividad y alcanzara el gateway y servicios corporativos. En un segmento compartido, las asociaciones IP/MAC y la interacción ARP constituyen un punto relevante para el análisis de suplantación.

La mera existencia de un mapeo DHCP no autentica la identidad de un dispositivo ni impide por sí sola configurar una dirección manual. Por ello, el laboratorio añadió restricciones para clientes conocidos y Static ARP, además de pruebas de conectividad.

### 4.2. Interceptación de tráfico

El informe documenta la utilización de un equipo intermediario en la LAN y la captura de comunicaciones Telnet/HTTP en Wireshark. Se declara que las credenciales resultaban legibles. Las capturas con credenciales se excluyen de este repositorio; la afirmación se atribuye al informe original, no a una nueva demostración publicada con secretos.

### 4.3. Impacto CIA

- **Confidencialidad:** exposición de información de autenticación y de aplicación.
- **Integridad:** posibilidad de manipulación de comunicaciones en un escenario intermediario; no se aporta una validación completa de todas las formas de alteración.
- **Disponibilidad:** interrupción de comunicaciones cuando el intermediario no reenvía los paquetes, además de restricciones o fallos de conectividad que requieren diagnóstico.

Distinguir posibilidad de impacto y resultado observado evita presentar todos los riesgos como ataques efectivamente demostrados.

## 5. Controles documentados

### C-01. Clientes conocidos y Static ARP

El trabajo registra mapeos estáticos IP/MAC y la opción de Static ARP en pfSense. Se busca limitar la comunicación de equipos no registrados con el gateway y mantener asociaciones definidas en su tabla ARP.

Las capturas de configuración y tablas permiten revisar el cambio. La captura de Static ARP muestra **dos mapeos** (`.100` y `.101`), aunque la narrativa del informe afirma en una fase que se registró únicamente la estación administrativa. No se atribuye esa imagen a un estado con un solo cliente permitido. Un cliente de prueba presenta pérdida de conectividad posterior; una de sus capturas muestra `Network is unreachable`, que por sí sola no identifica un descarte de firewall: también requiere revisar dirección, ruta y estado de interfaz. El resultado no se interpreta como prueba universal de aislamiento de toda la LAN.

La documentación de Netgate distingue restricciones DHCP, asociaciones estáticas y Static ARP global; además, señala que proteger la comunicación con el firewall no evita comunicación entre hosts del mismo segmento. El backend DHCP y la versión concreta deben contrastarse al reconstruir. Véase [referencias](fuentes.md).

### C-02. Filtrado de Telnet y SSH

Se documenta una regla para denegar Telnet hacia el objetivo corporativo y un filtro para limitar SSH a la estación autorizada. El repositorio conserva la regla, un intento SSH rechazado desde un origen no permitido y registros de bloqueo. **El log de Telnet muestra bloqueos desde `.30` y una entrada permitida desde `.100` al puerto 23:** acredita filtrado diferenciado por origen en esa captura, no la eliminación global de Telnet. No se ha determinado si esa excepción se retiró después.

La regla por IP limita un flujo; no establece identidad criptográfica del usuario. Si la estación permitida queda comprometida, la comunicación aún puede llegar al servicio. Por ello, el escenario añade autenticación por clave y un control local.

La conexión rechazada es un resultado del caso documentado. Un escaneo o prueba de un puerto no certifica que toda la política de red funcione correctamente.

### C-03. SSH por clave y ajustes del servicio

La evidencia muestra una clave explícita al iniciar SSH y una sesión exitosa. También registra parámetros como impedir ingreso directo de root, habilitar claves y deshabilitar autenticación por contraseña. Una captura de configuración no reemplaza todas las pruebas negativas necesarias.

El servidor antiguo exigió compatibilidad adicional con `ssh-rsa`; el informe menciona incompatibilidad con Ed25519. Se presenta como limitación de ese objetivo vulnerable. No se transforma en una recomendación para habilitar algoritmos legacy de forma global ni se afirma seguridad criptográfica moderna por haber usado una clave RSA.

La captura de un flujo SSH muestra banner y negociación visibles, seguidos de datos no legibles como el texto de Telnet. El cifrado protege la carga de la sesión; no oculta necesariamente direcciones, puertos, tamaños o todos los metadatos.

### C-04. HTTPS en el entorno legacy

El trabajo habilitó acceso web cifrado y conservó una captura con negociación TLS y datos de aplicación no legibles en texto claro. La imagen y el informe identifican TLS 1.0 como limitación de compatibilidad.

Esto permite comparar HTTP con un canal cifrado en el ejercicio; no demuestra que la aplicación sea segura ni que el certificado haya sido validado correctamente. RFC 8996 depreca TLS 1.0/1.1: el escenario se conserva como histórico y aislado, no como configuración productiva.

### C-05. fail2ban

Se documenta un jail para SSH con tres reintentos y un bloqueo de 30 segundos para acelerar la prueba. Los registros muestran una acción de bloqueo del origen tras fallos de autenticación. La captura seleccionada no demuestra una defensa universal frente a ataques distribuidos o credenciales válidas.

Las notas complementarias mencionan otros tiempos y nombres de jail. Se toma como referencia de la prueba el valor mostrado en la captura, sin imponerlo como una política adecuada para cualquier entorno.

### Delimitación del alcance corregido

Se retiran de esta edición los controles de RDP, los eventos 4624/4625 y la auditoría de Windows que se habían trasladado desde el informe anterior. No se convierten en tareas pendientes del proyecto: ya no forman parte de su desarrollo. La fila Windows que aún aparece en el anexo 2 se registra únicamente como una referencia residual del documento fuente.

## 6. Validación y resultados

| Pregunta de prueba | Evidencia disponible | Conclusión acotada |
|---|---|---|
| ¿Había conectividad inicial? | Ping con respuesta del gateway y del objetivo | Sí, para los destinos mostrados |
| ¿Cambió la situación ARP? | Tablas y configuración del informe | Se documenta diferencia; no acredita protección de cada cliente |
| ¿Se restringió el servicio remoto? | Regla, rechazo y registro del firewall | Restricción observable en los intentos mostrados |
| ¿Se logró acceso SSH usando una clave? | Comando y sesión exitosa | Acceso observado; no verifica todos los métodos alternativos |
| ¿La captura seguía mostrando credenciales legibles tras la migración? | Flujos SSH/TLS seleccionados | La carga mostrada no es legible como las comunicaciones iniciales descritas |
| ¿Actuó fail2ban? | Parámetros y log con bloqueo | Acción de bloqueo documentada en el escenario |

La selección se detalla en [Evidencias](evidencias.md). Se excluyen capturas con contraseñas, cookies o resultados sensibles y no se publican archivos de captura completos.

## 7. Riesgo residual

Permanecen el envenenamiento ARP entre clientes del mismo segmento, la suplantación de una MAC autorizada, el escenario de denegación de servicio sin reenvío y vulnerabilidades de los servicios objetivos. El filtrado no convierte la estación autorizada en confiable permanentemente.

La compatibilidad criptográfica legacy, la falta de una exportación operativa completa y las diferencias de direccionamiento impiden presentar el proyecto como una implementación reproducible sin revisión adicional. Las mejoras propuestas se separan de lo realizado en [Riesgo residual](riesgo-residual.md).

## 8. Conclusiones

1. Las medidas actuaron en capas diferentes: el gateway restringe comunicaciones, el transporte protege contenido y el servicio controla autenticación.
2. Static ARP en pfSense no debe presentarse como una solución integral contra ARP spoofing en toda la LAN.
3. El acceso SSH por clave y el cambio de protocolos reducen exposición en el caso evaluado, sin corregir las aplicaciones deliberadamente vulnerables.
4. fail2ban aporta una reacción documentada a intentos fallidos, no garantía ante cualquier abuso de credenciales.
5. El alcance técnico vigente es pfSense y Linux. La fila residual de Windows del anexo y las diferencias de IP no se convierten en controles o pruebas realizados.
6. El resultado más valioso es la trazabilidad: problema, control, prueba, limitación y siguiente paso. Una configuración funcional no equivale a seguridad absoluta.
