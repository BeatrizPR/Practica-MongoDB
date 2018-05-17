# Practica-MongoDB
Pequeña práctica realizada con MongoDB

### Arrancamos el servicio

```console
c:\mongodb\bin\mongod.exe 
```

### Ejecutamos la interfaz de comandos de MongoDB

```console
c:\mongodb\bin\mongo.exe
```



## Creamos la base de datos

```console
> use MongoDBPractica
switched to db MongoDBPractica
```
En mi caso la he creado sin mirar las base de datos, porque no tenía antes ninguna.

### Creamos objetos para tener una coleccion

```console
> use MongoDBPractica
switched to db MongoDBPractica
> var alumno1 = {nombre: "Francisco", apellido: "Mora"} 
> var alumno2 = {nombre: "Ana", apellido: "Baza", pais: "España"} 
> alumno1 { "nombre" : "Francisco", "apellido" : "Mora" } 
> alumno2 { "nombre" : "Ana", "apellido" : "Baza", "pais" : "España" }
```

```console
> db.usuarios.insert(alumno1)
WriteResult({ "nInserted" : 1 })
> db.usuarios.insert(alumno2)
WriteResult({ "nInserted" : 1 })
> db.usuarios.insert({nombre: "Alba", apellido: "Gonzalez", edad: 18, pais: “Italia”})
WriteResult({ "nInserted" : 1 })
> db.usuarios.insert({nombre: "Clara", apellido: "Martinez", edad: 27})
WriteResult({ "nInserted" : 1 })
```

### Muestro la colección

```console
> show collections
usuarios
```

### Insertar

```console
> db.usuarios.insert({nombre: "Alberto", apellido: "Gonzalez", edad: 30, pais: “España”}) 
WriteResult({ "nInserted" : 1 }) 
> db.usuarios.insert({nombre: "Mario", apellido: "Chamorro", edad: 32, pais: “Francia”}) 
WriteResult({ "nInserted" : 1 })
```

### Modificar

Modifico la edad de Mario de 32 a 24 años.

```console
> p = db.usuarios.findOne({nombre: "Mario"})
{
	"_id" : ObjectId("5afc4855c3e8a72502396863"),
	"nombre" : "Mario",
	"apellido" : "Chamorro",
	"edad" : 32,
	"pais" : "Francia"
}
> p.edad = 24
24
> db.agenda.update({nombre: "Encarna"}, p)
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

Compruebo que se ha modificado correctamente.

```console
> db.agenda.find({nombre: "Mario"})
{ "_id" : ObjectId("5afc4855c3e8a72502396863"), "nombre" : "Mario", "apellido" : "Chamorro", "edad" : 24, "pais" : "Francia" }
```

### Borrar

Borro la alumna Ana.

```console
> db.usuarios.remove({nombre: "Ana", apellido: "Baza"})
WriteResult({ "nRemoved" : 1 })
```

### Creo un índice

Creo el índice sobre los apellidos de los alumnos

```console
> db.usuarios.createIndex({apellido: 1})
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
```

### Consultas con igualdad, mayor que y menor que


Busco si hay algún alumno con 18 años.

```console
> db.usuarios.find( { edad: {$eq: 18} } )
{ "_id" : ObjectId("5afc4855c3e8a72502396860"), "nombre" : "Alba", "apellido" : "Gonzalez" , "edad": 18, "pais" : "Italia"}
```

Busco alumnos mayores de 10 años.

```console
> db.usuarios.find( { edad: {$gt: 10} } )
{ "_id" : ObjectId("5afc4694c3e8a72502396860"), "nombre" : "Alba", "apellido" : "Gonzalez" , "edad": 18, "pais" : "Italia"}
{ "_id" : ObjectId("5afc46cdc3e8a72502396861"), "nombre" : "Clara", "apellido" : "Martinez" , "edad": 27}
{ "_id" : ObjectId("5afc481ec3e8a72502396862"), "nombre" : "Alberto", "apellido" : "Gonzalez" , "edad": 30, "pais" : "España"}
{ "_id" : ObjectId("5afc4855c3e8a72502396863"), "nombre" : "Mario", "apellido" : "Chamorro" , "edad": 24, "pais" : "Francia"}
```

Busco alumnos menores de 25 años.

```console
> db.usuarios.find( { edad: {$gt: 10} } )
{ "_id" : ObjectId("5afc4694c3e8a72502396860"), "nombre" : "Alba", "apellido" : "Gonzalez" , "edad": 18, "pais" : "Italia"}
{ "_id" : ObjectId("5afc4855c3e8a72502396863"), "nombre" : "Mario", "apellido" : "Chamorro" , "edad": 24, "pais" : "Francia"}
```

### Consulta con resultados limitados y ordenados

Ordeno con un límite de 2 de forma ascendente por el nombre.

```console
> db.usuarios.find().sort({nombre: 1}).limit(2)
{ "_id" : ObjectId("5afc4694c3e8a72502396860"), "nombre" : "Alba", "apellido" : "Gonzalez" , "edad": 18, "pais" : "Italia"}
{ "_id" : ObjectId("5afc481ec3e8a72502396862"), "nombre" : "Alberto", "apellido" : "Gonzalez" , "edad": 30, "pais" : "España"}
```

Ordeno con un límite de 3 de forma descendente por el nombre.

```console
> db.usuarios.find().sort({nombre: -1}).limit(3)
{ "_id" : ObjectId("5afc4855c3e8a72502396863"), "nombre" : "Mario", "apellido" : "Chamorro" , "edad": 24, "pais" : "Francia"}
{ "_id" : ObjectId("5afc4653c3e8a7250239685e"), "nombre" : "Francisco", "apellido" : "Mora"}
{ "_id" : ObjectId("5afc46cdc3e8a72502396861"), "nombre" : "Clara", "apellido" : "Martinez" , "edad": 27}
```

### Consulta de agrupamiento con media y suma

Aquí inserto más alumnos para poder tener más datos con los que hacer la consulta

Agrupamiento de paises, con cuantos alumnos haya en cada uno de estos y la media de la edad.

```console
> db.usuarios.aggregate( [ {$match: {pais: {$ne: null}}}, {$group: {_id: "$pais", aparece: {$sum: 1}, "edad media": {$avg: "$edad"}}} ])
{ "_id" : "Francia", "repetidos" : 2, "edad media" : 22 }
{ "_id" : "Portugal", "repetidos" : 1, "edad media" : 28 }
{ "_id" : "España", "repetidos" : 3, "edad media" : 24.666666666668 }
{ "_id" : "Italia", "repetidos" : 1, "edad media" : 18 }
```

✲ **La practica que he realizado es esta [MongoDB](https://github.com/IESCampanillas/practica-MongoDB-DAW/blob/master/Practica_MongoDB.pdf)**