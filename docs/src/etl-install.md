# Instalación y Ejecución del ETL Georef

El proyecto `georef-ar-etl` utiliza los siguientes componentes para cumplir sus funciones:

- PostgreSQL 9.5
- PostGIS 2.4
- Python 3.5 + SQLAlchemy
- ogr2ogr (GDAL) 2.2.2

A continuación, se detallan los pasos a seguir para instalar y ejecutar el ETL en un entorno Ubuntu 16.04 (Xenial).

## 1. Instalación

### 1.2 Dependencias

Primero, instalar PostgreSQL, PostGIS y ogr2ogr utilizando el comando `apt`:
```bash
$ sudo add-apt-repository -y ppa:ubuntugis/ppa
$ sudo apt update
$ sudo apt install postgresql-9.5 postgresql-9.5-postgis-2.4 gdal-bin libpq-dev
```

### 1.2 Código

Luego, clonar el repositorio:
```bash
$ git clone https://github.com/datosgobar/georef-ar-etl.git
$ cd georef-ar-etl
```

### 1.3 Configuración

Crear un nuevo archivo de configuración `georef.cfg`. Se recomienda partir desde el archivo de ejemplo en `config/georef.example.cfg`:
```bash
cp config/georef.example.cfg config/georef.cfg
```

El archivo de configuración contiene, bajo la sección `[db]`, la configuración necesaria para establecer una conexión a la base de datos PostgreSQL. Los siguientes pasos de esta guía utilizan los siguientes valores de ejemplo:
```ini
[db]
host = localhost
port = 5432
database = georef_ar_etl
user = georef
password = changeme
```

### 1.4 Base de Datos

Para el funcionamiento del ETL, se debe contar con una base de datos con la extensión PostGIS habilitada, y un usuario que pueda crear, eliminar y modificar tablas.

Bajo un usuario administrador de PostgreSQL (por defecto, `postgres`), utilizar el comando `psql` para ejecutar las sentencias necesarias:
```sql
create database georef_ar_etl with encoding = 'utf-8';
create user georef with login password 'changeme';
```

Luego, conectarse a la base de datos utilizando el comando `\c georef_ar_etl`, y ejecutar las siguientes sentencias:
```sql
create extension postgis;
grant all privileges on all tables in schema public to georef;
```

### 1.5 Entorno Python

En la raíz del proyecto clonado con `git`, ejecutar los siguientes comandos para crear un nuevo entorno virtual de Python con `venv`:

```bash
$ python3 -m venv env
$ source env/bin/activate
```

Luego, instalar los paquetes necesarios:
```bash
(env) $ pip install -r requirements.txt
```

### 1.6 Migración inicial

Para crear las tablas utilizadas en el proceso de ETL, utilizar la receta `migrate`:
```bash
(env) $ make migrate
```

El comando debe volver a ejecutarse si se actualiza el proyecto y existen nuevas migraciones.

## 2. Ejecución

Una vez finalizado el proceso de instalación, utilizar la receta `run` para ejecutar todas las tareas del ETL. **El entorno virtual de Python debe estar activado**.

```bash
(env) $ make run
```

Por defecto, los productos del ETL serán:

Las tablas:

 - `georef_provincias`
 - `georef_departamentos`
 - `georef_municipios`
 - `georef_localidades`
 - `georef_calles`
 - `georef_intersecciones`
 - `georef_cuadras`
 
 Y los archivos (bajo `/files/latest/`):
 
 - `provincias.json`
 - `departamentos.json`
 - `municipios.json`
 - `localidades.json`
 - `calles.json`
 - `intersecciones.json`
 - `cuadras.json`