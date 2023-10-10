# Logs centralizados 
## **Introducción:**
En la era digital, la centralización efectiva de registros (logs) es crucial para la gestión y seguridad de sistemas. Este trabajo explora la implementación práctica del servicio rsyslog, detallando el proceso paso a paso para centralizar logs desde múltiples servidores en uno solo. Se enfoca en mejorar la eficiencia operativa y fortalecer la capacidad de respuesta ante eventos críticos, ofreciendo una visión detallada de la configuración segura y eficaz del sistema de centralización de logs.

## Desarollo
**Configuración del Servidor:**    

Primero, es necesario establecer reglas de firewall para permitir la llegada de los logs al servidor. Se abrirán los puertos 6689 tanto para TCP como para UDP:
```shell
$sudo ufw allow 6689/tcp    
$sudo ufw allow 6689/udp
```
A continuación, se editará el archivo /etc/rsyslog.conf para habilitar la recepción de logs a través de TCP y UDP. Se cargan los módulos imudp e imtcp y se configuran los puertos:
```shell
module(load="imudp")          
input(type="imudp" port="6689")            
module(load="imtcp")                
input(type="imtcp" port="6689")
```

Luego, se creará una plantilla para redirigir los logs entrantes a carpetas específicas con el formato ***/<nombre_del_host>/<nombre_del_programa>.log:***

```shell
$template RemoteLogs,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log"
*.* ?RemoteLogs
& ~
```
Finalmente, se reinicia el servicio rsyslog para aplicar los cambios:
```shell
$systemctl restart rsyslog.service
```
**Configuración del Cliente:**

En el lado del cliente, se editará el archivo /etc/rsyslog.d/50-default.conf para enviar los logs al servidor central en el puerto 6689 a través del protocolo TCP:
```shell
$sudo nano /etc/rsyslog.d/50-default.conf   
*.* @@IP_DEL_SERVIDOR:6689
```
Luego, se reinicia el servicio rsyslog en el cliente para que los cambios surtan efecto:
```shell
$systemctl restart rsyslog.service
```
**Finalización**

Para verificar la conexión y confirmar que los logs del cliente están llegando al servidor central, se utiliza el comando logger para crear un log de prueba en el cliente:
```shell
$logger "mimensaje"
```
En el servidor, se verifica si el log ha sido recibido y guardado en la carpeta correspondiente del siguiente modo:
```shell
$ cat /var/log/<nombre_del_host>/<nombre_del_programa>.log |grep      mimensaje
<timestamp> <nombre_del_host> <nombre_del_programa>: mimensaje
```
Este proceso demuestra la efectividad del sistema de centralización de logs implementado.
