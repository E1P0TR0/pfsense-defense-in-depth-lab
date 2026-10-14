# Procedencia y referencias

[Inicio](../README.md)

## 1. Fuente del trabajo

**Documento principal:** `UPC-PRE-202620-1ACB0025-7615-INFORME-TB1-Grupo2.docx`, conservado localmente en la carpeta de informes del TB1. Se utilizó su texto completo y se revisaron sus 43 imágenes en hojas de contacto, con inspección ampliada de evidencias seleccionadas.

**Fuente complementaria:** `PFSENSE INSTALLATION.txt`, apuntes de instalación y pruebas. Se consultó para identificar contexto y discrepancias; no se publica porque contiene anotaciones operativas y material sensible. No se copia su contenido como un procedimiento validado.

**Otra versión local:** una segunda copia del informe fue comparada por estructura y huella de texto. Es diferente y no se mezcló silenciosamente con el principal. Los originales, la presentación y la guía de actividad permanecen en sus carpetas; no se publican automáticamente.

Las huellas de las fuentes y la correspondencia de imágenes se registran en `metadata/`. No se incluyen rutas absolutas del equipo ni códigos universitarios.

## 2. Criterios de edición

- Se reorganizó el contenido como miniproyecto técnico en español, sin alterar los originales.
- Se conservaron diferencias de direccionamiento y versiones como datos declarados, no como un inventario final verificado.
- Se distinguieron configuración, observación y conclusión. Un resultado parcial no se convierte en garantía general.
- Se limitaron las afirmaciones de Static ARP al gateway y a las pruebas aportadas.
- Se marcaron Windows/RDP/auditoría como pendientes de evidencia específica.
- Se mantuvieron las limitaciones SSH/TLS legacy y fail2ban de prueba.
- Se retiraron datos personales innecesarios y se omitieron capturas con credenciales.
- No se ejecutaron las pruebas de nuevo al elaborar el repositorio.

## 3. Referencias técnicas contrastadas para las precisiones editoriales

1. **Netgate — DHCPv4 Server:** diferencia clientes conocidos, mapeos estáticos y Static ARP; detalla su alcance hacia el firewall y las limitaciones del segmento local. [Documento oficial](https://docs.netgate.com/pfsense/en/latest/services/dhcp/ipv4.html).
2. **IETF / RFC Editor — RFC 8996:** deprecación de TLS 1.0 y TLS 1.1. [Texto normativo](https://www.rfc-editor.org/rfc/rfc8996.html).
3. **Microsoft — Audit Logon:** propósito de la auditoría de sesiones y eventos 4624/4625. [Documento oficial](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/audit-logon).

Se contrastaron los pasajes pertinentes recuperados de estas fuentes. No se declara haber auditado toda su documentación ni el cumplimiento de un sistema real.

## 4. Bibliografía del informe original

El documento original también cita CIS Controls v8, NIST SP 800-30, SP 800-41, SP 800-115, OpenSSH, fail2ban, Wireshark y RFC 1918/4251. Se conserva esta información como bibliografía del trabajo: no se afirma haber consultado íntegramente esos documentos al preparar esta edición.

## 5. Informe frente a respaldo operativo

Esta publicación está deliberadamente limitada a documentación y evidencias. No incluye exportaciones de configuración, claves ni discos. Los respaldos operativos deben conservarse y verificarse por separado.

Si se desea preservar una configuración restaurable, debe gestionarse como un respaldo independiente con revisión de secretos. No dar por completo ese respaldo a partir de este repo.
