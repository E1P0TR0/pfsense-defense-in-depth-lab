# Seguridad y alcance de publicación

## Contenido permitido

Este repositorio contiene documentación derivada del informe académico y una selección de sus capturas. Se crea inicialmente como **privado**. No incorpora archivos del resto del curso, evaluaciones ni respaldos de Hermes.

## Contenido excluido

- Discos virtuales, instantáneas, imágenes de instalación y memoria de las VM.
- Respaldos completos de pfSense, incluyendo `config.xml`.
- Claves privadas, certificados privados, contraseñas, hashes de autenticación y tokens.
- Capturas de Telnet/HTTP que muestran credenciales en claro o sesiones/cookies.
- Capturas de listas de contraseñas y resultados que exponen una contraseña.
- Códigos universitarios, correos personales y metadatos innecesarios.
- DOCX/PPTX originales: conservados localmente, no incluidos por contener información y capturas que requieren revisión de publicación específica.

La `.gitignore` es una barrera preventiva, no un detector de secretos. Las imágenes seleccionadas se revisaron visualmente y se conservaron únicamente cuando no era necesario incluir credenciales visibles. Esta revisión no equivale a una garantía contra toda información oculta o esteganografía.

## Sobre las capturas

Las direcciones privadas corresponden al laboratorio y se mantienen cuando ayudan a explicar la prueba. Algunos nombres de usuario de sistemas de práctica aparecen como contexto, sin publicar sus credenciales. La selección excluye las capturas con secretos en vez de confiar en ocultar texto dentro de un documento Office.

## Antes de hacer público el proyecto

1. Confirmar autorización del equipo para compartir sus capturas y trabajo académico.
2. Revisar las políticas institucionales de reutilización de materiales.
3. Comprobar nuevamente imágenes, archivos y el historial Git completo.
4. Revisar si nombres y direcciones deben anonimizarse adicionalmente.
5. Verificar que no se incorporó un respaldo operativo de configuración.

No se asigna una licencia de redistribución automáticamente: el trabajo tiene autoría grupal. La privacidad del repositorio reduce exposición, pero no convierte credenciales en contenido seguro para versionar.

## Seguridad del laboratorio

Las configuraciones legacy se describen como parte de un ejercicio aislado. TLS 1.0 y las excepciones SSH registradas no constituyen recomendaciones para servicios productivos. Los sistemas deliberadamente vulnerables no deben exponerse a terceros.

Este repositorio **no autoriza borrar las máquinas ni sustituye un respaldo de su estado**. El alcance solicitado es conservar el informe como miniproyecto documental.
