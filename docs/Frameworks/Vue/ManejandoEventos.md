# Manejando eventos con VueJs

## Pasar varios parámetros mediante emit

> [Ejemplo en jsFiddle](https://jsfiddle.net/50wL7mdz/30115/)

En el componente hijo, tendremos un evento que lanzará un _emit_ al padre, el cual tendrá como parámetros el evento que deberá escucharse desde el padre y el resto de parámetros, los que queramos pasarle.

```html
<component @emission="hearEmission">1. complete data, without extras</component>
<component @emission="hearEmission('extra', ...arguments)">2. with extras, incomplete data</component>
<component @emission="function (a, b, c) { hearEmission('extra', a, b, c) }">3. expected, overly explicit</component>
```

```js
Vue.component('component', {
  template: '<button @click="emitEvent"><slot></slot></button>',
  methods: {
    emitEvent: function() {
      this.$emit('emission', 1, 2, 3);
    }
  }
});

new Vue({
  el: '#app',
  data: {
    logs: [],
    expected: ['extra', 1, 2, 3]
  },
  methods: {
    hearEmission: function(extra, a, b, c) {
      this.logs.push([extra, a, b, c]);
      if (this.logs.length === 11) {
        this.logs.splice(0, 1);
      }
    }
  }
})
```

Expected output: ["extra",1,2,3]

1. [1,2,3,null]
2. ["extra",1,2,3]
3. ["extra",1,2,3]
