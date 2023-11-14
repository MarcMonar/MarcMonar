# Fortalecimiento de Apache2

## Introducción

El fortalecimiento de Apache2 es una práctica esencial para reforzar la seguridad del servidor web Apache, minimizando las vulnerabilidades y mitigando amenazas potenciales. Se busca reducir la superficie de ataque y optimizar la resistencia del servidor ante posibles intentos de explotación, aplicando medidas como la restricción de privilegios, la configuración de cortafuegos y la desactivación de servicios innecesarios. Desde la configuración de SSL/TLS para asegurar la comunicación hasta la aplicación de actualizaciones de seguridad, estas prácticas son cruciales para construir un entorno web robusto y protegido contra amenazas cibernéticas.

En esta guía, se llevarán a cabo acciones específicas basadas en las directrices de fortalecimiento de Apache2 proporcionadas por Tecmint (https://www.tecmint.com/apache-security-tips/).

## Desarrollo

### 1. Eliminar la versión de Apache2

Añadiremos las siguientes líneas al final del archivo `/etc/apache2/apache2.conf`:

```bash
sudo vim /etc/apache2/apache2.conf
```

Agregamos:

```apache
ServerTokens Prod
ServerSignature Off
```

### 2. Eliminación de indexación

Modificaremos el archivo `apache2.conf` para eliminar la indexación de Apache2 y evitar la visualización de archivos ocultos:

```bash
sudo vim /etc/apache2/apache2.conf
```

Agregamos las siguientes líneas:

```apache
<Directory /var/www/html/>
    Options -Indexes 
</Directory>
```

Luego reiniciamos Apache:

```bash
systemctl restart apache2
```

### 4. Añadir HTTPS

Para habilitar HTTPS, se seguirá la práctica de crear un certificado autofirmado para Apache. Este procedimiento se detalla en la sección 5.

### 5. Habilitar HSTS estrictamente

Habilitamos el encabezado HSTS (Strict-Transport-Security) para mejorar la seguridad:

```bash
sudo a2enmod headers
sudo systemctl restart apache2
sudo vim /etc/apache2/sites-available/default_ssl.conf
```

Agregamos la siguiente línea:

```apache
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
```

### 6. Habilitar HTTP/2 en Apache

Habilitamos el soporte para HTTP/2 en Apache para obtener mejoras en rendimiento y seguridad:

```bash
sudo a2enmod http2
systemctl restart apache2
```

### 8. Deshabilitar la directiva de la firma del servidor en Apache

Editamos `/etc/apache2/apache2.conf` para desactivar la directiva `ServerSignature`:

```bash
sudo vim /etc/apache2/apache2.conf
```

Eliminamos o comentamos la línea:

```apache
# ServerSignature On
```

### 9. ServerTokens Directiva

Desactivamos la directiva `ServerTokens` para no revelar información sobre la versión de Apache:

```bash
sudo vim /etc/apache2/apache2.conf
```

Agregamos:

```apache
ServerTokens Prod
```

### 11. Deshabilitar módulos innecesarios

Desactivamos el módulo de autenticación básica, ya que no se utilizará:

```bash
sudo apache2ctl -M | grep auth
sudo a2dismod auth_basic -f
sudo systemctl restart apache2
```

### 14. Limitar el peso de los archivos al subirlos

Establecemos un límite en el tamaño de los archivos que se pueden cargar:

```bash
sudo mkdir -p /var/www/html/uploads
sudo vim /etc/apache2/apache2.conf
```

Agregamos:

```apache
<Directory /var/www/html/uploads>
    LimitRequestBody 8388608
</Directory>
```

### 16. Apache2 diferente usuario y grupo

Para mejorar la seguridad, asignamos a Apache2 un usuario y grupo específico:

```bash
sudo groupadd apachegroup
sudo useradd -g apachegroup apacheuser
sudo chown -R apacheuser:apachegroup /var/www/html
sudo vim /etc/apache2/apache2.conf
```

Agregamos:

```apache
User apacheuser
Group apachegroup
```

### 16. Anti DDos

Instalamos los módulos de seguridad ModSecurity y Mod_evasive para proteger contra ataques DDoS:

```bash
sudo apt-get install libapache2-mod-security2 -y
sudo apt-get install libapache2-mod-evasive -y
```

## Comprobación

### 1, 8 y 9

Antes:

```bash
curl http://localhost
```

Después:

```bash
systemctl restart apache2
curl http://localhost/test
```

### 2

Antes:

```bash
curl http://localhost/test/
```

Después:

```bash
curl http://localhost/test/
```

### 6

Antes:

```bash
curl -k -I --http2 https://localhost
```

Después:

```bash
curl -k -I --http2 https://localhost
```

### 14

Probamos la restricción de tamaño al cargar un archivo:

```bash
dd if=/dev/zero of=prueba bs=1M count=9
curl -T prueba http://localhost/uploads/
```

### 16

Verificamos si Apache2 funciona con los nuevos grupos:

```bash
curl -I -k https://localhost
```

Observamos la respuesta 200 OK y la longitud del contenido.
