# Laboratorio MongoDB - Diseño Web II

**Nota**: El usuario y la contraseña se encuentran en el BlackBoard por razones de seguridad

**Console Connection String**
```mongo "mongodb+srv://cineulacit.hadat.mongodb.net/CineULACIT" --username <username>```

**Compass Connection String**
```mongodb+srv://<username>:<password>@cineulacit.hadat.mongodb.net/CineULACIT```
 
```javascript
// [01] Visualizar todos los actores
db.actores.find().pretty()

// [02] Ver los precios de las entradas de las personas de la tercera edad
db.precios.distinct("tercera_edad")

// [03] Contar la cantidad de peliculas para todo publico
db.peliculas.count({"califiacion_edad": {$lt: 18}})

// [04] Ver los actores mayores a 50 años
db.actores.find({"edad": {$gt: 50}})

// [05] Ver todas las peliculas de Drama y Animadas
db.peliculas.find({genero: /^Drama|Animada/}) 

// [06] Ver las peliculas para mayores de 16 y 18 años
db.peliculas.find({$or: [{"califiacion_edad" : 16}, {"califiacion_edad" : 18}]})

// [07] Ver los horarios de las peliculas disponibles en todos los cines
db.horarios.aggregate({ $lookup:{ from:"cines", localField:"cine", foreignField:"_id", as:"cine" } },{ $lookup:{ from:"peliculas", localField:"pelicula", foreignField:"_id", as:"pelicula" } })

// [08] Ver todas las peliculas con los detalles de cada actor de la pelicula
db.peliculas.find().forEach(
    function (final) {
        final.actores = db.actores.find( { "_id": { $in: final.actores }  } ).toArray();
        print(final);
    }
);

// [09] Ver las peliculas de la tanda de la tarde con todos los detalles
db.horarios.find({hora: "tarde"}).forEach(
    function (final) {
        final.pelicula = db.peliculas.findOne( { "_id": final.pelicula } );
        final.cine = db.cines.findOne( { "_id": final.cine } );
        print(final);
    }
);

// [10] Ver todas las peliculas que estan dando en la sucursal de Curridabat con detalles
db.cines.find({sucursal: "Curridabat"}).forEach(
    function (final) {
        final.preliculas = db.peliculas.find( { "_id": { $in: final.preliculas }  } ).toArray();
        print(final);
    }
);
```
------

## Scripts Inserción de Datos

```javascript

//Creación de colecciones
db.createCollection("actores")
db.createCollection("peliculas")
db.createCollection("cines")
db.createCollection("horarios")
db.createCollection("precios")

//Datos de Actores
db.actores.insertMany([
    {
        "nombre": "Jennifer Fernandez",
        "edad": 25
    },
    {
        "nombre": "Will Smith",
        "edad": 40
    },
    {
        "nombre": "Michael Junior",
        "edad": 55
    },
    {
        "nombre": "Rosario Juls",
        "edad": 15
    },
    {
        "nombre": "Ginger Bridgetown",
        "edad": 80
    }

]);

//Datos de Precios
db.precios.insertMany([
    {
        "menor": 2300,
        "adulto": 3100,
        "tercera_edad":2500
    }
]);

//Datos de Peliculas
db.peliculas.insertMany([
    {
        "nombre": "Titanic",
        "director": "James Cameron",
        "precio": 150000,
        "genero": "Drama",
        "actores": [ObjectId("6029c50f140eff569d567a01"), ObjectId("6029c581140eff569d567a04")],
        "califiacion_edad": 0
    },
    {
        "nombre": "Interstellar",
        "director": "Julio Castro",
        "precio": 250000,
        "genero": "Ficcion",
        "actores": [ObjectId("6029c50f140eff569d567a02"), ObjectId("6029c581140eff569d567a03")],
        "califiacion_edad": 0
    },
    {
        "nombre": "50 Shades of Gray",
        "director": "Julieta Gray",
        "precio": 10000,
        "genero": "Romantica",
        "actores": [ObjectId("6029c50f140eff569d567a01"), ObjectId("6029c581140eff569d567a05")],
        "califiacion_edad": 18
    },
    {
        "nombre": "Rapidos y Furiosos",
        "director": "John Cena",
        "precio": 35000,
        "genero": "Accion",
        "actores": [ObjectId("6029c50f140eff569d567a02"), ObjectId("6029c581140eff569d567a05")],
        "califiacion_edad": 16
    },
    {
        "nombre": "Toy Story",
        "director": "Walt Disney",
        "precio": 1000000,
        "genero": "Animada",
        "actores": [],
        "califiacion_edad": 0
    }

]);


//Datos de Cines
db.cines.insertMany([
    {
        "sucursal": "Escazu",
        "preliculas": [ObjectId("6029c8b8140eff569d567a07"), ObjectId("6029c8b8140eff569d567a08"),ObjectId("6029c8b8140eff569d567a09"), ObjectId("6029c8b8140eff569d567a0a")],
        "precios":ObjectId("6029ca26140eff569d567a11")
    },
    {
        "sucursal": "Curridabat",
        "preliculas": [ObjectId("6029c8b8140eff569d567a07"), ObjectId("6029c8b8140eff569d567a08"),ObjectId("6029c8b8140eff569d567a10"), ObjectId("6029c8b8140eff569d567a0a")],
        "precios":ObjectId("6029ca26140eff569d567a11")
    },
    {
        "sucursal": "Alajuela",
        "preliculas": [ObjectId("6029c8b8140eff569d567a10"), ObjectId("6029c8b8140eff569d567a08"),ObjectId("6029c8b8140eff569d567a09"), ObjectId("6029c8b8140eff569d567a0a")],
        "precios":ObjectId("6029ca26140eff569d567a11")
    },
    {
        "sucursal": "Heredia",
        "preliculas": [ObjectId("6029c8b8140eff569d567a07"), ObjectId("6029c8b8140eff569d567a08"),ObjectId("6029c8b8140eff569d567a09"), ObjectId("6029c8b8140eff569d567a10")],
        "precios":ObjectId("6029ca26140eff569d567a11")
    },
    {
        "sucursal": "Limon",
        "preliculas": [ObjectId("6029c8b8140eff569d567a07"), ObjectId("6029c8b8140eff569d567a10"),ObjectId("6029c8b8140eff569d567a09"), ObjectId("6029c8b8140eff569d567a0a")],
        "precios":ObjectId("6029ca26140eff569d567a11")
    }

]);

//Datos de Horarios
db.horarios.insertMany([
    {
        "cine": ObjectId("6029cbac140eff569d567a12"),
        "pelicula": ObjectId("6029c8b8140eff569d567a07"),
        "hora": "mañana"
    },
    {
        "cine": ObjectId("6029cbac140eff569d567a13"),
        "pelicula": ObjectId("6029c8b8140eff569d567a08"),
        "hora": "tarde"
    },
    {
        "cine": ObjectId("6029cbac140eff569d567a14"),
        "pelicula": ObjectId("6029c8b8140eff569d567a09"),
        "hora": "noche"
    },
    {
        "cine": ObjectId("6029cbac140eff569d567a15"),
        "pelicula": ObjectId("6029c8b8140eff569d567a10"),
        "hora": "mañana"
    },
    {
        "cine": ObjectId("6029cbac140eff569d567a16"),
        "pelicula": ObjectId("6029c8b8140eff569d567a0a"),
        "hora": "tarde"
    }

]);
```

