# Logs centralizados 
## Introducción
El trabajo del administrador de sistemas puede verse simplificado centralizado los logs (registros del sistema) 
## Desarollo
La practica consta de dos partes 
La primera es tener un serviodor y nos ubicaremos en /etc, para ubicarnos en esta direccion usamos el **cd /etc**, despues cambiado la configuracion de **rsyslog.conf** poniendo lo siguinete .
- >```$template Remotelogs, "/var/log/%HOSTNAME%/RemoteLog.log" *.* ?RemoteLogs & ~```

Para entrar a **rsyslog.conf** lo podemos hacer con cualquir editor, en este caso usamos VIM, para que el **logger** de la maquina cliente nos pueda aparecer debemos desactivar el cortafuegos, para ellos ponemos estos dos comandos en el terminal.   

- >- ufw allow 514/tcp
- >- ufw allow 514/udp

La segunda parte de esta practica lo tiene que hacer la maquina cliente para ello nos colamos en el archivo **rsyslog.conf** de la misma manera que lo hicimos con el servidor, nos ubicamos el /etc que para ello usamos **cd /etc**, y entramos con un editor al archivo **rsyslog.conf**, dentro de este archivo añadiremos una linea, que es la siguiente.
- >  ```*.* @@192.168.82.153:156``` 
