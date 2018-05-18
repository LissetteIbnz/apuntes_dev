# Vuex patrón Store

En esta tercera entrega de introducción a Vuex vamos a ver cómo mejorar la solucíón de compartir datos entre componentes. Para mejorar la solución del capítulo anterior podemos usar un objeto (o un componente) adicional que sirva de almacén de los datos:

```js
var store = {
  numeroClicks: 0
};

```

Este objeto lo podemos compartir en todos los componentes que queramos y así todos compartirán la variable numeroClicks.

Para tener acceso a este objeto desde un componente debemos añadirlo en la sección de data:

```js
data: function() {
  return {
    store: store
  }
},
···

```

De esta forma el componente tendrá acceso al objeto store a través de this.store. Por ejemplo el componente boton quedaría:

```js
var boton = {
  template: '<button @click="incrementar">{{store.numeroClicks}}</button>',
  data: function() {
    return {
      store: store
    }
  },
  methods: {
    incrementar: function() {
      this.store.numeroClicks++;
    }
  }
};

```

De esta forma, al incrementar el valor de this.store.numeroClicks estaremos modificando el objeto store.

Del mismo modo el componente contador tendría acceso al valor compartido de numeroClicks:

```js
var contador = {
  template: '<div>Clicks: {{store.numeroClicks}}</div>',
  data: function() {
    return {
    store: store
      }
  }
};

```

Si te fijas, store forma parte de los datos del componente. Así que cuando store se cambie de valor los componentes que lo usen se actualizarán automáticamente.

Como tenemos un almacén centralizado para numeroClicks podemos añadir más componentes y este valor será accesible para todos. Por ejemplo, si añadimos otro botón éste tendría también acceso al número de clicks:

```html
<div id="app">
  <contador></contador>
  <boton></boton>
  <boton></boton>
</div>

```

## El ejemplo completo

Puedes ver el ejemplo completo aquí:

http://jsbin.com/vunohe/2/edit?html,js,console,output

## Las desventajas de este método

Como siempre, los ejemplos son muy bonitos y sencillos y todo funciona a la perfección. En la vida real seguramente tendremos más componentes y cada uno de ellos puede modificar el valor de store libremente.

Cuando esto sucede, si surge algún problema encontrar el fallo puede ser complicado; el valor de numeroClicks puede cambiar en múltiples lugares y hacer el seguimiento puede ser un dolor de cabeza. Imagina intentar localizar el error cuando tienes, por ejemplo, diez componentes con acceso a store.

Podríamos hacer que cada componente que quiera modificar algo guarde información en un registro (o que la muestre en la consola) pero nada nos garantiza que todos los componentes cumplan con ésto.

Otra desventaja es que cualquier validación que queramos hacer (por ejemplo limitar el número de clicks) tendremos que añadirla en todos y cada uno de los componentes que use el objeto store.

## Añadiendo unas mejoras: el patrón store en Vue

Para evitar que cualquier componente modifique a su antojo los datos compartidos podemos usar el patrón de diseño store:

```js
var store = {
  numeroClicks: 0,
  incrementar: function() {
    this.numeroClicks++;
  }
};

```

De esta forma todos los componentes que quieran modificar el valor de numeroClicks deberían pasar por store.

La ventaja es que toda la lógica de la aplicación relacionada con numeroClicks queda centralizada en un único sitio.

Si un componente quiere incrementar el número de clicks debería hacerlo a través de store:

```js
methods: {
  incrementar: function() {
    store.incrementar();
  }
}

```

El código completo de este ejemplo puedes verlo aquí:

http://jsbin.com/xopoxow/edit?html,js,console,output

## Seguimos con problemas

He comentado al principio de esta sección que si se quiere modificar el número de click se debería hacer a través de store. Sin embargo, nada nos impide seguir haciéndolo como antes; el método incrementar de antes sigue funcionando sin problemas:

```js
incrementar: function() {
  this.store.numeroClicks++;
}

```

Como, en el fondo, todos somos muy vagos no tardará mucho en llegar alguien que modifique directamente this.store.numeroClicks. ¿Cómo podemos evitarlo? ¿Con un cartelito en la entrada de la oficina pidiendo que no se haga? ¿Dando una paliza al que cometa semejante sacrilegio?

Por suerte hay otra opción que es Vuex. Con Vuex tenemos un almacén común para los datos pero con super poderes. En la próxima entrega vamos a ver cómo modificar nuestro ejemplo con esta nueva herramienta.