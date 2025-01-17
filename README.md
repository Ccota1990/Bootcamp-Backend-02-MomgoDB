# Introducción
En este base de datos puedes encontrar un montón de alojamientos y sus reviews, esto está sacado de hacer webscrapping.

**Pregunta. Si montaras un sitio real, ¿Qué posibles problemas pontenciales les ves a como está almacenada la información?** 

Indica aquí que problemas ves

 ## Obligatorio
Esta es la parte mínima que tendrás que entregar para superar este laboratorio.

### Consultas
- Saca en una consulta cuantos alojamientos hay en España.

```javascript
use ("airbnb")

db.listingsAndReviews.countDocuments({ 
    "address.country": {$eq: "Spain"}
})
```

- Lista los 10 primeros alojamientos de España:
  - Ordenados por precio de forma ascendente.
  - Sólo muestra: nombre, precio, camas y la localidad (address.market).

```javascript
use ("airbnb")

db.listingsAndReviews.find( 
    {"address.country": {$eq: "Spain"}},
    {_id:0, name: 1, price: 1 ,
        beds:1, "address.market":1}
    ).limit(10).sort({price:1})
```

### Filtrando
- Queremos viajar cómodos, somos 4 personas y queremos:
  - 4 camas.
  - Dos cuartos de baño o más.
  - Sólo muestra: nombre, precio, camas y baños.

```javascript
use ("airbnb")

db.listingsAndReviews.find( 
    {
        beds: {$eq: 4},
        bathrooms: {$gte:2}
    },
    {_id:0, name: 1, price: 1 ,beds:1, bathrooms:1}
)
```

- Aunque estamos de viaje no queremos estar desconectados, así que necesitamos que el alojamiento también tenga conexión wifi. A los requisitos anteriores, hay que añadir que el alojamiento tenga wifi.
  - Sólo muestra: nombre, precio, camas, baños y servicios (amenities).

```javascript
use ("airbnb")

db.listingsAndReviews.find( 
    {
        beds: {$eq: 4},
        bathrooms: {$gte:2},
        amenities: { $regex: ".*Wifi.*" }
    },
    {_id:0, name: 1, price: 1 ,beds:1, bathrooms:1, amenities:1}
)
```

- Y bueno, un amigo trae a su perro, así que tenemos que buscar alojamientos que permitan mascota (Pets allowed).
  - Sólo muestra: nombre, precio, camas, baños y servicios (amenities).

```javascript
use ("airbnb")

db.listingsAndReviews.find( 
    {
        beds: {$eq: 4},
        bathrooms: {$gte:2},
        amenities: { $all: ["Wifi", "Pets allowed"] }
    },
    {_id:0, name: 1, price: 1 ,beds:1, bathrooms:1, amenities:1}
)
```

- Estamos entre ir a Barcelona o a Portugal, los dos destinos nos valen. Pero queremos que el precio nos salga baratito (50 $), y que tenga buen rating de reviews (campo review_scores.review_scores_rating igual o superior a 88).
  - Sólo muestra: nombre, precio, camas, baños, rating, localidad y país.

```javascript
use ("airbnb")

db.listingsAndReviews.find( 
    {
        $or: [
            {"address.market" : {$eq: "Barcelona"}},
            {"address.market" : {$eq: "Portugal"}}
        ],
        price: {$eq:50},
        "review_scores.review_scores_rating" : {$gte:88},
    },
    {_id:0, name: 1, price: 1 ,beds: 1, bathrooms: 1,
         "review_scores.review_scores_rating": 1, 
         "address.market": 1, "address.country": 1 }
)
```

- También queremos que el huésped sea un superhost (host.host_is_superhost) y que no tengamos que pagar depósito de seguridad (security_deposit).
  - Sólo muestra: nombre, precio, camas, baños, rating, si el huésped es superhost, depósito de seguridad, localidad y país.

```javascript
use ("airbnb")

db.listingsAndReviews.find( 
    {
        $or: [
            {"address.market" : {$eq: "Barcelona"}},
            {"address.market" : {$eq: "Portugal"}}
        ],
        price: {$eq:50},
        "review_scores.review_scores_rating" : {$gte:88},
        "host.host_is_superhost": {$eq:true},
        "security_deposit": {$eq:0}
    },
    {_id:0, name: 1, price: 1 ,beds: 1, bathrooms: 1,
        "review_scores.review_scores_rating": 1, 
        "address.market": 1, "address.country": 1, 
        "host.host_is_superhost":1, "security_deposit":1 }
)
```

### Agregaciones
- Queremos mostrar los alojamientos que hay en España, con los siguientes campos:
  - Nombre.
  - Localidad (no queremos mostrar un objeto, sólo el string con la localidad).
  - Precio

```javascript
use ("airbnb")

    db.listingsAndReviews.aggregate([
        {
            $match:{
                "address.country": "Spain",
            }
        },
        {
            $project:{
                _id: 0,
                name: 1, 
                localidad: "$address.market",
                price: 1
            }
        },
    ])
```

- Queremos saber cuantos alojamientos hay disponibles por pais.

```javascript
use ("airbnb")

db.listingsAndReviews.aggregate([
    {
        $group:{
            _id: "$address.country",
            count: {$sum: 1}
        }
    },
    {
        $sort: {_id: 1}
    }
])
```

## Opcional
- Queremos saber el precio medio de alquiler de airbnb en España.

```javascript
use ("airbnb")

db.listingsAndReviews.aggregate([
    {
        $match:{
            "address.country": "Spain",
        }
    },
    {
        $group: {
            _id: 0,
            precioMedio: { $avg: '$price' }
      },
    }
])
```

- ¿Y si quisieramos hacer como el anterior, pero sacarlo por paises?

```javascript
use ("airbnb")

db.listingsAndReviews.aggregate([
    {
        $group: {
            _id: "$address.country",
            precioMedio: { $avg: '$price' }
      },
    }
])
```

- Repite los mismos pasos para calcular el precio medio de alquiler, pero agrupando también por numero de habitaciones.

```javascript
use ("airbnb")

db.listingsAndReviews.aggregate([
    {
        $group: {
            _id:  {
                Pais: "$address.country",
                Habitaciones: "$bedrooms"
            },
            precioMedio: { $avg: '$price' }
      },
    }
])
```

## Desafio
Queremos mostrar el top 5 de alojamientos más caros en España, con los siguentes campos:

- Nombre.
- Precio.
- Número de habitaciones
- Número de camas
- Número de baños
- Ciudad.
- Servicios, pero en vez de un array, un string con todos los servicios incluidos.

```javascript
use ("airbnb")

db.listingsAndReviews.aggregate([
    {
      $match: {
        "address.country": "Spain",
      },
    },
    {
      $project:
        {
          _id: 0,
          name: 1,
          price: 1,
          bedrooms: 1,
          beds: 1,
          bathrooms: 1,
          city: "$address.market",
          servicios: {
            $reduce: {
              input: "$amenities", initialValue: "",
              in: {
                $cond: [
                    {$eq: ["$$value", ""]},
                    {
                        $concat: [
                              "$$value",
                              "$$this",
                            ],
                    },
                    {
                        $concat: [
                              "$$value",
                              ", ",
                              "$$this",
                            ],
                    }
                ]
              },
            },
          },
        },
    },    
    {
        $sort: {
          price: -1,
        },
    },
    {
        $limit: 5,
    },
  ])
```