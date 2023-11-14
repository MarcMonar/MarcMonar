# Control de Acceso: Listas y Práctica

## Introducción

La labor de un gestor de sistemas no solo implica la administración técnica, sino también la salvaguarda de archivos con la integridad y autenticación necesarias. Esta práctica se centra en la edición controlada de Listas de Control de Acceso (ACL) para fomentar la práctica en un entorno seguro.

## Proceso

### Ejercicio

La estructura de directorios se diseñará según el siguiente esquema:

```
Directorio Compartido de Grupos/
├── Grupo Teachers
├── ESO1
├── ESO2
└── Estudiantes
```

Se asignarán usuarios y grupos de la siguiente manera:

**Grupos:**
- Alumnos
- Usuarios
- ESO1
  - Matemáticas
- ESO2

**Usuarios:**
- Est1
- Est2
- Est3
- Prof1
- Prof2

#### Condiciones

- El grupo `alumnos` incluirá a todos los estudiantes.
- En el grupo `profesores` estarán todos los Profesores.
- En los grupos `ESO1` y `ESO2`, los estudiantes Est1 y Est2 se unirán a ellos en sus respectivos grupos.
- Est3 no tiene otro grupo aparte de `alumnos`.
- A la carpeta `ESO1` tendrá acceso: Profesores con permisos `rw` y ESO1 con permisos `r`.
- A la carpeta `ESO2` tendrá acceso: Profesores con permisos `rw` y ESO2 con permisos `r`.
- A la carpeta `Profesores` tendrá acceso: Prof1 con permisos `rw` y Profesores con permisos `r`.
- A la carpeta `alumnos` tendrá acceso: Profesores con permisos `rw` y Alumnos con permisos `r`.

### Desarrollo

Creamos la estructura de directorios utilizando el comando tree:

```bash
Directorio Compartido de Grupos/
├── ESO1
│   └── matematicas
├── ESO2
├── alumnos
└── profesores
```

Luego, creamos los grupos y usuarios con el siguiente script Bash:

```bash
#!/bin/bash
addgroup profesores
for i in 1 2; do
    adduser est$i
    adduser prof$1
    addgroup eso$i
    adgroup prof$1 profesores
    adduser est$1 alumnos
done
addgroup est1 eso1
addgroup est2 eso2
adduser est3
```

Una vez creados los grupos y usuarios, continuamos con la asignación de permisos mediante el script `acl.sh`:

```bash
#!/bin/bash
setfacl -Rb *
setfacl -Rdm u:prof1:rw,g:profesores:r profesores
setfacl -Rdm g:profesores:rwX,g:eso1:rX eso1
setfacl -Rdm g:profesores:rwX,g:eso2:rX eso2
setfacl -Rdm g:alumnos:rX,g:profesores:rwX alumnos

setfacl -Rm u:prof1:rw,g:profesores:r profesores
setfacl -Rm g:profesores:rwX,g:eso1:rX eso1
setfacl -Rm g:profesores:rwX,g:eso2:rX eso2
setfacl -Rm g:alumnos:rX,g:profesores:rw alumnos
```

Establecemos los permisos en las carpetas:

```bash
$ sudo bash acl.sh
```

Los permisos resultantes serán similares a los siguientes:

```bash
# Permisos para eso1
# ...

# Permisos para eso2
# ...

# Permisos para alumnos
# ...

# Permisos para profesores
# ...
```

## Conclusión / Verificación

### Est1 no puede leer eso2

```bash
$ echo "Prueba Lectura" > eso2/lectura.md
$ su est1
$ cd eso2
bash: cd: eso2: Permiso denegado
```

### Herencia de permisos en eso1/matematicas y creación de carpeta de física

```bash
$ ll
# ...
$ cd matematicas/
$ pwd
/Directorio Compartido de Grupos/eso1/matematicas
# ...
```

### Prof2 no puede escribir en profesores

```bash
$ su prof2
$ cd /Directorio\ Compartido\ de\ Grupos/profesores/
$ touch hola
touch: no se puede efectuar `touch' sobre 'hola': Permiso denegado
```


