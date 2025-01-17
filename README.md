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

- Lista los 10 primeros alojamientos de España:
  - Ordenados por precio de forma ascendente.
  - Sólo muestra: nombre, precio, camas y la localidad (address.market).


use ("airbnb")

db.listingsAndReviews.find( 
    {"address.country": {$eq: "Spain"}},
    {_id:0, name: 1, price: 1 ,
        beds:1, "address.market":1}
    ).limit(10).sort({price:1})

### Filtrando
- Queremos viajar cómodos, somos 4 personas y queremos:
  - 4 camas.
  - Dos cuartos de baño o más.
  - Sólo muestra: nombre, precio, camas y baños.

`Pega aquí tu consulta`

- Aunque estamos de viaje no queremos estar desconectados, así que necesitamos que el alojamiento también tenga conexión wifi. A los requisitos anteriores, hay que añadir que el alojamiento tenga wifi.
  - Sólo muestra: nombre, precio, camas, baños y servicios (amenities).

`Pega aquí tu consulta`

- Y bueno, un amigo trae a su perro, así que tenemos que buscar alojamientos que permitan mascota (Pets allowed).
  - Sólo muestra: nombre, precio, camas, baños y servicios (amenities).

`Pega aquí tu consulta`

- Estamos entre ir a Barcelona o a Portugal, los dos destinos nos valen. Pero queremos que el precio nos salga baratito (50 $), y que tenga buen rating de reviews (campo review_scores.review_scores_rating igual o superior a 88).
  - Sólo muestra: nombre, precio, camas, baños, rating, localidad y país.

`Pega aquí tu consulta`

- También queremos que el huésped sea un superhost (host.host_is_superhost) y que no tengamos que pagar depósito de seguridad (security_deposit).
  - Sólo muestra: nombre, precio, camas, baños, rating, si el huésped es superhost, depósito de seguridad, localidad y país.

`Pega aquí tu consulta`

### Agregaciones
- Queremos mostrar los alojamientos que hay en España, con los siguientes campos:
  - Nombre.
  - Localidad (no queremos mostrar un objeto, sólo el string con la localidad).
  - Precio

`Pega aquí tu consulta`

- Queremos saber cuantos alojamientos hay disponibles por pais.

`Pega aquí tu consulta`

## Opcional
- Queremos saber el precio medio de alquiler de airbnb en España.

`Pega aquí tu consulta`

- ¿Y si quisieramos hacer como el anterior, pero sacarlo por paises?

`Pega aquí tu consulta`

- Repite los mismos pasos para calcular el precio medio de alquiler, pero agrupando también por numero de habitaciones.

`Pega aquí tu consulta`

## Desafio
Queremos mostrar el top 5 de alojamientos más caros en España, con los siguentes campos:

- Nombre.
- Precio.
- Número de habitaciones
- Número de camas
- Número de baños
- Ciudad.
- Servicios, pero en vez de un array, un string con todos los servicios incluidos.

`Pega aquí tu consulta`