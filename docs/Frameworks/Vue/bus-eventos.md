# Crear un bus de eventos global con Vue.js

> [Artículo Alligator.io](https://alligator.io/vuejs/global-event-bus/)

## Inicializando

Lo primero que debe hacer es crear el bus de eventos y exportarlo en algún lugar para que otros módulos y componentes puedan usarlo.

*event-bus.js*

```js
import Vue from 'vue';

const EventBus = new Vue();
export default EventBus;
```

Todo lo que necesita hacer es importar la biblioteca Vue y exportar una instancia de la misma.
Lo que esencialmente obtienes es un componente que está completamente desacoplado del DOM o del resto de tu aplicación.

## Usando el bus de eventos

Ahora que ha creado el bus de eventos, todo lo que necesita hacer para usarlo es importarlo en sus componentes y llamar a los mismos métodos que usaría si estuviera pasando mensajes entre los componentes principales y secundarios.

### Envío de eventos

Digamos que tiene un componente muy emocionado que siente la necesidad de notificar a su aplicación completa de cuántas veces se ha hecho clic cada vez que alguien hace clic en él. Aquí se explica cómo implementarlo usando EventBus.emit (channel: string, payload1: any, ...) .

Estoy usando un único componente de archivo aquí, pero puede usar cualquier método de creación de componentes que desee.
PleaseClickMe.vue

```html
<template>
  <div class="pleeease-click-me" @click="emitGlobalClickEvent()"></div>
</template>

<script>
// Import the EventBus we just created.
import EventBus from './event-bus.js';

export default {
  data() {
    return {
      clickCount: 0
    }
  },

  methods: {
    emitGlobalClickEvent() {
      this.clickCount++;
      // Send the event on a channel (i-got-clicked) with a payload (the click count.)
      EventBus.$emit('i-got-clicked', this.clickCount);
    }
  }
}
</script>
```

### Recibir eventos

Ahora, cualquier otra parte de su aplicación lo suficientemente amable como para darle a PleaseClickMe.vue la atención que tan desesperadamente anhela puede importar EventBus y escuchar en el canal i-get- clicked usando EventBus. $ On (channel: string, callback (payload1, ...) ) .

*the-kindly-script.js*

```js
// Import the EventBus.
import { EventBus } from './event-bus.js';

// Listen for the i-got-clicked event and its payload.
EventBus.$on('i-got-clicked', clickCount => {
  console.log(`Oh, that's nice. It's gotten ${clickCount} clicks! :)`)
});
```

Si solo desea escuchar la primera emisión de un evento, puede usar EventBus. $ Una vez (channel: string, callback (payload1, ...)) .

### Eliminar los oyentes del evento

Una vez que una parte de su aplicación se cansa de escuchar la cantidad de veces que se ha hecho clic en PleaseClickMe.vue , pueden anular el registro de su controlador de ese canal.

```js
// Import the EventBus we just created.
import { EventBus } from './event-bus.js';

// The event handler function.
const clickHandler = function(clickCount) {
  console.log(`Oh, that's nice. It's gotten ${clickCount} clicks! :)`)
}

// Listen to the event.
EventBus.$on('i-got-clicked', clickHandler);

// Stop listening.
EventBus.$off('i-got-clicked', clickHandler);
```

También puede eliminar todos los oyentes de un evento en particular utilizando EventBus. $ Off ('i-got-clicked') sin argumento de devolución de llamada.
Si realmente necesita eliminar cada oyente de EventBus , independientemente del canal, puede llamar a EventBus. $ Off () sin argumentos.