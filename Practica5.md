# Transmisiones Web Seguras (TLS) con Apache

## Introducción

Internet es un canal no seguro, donde cualquier persona podría obtener nuestros datos. Para evitar esto, es crucial cifrar ciertas comunicaciones con información confidencial. En este caso, generaremos un certificado autofirmado para un servidor web Apache, asegurando transmisiones web seguras mediante el uso de TLS.

## Instalación de Apache

Instalamos Apache y actualizamos el sistema:

```bash
sudo apt install apache2
sudo apt update
```

## Crear Certificado de Autoridad (CA)

### Paso 1

Instalamos el conjunto de secuencias de comandos easy-rsa:

```bash
sudo apt install easy-rsa
```

### Paso 2

Preparamos un directorio para la infraestructura de clave pública:

```bash
sudo mkdir ~/easy-rsa
```

### Paso 3

Creamos enlaces simbólicos que apunten a los archivos del paquete easy-rsa:

```bash
sudo ln -s /usr/share/easy-rsa/* ~/easy-rsa/
```

### Paso 4

Restringimos el acceso para que solo el propietario pueda acceder a él:

```bash
sudo chmod 700 /home/your_username/easy-rsa
```

### Paso 5

Iniciamos el PKI dentro del directorio easy-rsa:

```bash
cd ~/easy-rsa
./easyrsa init-pki
```

### Paso 6

Creamos y editamos el archivo `vars` con los datos de la organización:

```bash
sudo nano ~/easy-rsa/vars
```

```plaintext
set_var EASYRSA_REQ_COUNTRY    "España"
set_var EASYRSA_REQ_PROVINCE   "Valencia"
set_var EASYRSA_REQ_CITY       "Valencia"
set_var EASYRSA_REQ_ORG        "ASIR2SGD"
set_var EASYRSA_REQ_EMAIL      "bartormon@alu.edu.gva.es"
set_var EASYRSA_REQ_OU         "Community"
set_var EASYRSA_ALGO           "ec"
set_var EASYRSA_DIGEST         "sha512"
```

## Generar Certificado

### Paso 1

Creamos el certificado root público y el par de claves privadas para su entidad de certificación:

```bash
./easyrsa build-ca
```

### Paso 2

Copiamos todo el contenido del archivo `~/easy-rsa/pki/ca.crt`:

```bash
cat ~/easy-rsa/pki/ca.crt
```

### Paso 3

Pegamos el certificado en el archivo `/tmp/ca.crt`:

```bash
nano /tmp/ca.crt
```

## Crear CSR - Peticion

### Paso 1

Aseguramos que tenemos instalado el servicio openssl en el sistema:

```bash
sudo apt update
sudo apt install openssl
```

### Paso 2

Creamos un directorio y generamos dentro una clave privada:

```bash
sudo mkdir ~/practice-csr
sudo cd ~/practice-csr
sudo openssl genrsa -out barbara-server.key
```

### Paso 3

Creamos la petición:

```bash
openssl req -new -key barbara-server.key -out barbara-server.req
```

## Firmado por CA - Firmar Petición

### Paso 1

Nos movemos a la carpeta del CA e importamos la CSR:

```bash
cd ~/easy-rsa
./easyrsa import-req barbara-server.req barbara-server
```

### Paso 2

Firmamos la CSR:

```bash
./easyrsa sign-req server barbara-server
```

## Configurar Apache

### Paso 1

Modificamos el archivo `/etc/apache2/sites-available/default-ssl.conf`:

```bash
sudo nano /etc/apache2/sites-available/default-ssl.conf
SSLCertificateFile      /etc/ssl/certs/barbara-server.crt
SSLCertificateKeyFile   /etc/ssl/private/barbara-server.key
```

### Paso 2

Movemos el certificado y la clave privada al directorio especificado:

```bash
cd ~/practice-csr
sudo cp barbara-server.crt /etc/ssl/certs/
sudo cp barbara-server.key /etc/ssl/private/
```

### Paso 3

Habilitamos el sitio y el módulo de SSL:

```bash
sudo a2ensite default-ssl.conf
sudo a2enmod ssl
```

### Paso 4

Reiniciamos Apache:

```bash
systemctl restart apache2
```

### Paso 5

Accedemos al navegador, buscamos nuestro sitio nuevo con HTTPS y debería aparecer el mensaje de seguridad.

## Importar el Certificado

Vamos a los ajustes del navegador, buscamos el apartado de certificados y pulsamos el botón ‘Ver certificados’. Nos vamos a la ventana de ‘Autoridades’, y pulsamos el botón de ‘Importar…’. Buscamos el certificado de nuestra CA en `~/easy-rsa/pki/ca.crt`. Una vez vemos nuestra Autoridad Certificada en el listado, pulsamos el botón ‘Aceptar’.

## Comprobación

Si accedemos ahora a nuestro sitio nuevo con HTTPS, debería mostrar que la conexión es segura.
- `-k` o `--insecure`: Para certificados SSL autofirmados.
- `-v`: Modo verbose para mostrar detalles.

El resultado mostrará la información del certificado creado, demostrando que la configuración y el certificado autofirmado están en funcionamiento.
