# Politica de contraseñas 
## Introducción 
La seguridad de nuestro sistema puede verse amenazado. Una buena politica de contraseñas reformaria la seguridad.

El objetico de la practica es entender y configurar las directrices de seguridad, para ello utilizaremos el modulo **pan pw-password.**
## Desarrollo
- Instalación: 
- Configuración: 
  > para la configuración de la politicas de contraseñas debemos modificar un archivo **/etc/security/pwquality.conf**.Dentro de este archivo podemos modificar algunas cosas, que son las siguientes: 
  
    - ***difok:*** Número de caracteres en una nueva contraseña que no deben estar presentes en la contraseña anterior.
    - ***minlen:*** Tamaño mínimo aceptable para la nueva contraseña.
    - ***dcredit:*** Crédito máximo por tener dígitos en la nueva contraseña.
    - ***ucredit:*** Crédito máximo por tener letras mayúsculas en la nueva contraseña.
    - ***lcredit:*** Crédito máximo por tener letras minúsculas en la nueva contraseña.
    - ***ocredit:*** Crédito máximo por tener otros caracteres en la nueva contraseña.
    - ***minclass:*** Número mínimo de clases de caracteres requeridas para la nueva contraseña
    - ***maxrepeat:*** Número máximo de caracteres repetidos.
    - ***maxclassrepeat:*** Número máximo de caracteres consecutivos en la misma clase.
    - ***gecoscheck:*** Verifica si las palabras individuales de más de 3 caracteres del campo passwd GECOS (campo de comentarios) del usuario están contenidas en la nueva contraseña.
    - ***dictpath:*** Ruta a los diccionarios de clacklib.
    - ***badwords:*** Lista de palabras separadas por espacios que no deben incluirse en la contraseña.

## Comprabación 
> 