# Clean-Architeture

## Que es arquitectura limpia?
Es un modelo de arquitectura de software, su principal ventaja es que divide completamente el bussines logic de la aplicacion.

La clasficiacion definida es la siguiente:

### Entidades.
Son los objectos del negocio, por ejemplo: En la aplicacion uber las entidades serian cliente, conductor, viaje, etc...

### Infraestructura
A grandes rasgos, es el origen de los datos; base de datos, peticion http, cualquiera que sea.

### Casos de uso
Son las reglas del negocio, por ejemplo:
1. En un login las reglas serian: contraseña debe tener al menos 4 caracteres, alphanumericos.
2. En un punto de ventas: Cada factura tiene que tener un cliente asociado.
3. En un juego rpg: Cuando la vida del jugador este llena y llegue a cero, el jugador tendra 1 vida extra.



Esta division se puede ver en la siguiente imagen:
<img src="https://xurxodev.com/content/images/2016/07/CleanArchitecture-8b00a9d7e2543fa9ca76b81b05066629.jpg" width="500px"/>

Siendo la parte **amarilla las entidades**, la parte **roja los casos de uso** y las partes **verde y azul la infraestructura**

## Implementacion en typescript

Lo que vamos a hacer es un simple ejemplo con **item** como **entidad** y como regla cada item debe tener **fecha de caducidad**.

### Entidad

Item.ts
```javascript
export class Item {
  ID: string;
  SKU: string;
  Stock: number;
  ExpirationDate: 

  constructor(ID: string, Name: string) {
    this.ID = ID;
    this.Name = Name;
  }
}
```
