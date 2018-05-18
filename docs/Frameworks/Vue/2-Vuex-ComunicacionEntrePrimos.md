# BUS

En la entrega anterior sobre Vuex vimos una posible solución al problema de compartir datos entre componentes. En dicha solución la comunicación se hacía entre padres e hijos.
Cuando tenemos varios niveles de componentes (un componente que es hijo de otro componente) la cosa se complica y el código se puede volver tremendamente confuso.

Otra posible solución es comunicar directamente dos componentes sin necesidad de pasar por el componente raíz usando un tercer componente, algo así como un bus de eventos.

## Usar un tercer componente para comunicar componentes

El código de este ejemplo puedes encontrarlo en: http://jsbin.com/potufe/edit?html,js,console,output.

Para la comunicación entre componentes que no son padre-hijo podemos usar un tercer componente que haga de intermediario:

```js
var bus = new Vue();

```

## Lanzar el evento

Cuando el usuario haga click en el botón de incremento se ejecutará el método incrementar:

```js
incrementar: function() {
  this.numeroClicks++;
  bus.$emit('incrementar-clicks', 1);
}

```

Este método lanzará el evento 'incrementar-clicks'.

## El componente botón

En el componente botón hemos cambiado el evento que se lanza y usamos el bus intermedio que hemos creado:

```js
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
      bus.$emit('incrementar-clicks', 1);
    }
  }
};

```

## Detectar el evento

El siguiente paso es pedirle al componente contador que empiece a escuchar a este tipo de evento. Esto lo hacemos en el método created:

```js
created: function() {
  bus.$on('incrementar-clicks', function (id) {
    this.numeroClicks++;
  }.bind(this))
}

```

En created le pedimos al componente que escuche los eventos 'incrementar-clicks' que se produzcan a través del componente bus. Cuando ese evento se produzca se incrementará la propiadad numeroClicks.

## El componente contador

El componente contador quedaría así:

```js
var contador = {
  template: '<div>Clicks: {{numeroClicks}}</div>',
  data: function() {
    return {
     numeroClicks: 0
      }
  },
  created: function() {
    bus.$on('incrementar-clicks', function (id) {
      this.numeroClicks++;
    }.bind(this))
  }
};

```

Como se puede ver, ha desaparecido la propiedad 'numeroClicks' y ahora es parte de los datos del componente.

## El ámbito de this

Ten en cuenta que es necesario añadir .bind(this) al final para que this se refiera al componente contador. Dependiendo del contexto this se puede referir a una cosa o a otra; prueba a quitar el .bind(this) para ver el problema.

También podríamos hacer:

```js
created: function() {
  var thisObj = this;
  bus.$on('incrementar-clicks', function (id) {
    thisObj.numeroClicks++;
  })
}

```

## El componente raíz y las propiedades son innecesarias

En la entrega anterior usábamos el componente padre para comunicar los dos componentes. Necesitábamos también usar props y usar al padre para modificar el valor de numeroClicks. Ahora el raíz queda excluido de la comunicación y se simplifica:

```js
new Vue({
  el: '#app',
  components: { contador: contador, boton: boton }
});

```

El fichero HTML también se simplifica:

```html
<div id="app">
  <contador></contador>
  <boton></boton>
</div>

```

## Ventajas de esta solución

Una de las ventajas de esta solución frente a la anterior es que si hay varios niveles (componentes que no cuelgan directamente del raíz) la comunicación es más sencilla. El código queda más limpio y claro al no tener que ir pasando propiedades de padres a hijos.

## Desventajas de esta solución

Cuando hay varios componentes que tienen que recibir el evento la cosa se empieza a complicar.
No tenemos un sitio centralizado donde se guardan los datos. Cada componente tiene su propio numeroClicks y pueden empezar a surgir disparidades.
Si hay varios componentes que quieran modificar el valor de numeroClick cada uno estará usando su propio código y tenemos código duplicado.
En la próxima entrega vamos a ver cómo mejorar esto usando el llamado patrón store. Esa tampoco es la mejor solución pero ya empieza a acercarse.

**Extraído de [Gulvi](https://gulvi.com/serie/vuejs-vuex/capitulo/comunicacion-componentes-vuejs)**