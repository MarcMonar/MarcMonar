# Generación de Certificado Autofirmado para Página Web Apache

## Introducción

En el entorno de Internet, la seguridad es esencial para proteger los datos de usuarios. Para asegurar las comunicaciones, se implementa el cifrado TLS (Transport Layer Security) mediante certificados. En esta práctica, se abordará la generación y autofirma de un certificado en un servidor web Apache.

### Desarrollo

1. **Instalación de Apache:**

   Comenzamos instalando Apache para dar inicio a la práctica:

   ```bash
   apt install apache2
   ```

2. **Generar Certificado:**

   Creamos una clave y un Certificado de Solicitud de Firma (CSR):

   ```bash
   openssl genrsa -out apache2.key.pem 2048
   openssl req -new -key apache2.key.pem -out solicitud.csr
   ```

   Durante la creación del CSR, ingresamos la información requerida.

3. **Autofirma del Certificado:**

   Autofirmamos el certificado para su uso:

   ```bash
   openssl x509 -req -days 365 -in solicitud.csr -signkey apache2.key.pem -out certificadoapache2.crt
   ```

4. **Configuración de Apache:**

   Movemos y ajustamos los permisos de los archivos:

   ```bash
   chmod 777 solicitud.csr
   chown root:ssl-cert apache2.key.pem
   chmod 640 apache2.key.pem
   mv solicitud.csr /etc/ssl/certs/
   mv apache2.key.pem /etc/ssl/private
   ```

   Modificamos el archivo de configuración `/etc/apache2/sites-available/default-ssl.conf`:

   ```bash
   vim /etc/apache2/sites-available/default-ssl.conf
   ```

   Editamos las líneas:

   ```apache
   SSLCertificateFile /etc/ssl/certs/certificadoapache.cert # línea nº1 a editar
   SSLCertificateKeyFile /etc/ssl/private/apache2.key.pem # línea nº2 a editar
   ```

   Reiniciamos Apache, activamos los módulos SSL y comprobamos el funcionamiento:

   ```bash
   systemctl restart apache2
   sudo a2enmod ssl
   a2enmod headers
   a2ensite default-ssl
   ```

### Comprobación

Realizamos un test con `curl` para verificar el certificado desde otra máquina cliente:

```bash
curl -k -v https://10.0.0.1
```

- `-k` o `--insecure`: Para certificados SSL autofirmados.
- `-v`: Modo verbose para mostrar detalles.

El resultado mostrará la información del certificado creado, demostrando que la configuración y el certificado autofirmado están en funcionamiento.
