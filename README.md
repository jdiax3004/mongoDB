# Laboratorio MongoDB - Diseño Web II

**Nota**: El usuario y la contraseña se encuentran en el BlackBoard

Console Connection String
`mongo "mongodb+srv://cineulacit.hadat.mongodb.net/CineULACIT" --username <username>`

Compass Connection String
`mongodb+srv://<username>:<password>@cineulacit.hadat.mongodb.net/CineULACIT`
 
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
