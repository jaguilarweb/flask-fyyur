# Proyecto Fyyur

Este proyecto corresponde al proyecto final de la sección  de FullStack Nano Degree (FSND) impartido por Udacity.

Este curso lo realicé el año 2020 y luego de varios años vuelvo a repasarlo, por tanto, este proyecto incorpora más experiencia en desarrollo actualizandolo para el año 2024.


## Creación del ambiente de desarrollo

Descarga del proyecto:

```bash
git clone https://github.com/udacity/FSND.git
```

Se crea el proyecto con nombre 'flask-fyyur-v2020' y se modifica el nombre de la carpeta 'starter_code' por 'src'.

Se inicia un repositiorio git para versionamiento con:
  
  ```bash
  git init
  ```

Fuera de la carpeta src se inicia un ambiente virtual:
  
  ```bash
  python3 -m venv venv
  ```

Se activa el ambiente virtual:
  
  ```bash
  source venv/bin/activate
  ```

Es innecesaria la inicialización de nodejs e instalación de bootstrap por consola, ya que el código inicial incorpora las librerías estáticas.


### Instalación de librerías necesarias

En el proyecto hay un archivo de requirement, pero la librería 'distutils' da problemas porque se deprecó para la versión de `python3.12` que es en la que estoy desarrollando este proyecto. Por dicha razón, instalo manualmente las librerías para el proyecto:

Librerías instaladas:

- babel==2.15.0
- flask-wtf==1.2.1
- flask_sqlalchemy==3.1.1
- flask-moment==1.0.6
- python-dateutil==2.9.0
- psycopg2==2.9.9

```bash
  pip3 install <librería>
```

Nota:
Comando para guardar la configuración de las librerias instaladas en el proyecto:

  ```bash
  pip freeze > requirements.txt
  #ó
  python3 -m pip freeze > src/requirements.txt
  ```

### Configuración de la base de datos

Se crea la base de datos en postgresql con el nombre 'fyyur':

```bash
createdb fyyur
```

Se configura la base de datos en el archivo 'config.py' de la carpeta 'src':

```python
SQLALCHEMY_DATABASE_URI = 'postgresql://postgres@localhost:5432/fyyur'
```
En caso de incorporar contraseña:

```python
SQLALCHEMY_DATABASE_URI = 'postgresql://postgres:password@localhost:5432/fyyur'
```

Levantar la aplicación con:

```bash
python3 src/app.py
```

Ya se puede acceder a la aplicación desde la url `http://localhost:5000/`
