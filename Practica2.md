# Configuración de Políticas de Contraseñas con PAM y libpwquality
## Introducción 
En el panorama digital actual, la seguridad de los sistemas informáticos es de vital importancia. Una de las primeras líneas de defensa contra intrusiones es tener políticas de contraseñas robustas y seguras. En este trabajo, exploraremos la implementación de políticas de contraseñas efectivas utilizando el módulo Pluggable Authentication Modules (PAM) junto con la herramienta libpwquality. Estudiaremos cómo establecer reglas y directrices específicas para asegurar que las contraseñas cumplan con estándares de seguridad rigurosos.

## Desarrollo
**Instalación de Herramientas**
Antes de comenzar, asegurémonos de tener las herramientas necesarias. Instalaremos el paquete ***libpwquality-tools***:
```shell
$ sudo apt install libpwquality-tools
```
Para verificar que se ha instalado correctamente, utilizamos el comando ***pwscore*** para calificar una contraseña:
```shell
$ pwscore
mi_contraseña # Contraseña a probar
50 # Puntuación de la contraseña
```
**Configuración de Políticas**
Editaremos el archivo /etc/security/pwquality.conf para establecer las políticas de contraseñas. Algunas configuraciones importantes incluyen:

    minlen: Longitud mínima aceptable para la nueva contraseña.
    dcredit, ucredit, lcredit: Créditos máximos por tener dígitos, letras mayúsculas y minúsculas en la nueva contraseña, respectivamente.
    gecoscheck: Verifica si las palabras individuales del campo de comentarios del usuario están contenidas en la nueva contraseña.
    dictpath, badwords: Configuraciones relacionadas con diccionarios y palabras prohibidas.

Por ejemplo, configuraremos una longitud mínima de 12 caracteres y créditos específicos para dígitos, letras mayúsculas y minúsculas:
```shell
$ sudo vim /etc/security/pwquality.conf
minlen = 12
dcredit = 3 # Crédito máximo por dígito en la nueva contraseña
<esc> :wq # Guardar la configuración
```
**Verificación de Políticas**
Verificaremos la aplicación de las directivas utilizando ***pwscore*** Si una contraseña no cumple con las políticas establecidas, se mostrará un mensaje de error:
```shell
$ pwscore
hola
Falló la comprobación de calidad de la contraseña:
La contraseña tiene menos de 12 caracteres
```
**Ajuste y Comprobación**
Podemos ajustar los parámetros y comprobar la complejidad de las contraseñas. Por ejemplo, al establecer créditos negativos, requerimos un número mínimo de caracteres para ciertas categorías:
```shell
$ sudo vim /etc/security/pwquality.conf
dcredit = -2
ucredit = -3
lcredit = -1
```
Ahora, al probar contraseñas, se aplicarán las nuevas políticas:
```shell
$ pwscore
hola
Falló la comprobación de calidad de la contraseña:
La contraseña contiene menos de 2 dígitos
$ pwscore
hola12
Falló la comprobación de calidad de la contraseña:
La contraseña contiene menos de 3 letras en mayúscula
$ pwscore
HOL123
Falló la comprobación de calidad de la contraseña:
La contraseña contiene menos de 1 letra en minúscula
$ pwscore
HOLAa12334278
31
```

**Conclusión** 

Establecer políticas de contraseñas sólidas y adaptadas a las necesidades específicas del sistema es esencial para la seguridad cibernética. Con PAM y libpwquality, podemos configurar reglas detalladas y verificar la complejidad de las contraseñas, fortaleciendo así las defensas contra posibles ataques. Estas prácticas son cruciales en cualquier entorno digital para proteger la integridad y confidencialidad de la información.