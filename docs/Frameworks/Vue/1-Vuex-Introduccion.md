# VUEX

Cuando creamos una aplicación con Vue nos encontramos con varios componentes que tienen que comunicarse entre ellos. Por ejemplo, el habitual ejemplo del contador y el botón.

En esta serie voy a explicar qué es Vuex, cómo usarlo y por qué usarlo. En esta primera entrega vamos a ver el problema de la comunicación entre componentes y una posible solución. En la siguiente entrega veremos el patrón store, luego le añadiremos unas mejoras y, por fin, mostraré la solución con Vuex.

## El problema

Imaginemos una sencilla aplicación que tiene dos componentes independientes: un botón y un contador. Lo que queremos es que cada vez que se haga click en el botón se incremente el valor del contador.

El código podría ser algo así:

```js
var contador = {
  template: '<div>Clicks: {{numeroClicks}}</div>',
  data: function() {
    return {
      numeroClicks: 0
    }
  }
};

var boton = {
  template: '<button @click="incrementar">{{numeroClicks}}</button>',
  data: function() {
    return {
    numeroClicks: 0
    }
  },
  methods: {
    incrementar: function() {
      this.numeroClicks++;
    }
  }
};

new Vue({
  el: '#app',
  components: { contador: contador, boton: boton }
});

```

Puedes verlo en funcionamiento aquí:

http://jsbin.com/dagitat/edit?html,js,console,output
Para que se pueda entender mejor el problema he añadido un contador también al botón. Puedes ver que este código no hace lo que buscamos; cada uno de los componentes va por independiente y no se comunican entre ellos.

## ¿Cómo podemos comunicar dos componentes Vue?

Una posible solución podría ser con eventos usando al objeto Vue principal como intermediario. Otra posible solución sería usar un bus, que veremos en la siguiente entrega.

Si usásemos al padre como intermediario podríamos tener algo así; por un lado tendríamos el componente contador que cogiera el valor numeroClicks como una propiedad:

```js
var contador = {
  template: '<div>Clicks: {{numeroClicks}}</div>',
  props: ['numero-clicks']
};

```

Y para mostrar este componente usaríamos la etiqueta así:

```js
<contador v-bind:numero-clicks="numeroClicks"></contador>

```

numeroClicks debe estar presente en el objeto Vue raíz:

```js
new Vue({
  el: '#app',
  components: { contador: contador, boton: boton },
  data: {
    numeroClicks: 0
  }
});

```

Y para incrementar su valor podemos añadir un nuevo método (botonPulsado):

```js
new Vue({
  el: '#app',
  components: { contador: contador, boton: boton },
  data: {
    numeroClicks: 0
  },
  methods: {
    botonPulsado: function() {
      this.numeroClicks++;
    }
  }
});

```

Ahora bien ¿cómo hacemos que al pulsar el botón se llame al método botonPulsado? Pues para eso podemos usar eventos. Vamos a crear un nuevo evento llamado 'boton-pulsado':

```js
<boton v-on:boton-pulsado="botonPulsado"></boton>

```

Cuando este evento tenga lugar se va a llamar al método botonPulsado del componente Vue raíz. Por último, para lanzar este evento basta con añadir modificar el método incrementar del componente boton:

```js
incrementar: function() {
  this.numeroClicks++;
  this.$emit('boton-pulsado');
}

```

Aquí:

```js
this.$emit('boton-pulsado');

```

va a lanzar el evento boton-pulsado, que está asociado al método botonPulsado en el objeto Vue padre. Al ejecutarse este método tendremos que se incrementa el valor de la variable numeroClicks que, a su vez, modifica el valor de la propiedad numeroClicks del objeto contador.

Resultado: cuando hagamos click en el botón se actualizarán ambos contadores y estarán sincronizados.

El archivo HTML quedaría así:

```js
<div id="app">
  <contador v-bind:numero-clicks="numeroClicks"></contador>
  <boton v-on:boton-pulsado="botonPulsado"></boton>
</div>

```

Y el código completo quedaría así:

```js
var contador = {
  template: '<div>Clicks: {{numeroClicks}}</div>',
  props: ['numero-clicks']
};

var boton = {
  template: '<button @click="incrementar">{{numeroClicks}}</button>',
  data: function() {
    return {
    numeroClicks: 0
    }
  },
  methods: {
    incrementar: function() {
      this.numeroClicks++;
      this.$emit('boton-pulsado');
    }
  }
};

new Vue({
  el: '#app',
  components: { contador: contador, boton: boton },
  data: {
    numeroClicks: 0
  },
  methods: {
    botonPulsado: function() {
      this.numeroClicks++;
    }
  }
});

```

Puedes ver el ejemplo en funcionamiento aquí:

http://jsbin.com/voqige/edit?html,js,console,output

## Desventajas

Todo lo que hemos hecho aquí es perfectamente correcto. El problema surge cuando nuestra aplicación empieza a crecer:

Por un lado tenemos que asociar cada evento con el objeto principal.
Si tenemos varios niveles de elementos hijos puede empezar a ser confuso qué componente es el que provoca el evento.
Si queremos comunicar componentes que no “cuelgan” directamente del objeto raíz la comunicación se complica un montón.
Una posible solución es usar un evento intermedio para que sirva de canal de comunicación. En la próxima entrega veremos cómo hacerlo y las desventajas que tiene.