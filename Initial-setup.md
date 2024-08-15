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

#############################
Continuación del desarrollo
#############################

## Modelos Base de datos
//TODO:
Revisar que los modelos de las tablas se correspondan con la estructura deseada.
Revisar con forms.py que tiene la estructura de datos (crear una tabla)


Instalación de Flask-Migrate

```bash
pip3 install Flask-Migrate
```

Antes de crear la migración:

- creamos la carpeta `migrations`
- importamos en app.py `from flask_migrate import Migrate`
- agregamos a app.py `migrate = Migrate(app,db)`

Finalmente, aplicamos el siguiente comando:

  ```bash
  flask db init
  # Al dejar las migraciones fuera del archivo src
  flask --app src/app db init
  ```

Nota:
Una forma de continuar alternativa, sería seguir trabajando con la información fake.
Dado que la aplicación se encuentra funcional, avanzar con las rutas y funcionalidades. Una vez obtenida la funcionalidad deseada, avanzar en la persistencia de los datos.

Ahora corremos la migración para detectar los cambios en los modelos y upgrade para reflejarlos en la base de datos:

  ```bash
  # Al dejar las migraciones fuera del archivo src
  flask --app src/app db migrate -m "Agregar un mensaje si se desea"
  flask --app src/app db upgrade
  ```

Nota:

La relación muchos a muchos utilizada tiene por referencia la siguiente documentación:
[REF](https://docs.sqlalchemy.org/en/14/orm/basic_relationships.html#many-to-many)

Título: Association Object¶

```python
class Association(Base):
    __tablename__ = "association_table"
    left_id = Column(ForeignKey("left_table.id"), primary_key=True)
    right_id = Column(ForeignKey("right_table.id"), primary_key=True)
    extra_data = Column(String(50))
    child = relationship("Child", back_populates="parents")
    parent = relationship("Parent", back_populates="children")


class Parent(Base):
    __tablename__ = "left_table"
    id = Column(Integer, primary_key=True)
    children = relationship("Association", back_populates="parent")


class Child(Base):
    __tablename__ = "right_table"
    id = Column(Integer, primary_key=True)
    parents = relationship("Association", back_populates="child")
  ```

## Formularios

El desarrollo lo continuamos en app.py, pasando los datos fake a datos provenientes de la base de datos.

Para lo anterior, vamos a partir por la creación de los venue (eventos).
El flujo de los datos es:

1. Crear un formulario para la creación de un venue.
2. Crear una ruta para la creación de un venue.
3. Crear una ruta para la visualización de los venues creados.

## Flujos de datos explicados

### Crear Venues
Homepage -> button 'Post a venue'

Homepage corresponde a la página de template en html.
El botón 'Post venue' tiene el link a la ruta '/venue/create', por tanto, al hacerle click hace una petición GET al servidor (app.py).
En la ruta '/venue/create' encontramos dos route accesibles, uno por el metodo GET y el segundo por el método POST.
En este caso, al hacer la petición GET ingresamos a la función 'create_venue_form()' que crea un objeto de tipo formulario. Este modelo de dato fue importado desde el archivo 'form.py' en el cual creamos 3 tipos de formularios (ShowForm(), VenueForm() y ArtistForm()).
El formulario creado es enviado por parámetro a la plantilla 'forms/new_venue.html' que es renderizada en el navegador `return render_template('forms/new_venue.html', form=form)`.
En la plantilla 'new_venue.html' se renderiza el formulario, el cual recolecta todos los datos de los distintos campos que le fueron creados en forms.py y que ahora están accesibles en la plantilla html.
El formulario, tiene la siguiente configuración: `<form method="post" class="form" action="/venues/create">`; por tanto, al hacer click en el botón 'Submit' se envía una petición POST al servidor, la cual es recibida por la función 'create_venue_submission()'.
En esta función se valida el formulario y se obtienen los datos de cada uno de los campos del formulario mediante un `request.form.get('name')`para los campos de string y `request.form.getlist('genders')` para los array.
Una vez guardado cada valor de los campos en una respectiva variable, se usan estas para crear una nueva instancia del objeto 'venue', la que luego se envía a la base de datos en un bloque try catch. Si la instrucción genera un error, se ingresará en el bloque catch, generandose el rollback para que no se guarden los datos a la base de datos, la variable 'error' tomara un valor 'True' y se enviarán los errores. Si la instrucción estuvo correcta, se guardarán los datos en la base de datos y se renderizará la página home junto a un mensaje flash de éxito.

Actualizar la referencia a los flashing:
[Simple Flashing](https://flask.palletsprojects.com/en/3.0.x/patterns/flashing/#simple-flashing)


#### Queries


Flask-SQLAlchemy en su version 3.1 considera que la interfaz de consultas que utiliza la siguiente forma ya está deprecada (legacy):
[Legacy Query](https://flask-sqlalchemy.palletsprojects.com/en/3.1.x/legacy-query/)
  
```python
venues = Venue.query.all()
```

En dicha versión considera que es preferible el uso de la siguiente expresión:
  
  ```python
  venues = db.session.query(Venue).all()
  ```

Referencia para buscar las posibles consultas:
[SQLAlchemy Query API](https://docs.sqlalchemy.org/en/14/orm/query.html)

Por tanto, algunas expresiones posibles de consultas podemos listarlas como sigue:
  
  ```python
  # Consulta de todos los elementos
  venues = db.session.query(Venue).all()
  # Consulta filtrando por un término
  venues = db.session.query(Venue).filter(Venue.city == 'San Francisco').all()
  # Consulta filtrando por un término y ordenando
  venues = db.session.query(Venue).filter(Venue.city == 'San Francisco').order_by(Venue.name).all()
  # Consulta filtrando por un término y ordenando de forma descendente
  venues = db.session.query(Venue).filter(Venue.city == 'San Francisco').order_by(desc(Venue.name)).all()
  # Consulta filtrando por un término y paginando
  venues = db.session.query(Venue).filter(Venue.city == 'San Francisco').paginate(page, per_page, False)
  # Consulta filtrando por un término y paginando con un ordenamiento
  venues = db.session.query(Venue).filter(Venue.city == 'San Francisco').order_by(desc(Venue.name)).paginate(page, per_page, False)
  # Consultas usando distinct
  venues = db.session.query(Venue).distinct(Venue.city).all()
  # Consultas usando join
  venues = db.session.query(Venue).join(Artist).all()
  # Consultas usando join y filtrando
  venues = db.session.query(Venue).join(Artist).filter(Artist.city == 'San Francisco').all()
  # Consultas usando join y filtrando y ordenando
  venues = db.session.query(Venue).join(Artist).filter(Artist.city == 'San Francisco').order_by(desc(Artist.name)).all()
  # Consultas usando join y filtrando y ordenando y paginando
  venues = db.session.query(Venue).join(Artist).filter(Artist.city == 'San Francisco').order_by(desc(Artist.name)).paginate(page, per_page, False)
  # Consultas usando subquery
  venues = db.session.query(Venue).filter(Venue.city.in_(db.session.query(Artist.city).filter(Artist.city == 'San Francisco'))).all()
  # Consultas usando subquery y filtrando
  venues = db.session.query(Venue).filter(Venue.city.in_(db.session.query(Artist.city).filter(Artist.city == 'San Francisco'))).filter(Venue.name == 'The Musical Hop').all()
  # Consultas usando subquery y filtrando y ordenando
  venues = db.session.query(Venue).filter(Venue.city.in_(db.session.query(Artist.city).filter(Artist.city == 'San Francisco'))).filter(Venue.name == 'The Musical Hop').order_by(desc(Venue.name)).all()
  # Consultas usando subquery y filtrando y ordenando y paginando
  venues = db.session.query(Venue).filter(Venue.city.in_(db.session.query(Artist.city).filter(Artist.city == 'San Francisco'))).filter(Venue.name == 'The Musical Hop').order_by(desc(Venue.name)).paginate(page, per_page, False)
  # Consulta para contar
  venues = db.session.query(Venue).count()
  # Consulta para mostrar una cantidad especifica de resultados
  venues = db.session.query(Venue).limit(10).all()
  venues = db.session.query(Venue).get(10)
  # Consultas usando ilike
  venues = db.session.query(Venue).filter(Venue.name.ilike('%musical%')).all()
  # Consultas usando like
  venues = db.session.query(Venue).filter(Venue.name.like('%musical%')).all()
  # Consultas usando not
  venues = db.session.query(Venue).filter(not_(Venue.name == 'The Musical Hop')).all()
  # Consultas usando lambda
  venues = db.session.query(Venue).filter(lambda: Venue.name == 'The Musical Hop').all()
  ```

Significado de las palabras claves de las consultas:
- `filter`: Filtra los datos de la tabla según el término que se le pase.
- `order_by`: Ordena los datos de la tabla según el término que se le pase.
- `desc`: Ordena los datos de forma descendente.
- `paginate`: Pagina los datos de la tabla según el número de página y la cantidad de elementos por página que se le pase.
- `distinct`: Filtra los datos de la tabla según el término que se le pase.
- `join`: Realiza un join entre dos tablas.
- `in_`: Filtra los datos de la tabla según el término que se le pase.
- `subquery`: Realiza una subconsulta.
- `all()`: Devuelve todos los resultados de la consulta.
- `first()`: Devuelve el primer resultado de la consulta.
- `one()`: Devuelve un solo resultado de la consulta.
- `one_or_none()`: Devuelve un solo resultado de la consulta o None si no hay resultados.
- `scalar()`: Devuelve un solo resultado de la consulta o None si no hay resultados.
- `count()`: Devuelve el número de resultados de la consulta.
- `delete()`: Elimina los resultados de la consulta.
- `update()`: Actualiza los resultados de la consulta.
- `add()`: Agrega un nuevo elemento a la consulta.
- `get()`: Devuelve un solo resultado de la consulta.
- `get_or_404()`: Devuelve un solo resultado de la consulta o un error 404 si no hay resultados.
- `limit()`: Limita el número de resultados de la consulta.
- `ilike()`: Filtra los datos de la tabla según el término que se le pase de forma insensible a mayúsculas y minúsculas.
- `like()`: Filtra los datos de la tabla según el término que se le pase.
- `not_`: Filtra los datos de la tabla según el término que se le pase.
- `lambda`: Filtra los datos de la tabla según el término que se le pase.


Uso de lambda:
```python
venues = db.session.query(Venue).filter(lambda: Venue.name == 'The Musical Hop').all()
```
En este ejemplo, la función lambda se utiliza para filtrar los datos de la tabla Venue según el
nombre del lugar sea igual a 'The Musical Hop'.

Cómo se usa lambda en las query de flask-sqlachemy?

```python
venues = db.session.query(Venue).filter(lambda: Venue.name == 'The Musical Hop').all()
```
En este ejemplo, la función lambda se utiliza para filtrar los datos de la tabla Venue según el
nombre del lugar sea igual a 'The Musical Hop'.


Solucionando problema:

Al colectar los datos del formulario de los genders (genéros), usamos el método getlist() que guarda las opciones en la base de datos de la siguiente forma:

```
{Blues,Electronic,Jazz,Pop} 
```

Al recuperar estos datos para presentarlos por el frontend, si los imprimimos por pantalla se ven:

```
['{', 'C', 'l', 'a', 's', 's', 'i', 'c', 'a', 'l', ',', 'F', 'o', 'l', 'k', ',', 'J', 'a', 'z', 'z', ',', '"', 'R', 'o', 'c', 'k', ' ', 'n', ' ', 'R', 'o', 'l', 'l', '"', '}']
```

Es decir, la lista se muestra como una lista de caracteres (lo que en otros lenguajes llamaríamos char, pero python no tiene ese tipo de dato y lo trata como string).

Al llevar este dato a la plantilla, cuando se renderiza esta, muestra los caracteres impresos por separado, y por tanto, a nivel de estilo de la plantilla podemos ver que cada caracter por separado toma el estilo diseñado para abarcar la palabra completa del 'genero musical' de este proyecto.

Para resolver lo anterior, usamos algunos métodos de manejos de string:
- `join()`: Unimos todos los elementos de la lista en una cadena.
- `strip('{}')`: Eliminamos los carácteres innecesarios como los parentesis de llave.
- `split(',')`: Finalmente, dividimos la cadena de string creada en una lista de strings. Para ello separamos los elementos mediante la coma que los unía.

Ahora, se visualizan correctamente los generos con sus respectivos estilos en la plantilla correspondiente.

### Eliminar Venues

Para eliminar un venue, se crea un botón en la plantilla 'venues.html'.
Como el formulario no permite peticiones del tipo 'DELETE', hubo que modificar el endpoint para pasarlo de metodo 'DELETE' a 'POST'.

Por otra parte, al formulario le proporcionamos mediante el input del tipo submit la acción de url_for, la cual tiene por primer parámetro el nombre de la función del endpoint al que se dirige la petición (Podría haber más de una función por la cual se acceda con el endpoint'venues/<id_venue>' y el método post), y como segundo parámetro se envía la id del elemento a eliminar.

En la función 'delete_venue()' se recibe la id del elemento a eliminar, se busca en la base de datos y se elimina. Finalmente, se redirige a la página de inicio.

### Flask-WTF

Flask-WTF es una extensión de Flask que proporciona una integración con WTForms, una biblioteca de formularios en Python.

Considerar que el valor de un checkbox es booleano, no obstante, los valores arrojados por el Flask-Wtf son:
- 'y': Si el checkbox está seleccionado.
- None: Si el checkbox no está seleccionado.

Para solucionar lo anterior, se puede usar el método `getlist()` que devuelve una lista con los valores de los checkbox, en caso de valores múltiples.

En nuestro caso creamos una validación para asignar un valor booleano, más conveniente para guardarlo en la base de datos para manipulaciones posteriores:

  ```python
    seeking_venue = True if request.form.get('seeking_venue') else False
    # o
    seeking_venue = True if seeking_venue == 'y' else False
  ```
Otra alternativa sería:

```python
  if seeking_venue == 'y':
    seeking_venue = True
  else:
    seeking_venue = False
```


//TODO: Revisar los métodos update