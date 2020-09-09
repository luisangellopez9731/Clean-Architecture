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

Lo que vamos a hacer es un simple ejemplo con **item** como **entidad** y como regla cada item debe tener **como minimo 3 meses para expirar**.

### Entidad

Item.ts
```javascript
export class Item {
  ID: string;
  SKU: string;
  Stock: number;
  ExpirationDate: Date;

  constructor(ID: string, SKU: string, Stock: number, ExpirationDate: Date) {
    this.ID = ID;
    this.SKU = SKU;
    this.Stock = Stock;
    this.ExpirationDate = ExpirationDate;
  }
}
```

ItemRepository.js
```javascript
import {Item} from './Item'
export interface ItemRepository {
  addItem(): Promise<Item>;
}
```

Hasta aqui algunas cosas que señalar:
1. Item es la entidad principla o base, sus propiedades estan definidas por el negocio.
2. ItemRepository es una interfaz para ser mas eficiente ante los cambios, mas adelante lo vas a entender.


### Infraestructura

Dentro de la infraestructura va nuestro metodo para añadir ese item.

ItemRepositoryImpl.ts
```javascript
import {Item} from './entities/Item'
import {ItemRepository} from './entities/ItemRepository';

export class ItemRepositoryImpl implements ItemRepository {
  async addItem(item: Item): Promise<Item> {
    // Aqui va tu logica para agregar el item, si es una base de datos o una peticion http, o cualquier otra cosa.
    // A modo de retornar lo que pide la interfaz, retornamos el item.
    return item;
  }
}
```

Bien, aqui podemos ver que tenemos una clase **ItemRepositoryImpl** usando la entidad **ItemRepository**, esto nos ayuda para implementar cambios o alternativas, por ejemplo:
El proyecto empieza implementando mysql como base de datos, entonces podemos crear la clase **ItemRepositoryImplMYSQL** o como es nuestra clase inicial podemos simplemente llamarla **ItemRepositoryImplBase** o algo asi, el caso es que si en el futuro se decide tambien implementar **SQLSERVER**, solo tenemos que agregar una nueva clase **ItemRepositoryImplSQLSERVER** que implemente la misma interfaz **ItemRepository**.

La interfaz **ItemRepository** nos permite definir lo que tiene que hacer nuestra implementacion y lo que tiene que devolver, para implementar esta definicion tenemos que crear una clase que implemente esta interfaz, de esto modo a lo que definimos lo podemos dar los procesos que ejecuta.

### Uso de caso.

Recordar que el uso de caso son las reglas de negocios, las limitaciones y caracteristicas de cada entidad.

En nuestro caso aqui definimos la caracteristica de que **como minimo 3 meses para expirar**:

ItemServiceImpl.ts
```javascript
import {Item} from './entities/Item'
import {ItemRepository} from './entities/ItemRepository'
import {ItemRepositoryImpl} from './infrastructure/ItemRepositoryImpl'

class UseCases {
  static ExpirationDateIsValid(expirationDate: date): boolean {
    const today = Date.now();
    cosnt difference = (expirationDate.getFullYear()*12 + expirationDate.getMonth()) - (today.getFullYear()*12 + today.getMonth());
    if(difference >= 3) {
      return true;
    }else {
      return false;
    }
  }
}

export class ItemServiceImpl implements ItemRepository {
  ItemRepo: ItemRepository;
  constructor() {
    this.ItemRepo = new ItemRepositoryImpl();
  }
  
  async addItem(item: Item): Promise<Item> {
    // Aqui va nuestra regla de negocio, si es valida creamos el item, si no retornamos un error.
    if(UseCases.ExpirationDateIsValid(item.ExpirationDate)) {
      return ItemRepo.addItem(item);
    }else {
      throw new Error('La fecha de caducidad debe de ser al menos de 3 meses');
    }
    
  }
}
```

De esta forma tenemos desaclopado la implementacion de la base de datos y las reglas del negocio.

Esta arquitectura nos permite simplemente desde nuestra app llamar al metodo **addItem** de **ItemServiceImpl** aplicando las reglas del negocio que fuerno definidas.

### Arquitectura de los archivos:
```
-core
  -entities
    Item.ts
    ItemRepository.ts
  -infraestructure
    ItemRepositoryImpl.ts
  -usecases
    ItemServiceImpl.ts
```

## Ventajas
1. Tenemos desaclopado el **presentador**, las **reglas del negocio** y la **infraestructura**, esto significa que las reglas del negocio pueden cambiar sin afectar la infraestructura ni al presentador y asi mismo para las demas.
2. Podemos realizar pruebas y pruebas unitarias de forma sencilla, por ejemplo: A las reglas del negocio sin necesidad de guardar en la base de datos, a la base de datos sin necesidad de validar las reglas del negocio y al presentador sin necesidad de validar las reglas del negocio, ni de guardar en la base de datos o una de las dos.

## Consideraciones
1. Tener en cuenta que no se habla de cambios a las **entidades** por que este es el modulo del negocio que menos deberia cambiar, si se efectuan cambios en las entidades, entonces tenemos que hacer cambios a nuestra **infraestructura**, las **reglas del negocio** y hasta al **presentador**.
2. Esta es una implementacion de **arquitectura limpia** un poco directa, por que la **implementacion de la infraestructura** esta acoplada a las **reglas del negocio**, de este modo te ahorras instanciar la implementacion de la infraestructura y luego las reglas del negocio, ejemplo:
```javascript
const ir = new ItemRepositoryImpl();
const itemService = new ItemServiceImpl(ir);
```
3. Si quieres aportar algo, solo has **fork**, validare los cambios y los publicare aqui mismo con una mencion a tu usurario de github.
