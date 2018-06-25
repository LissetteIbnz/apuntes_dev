# Qué es Vuex

> http://op6gls4d1.bkt.clouddn.com/vuejs-vuex/docs/es/intro.html#

Vuex es una arquitectura para el desarrollo de aplicaciones en Vue.js con manejo de estado centralizado. Está inspirado en Flux y Redux, pero con conceptos simplificados e implementación diseñada específicamente para tomar ventaja del sistema reactivo de Vue.js.

## Por qué lo necesito

Si tu app es simple, quizás no necesites Vuex. No lo apliques prematuramente. Pero si estás construyendo un SPA de dimensiones medio-grandes, probablemente te hayas encontrado en situaciones que te han hecho pensar en cómo mejorar la forma de estructurar las cosas fuera de tu componente Vue. Aquí es donde Vuex entra en juego.

Cuando usamos Vue.js a solas, a menudo tendemos a almacenar el estado "dentro" de nuestros componentes. Lo que significa que *cada componente guarda una pieza del estado de nuestra aplicación*, y como resultado *el estado es dispersado por todos lados*. Sin embargo, a veces una parte del estado necesita ser *compartido por múltiples componentes*. Una práctica común es dejar que un componente "envíe" algún estado a otros componentes usando el sistema de eventos personalizado. El problema con este patrón es que el flujo de eventos dentro de grandes ramas de componentes puede volverse complejo rápidamente, y a menudo es difícil encontrar la causa cuando algo va mal.

Para tratar mejor los estados compartidos en grandes aplicaciones, necesitamos diferenciar entre estado local del componente y estado a nivel de aplicación. **El estado de aplicación no pertenece a un componente específico**, pero nuestros componentes pueden observarlo para actualizaciones de DOM reactivos. Centralizando su manejo en un único sitio, no necesitamos ir pasando eventos, porque *todo lo que afecta a más de un componente debería pertenecer sl store*. En adición esto nos permite grabar e inspeccionar cada mutación para un entendimiento más fácil de los cambios de estado, e incluso poner en práctica cosas avanzadas como la depuración con viaje en el tiempo.

Vuex también pone en vigor algunas opiniones sobre cómo dividir la lógica de la administración del estado en diferentes lugares, pero aun así permite suficiente flexibilidad para la estructura del código actual.

> [Ir a Tabla de contenidos](0-Vuex-TablaContenidos.md)

# Empezando

En el centro de toda aplicación Vuex está el almacén. Un almacén o "store" es básicamente un contenedor que guarda el estado de tu aplicación. Hay dos hechos que hacen al almacén Vuex diferente a un objeto global plano:

**Los almacenes Vuex son reactivos**. Cuando los componentes Vue recuperan un estado, el mismo reactiva y eficientemente será actualizado si el estado del almacén cambia.

*No puedes directamente mutar el estado del almacén*. La única manera de cambiar el estado del almacén es despachando explícitamente mutaciones. Esto hace que cada cambio de estado sea fácilmente rastreable, y habilita utilidades que nos ayudan a comprender mejor nuestras aplicaciones.

## El almacén más simple

La creación de un almacén Vuex es bastante sencillo, simplemente proporciona un objeto con el estado inicial, y algunas mutaciones:

```js
import Vuex from 'vuex'

const state = {
  count: 0
}

const mutations = {
  INCREMENT (state) {
    state.count++
  }
}

export default new Vuex.Store({
  state,
  mutations
})
```

Ahora, puedes acceder el objeto del estado como `store.state`, y ejecutar una mutación despachando su nombre:

```js
store.dispatch('INCREMENT')

console.log(store.state.count) // -> 1
```

Si prefieres despachar con el estilo de objetos, también puedes hacer lo siguiente:

```js
// mismo efecto al anterior
store.dispatch({
  type: 'INCREMENT'
})
```

De nuevo, la razón por la que estamos despachando una mutación en vez cambiar `store.state.count` directamente, es porque queremos explícitamente rastrearlo (hacerlo reactivo). Esta simple convención hace tu intención más explícita, para que así puedas razonar mejor sobre cambios de estado en tu app cuando vayas leyendo el código. Adicionalmente, eso nos da la oportunidad de implementar utilidades que puedan registrar cada mutación, tomar instantáneas de estado, o incluso realizar depuraciones con viaje en el tiempo con las [Vue DevTools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd?hl=es).

Ahora, esto es tan sólo el ejemplo más simple posible de lo que es el almacén. Pero Vuex es más que solamente el almacén. Seguidamente, discutiremos algunos conceptos básicos en profundidad: Estado, Mutaciones y Acciones.

> [Ir a Tabla de contenidos](0-Vuex-TablaContenidos.md)