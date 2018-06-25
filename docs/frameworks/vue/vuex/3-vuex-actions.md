# Actions

Actions are similar to mutations, the differences being that:

Instead of mutating the state, actions commit mutations.
Actions can contain arbitrary asynchronous operations.
Let's register a simple action:

const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})

Action handlers receive a context object which exposes the same set of methods/properties on the store instance, so you can call context.commit to commit a mutation, or access the state and getters via context.state and context.getters. We will see why this context object is not the store instance itself when we introduce Modules later.

In practice, we often use ES2015 argument destructuring to simplify the code a bit (especially when we need to call commit multiple times):

actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
# Dispatching Actions
Actions are triggered with the store.dispatch method:

store.dispatch('increment')
This may look dumb at first sight: if we want to increment the count, why don't we just call store.commit('increment') directly? Remember that mutations have to be synchronous? Actions don't. We can perform asynchronous operations inside an action:

actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
Actions support the same payload format and object-style dispatch:

// dispatch with a payload
store.dispatch('incrementAsync', {
  amount: 10
})

// dispatch with an object
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
A more practical example of real-world actions would be an action to checkout a shopping cart, which involves calling an async API and committing multiple mutations:

```js
actions: {
  checkout ({ commit, state }, products) {
    // save the items currently in the cart
    const savedCartItems = [...state.cart.added]
    // send out checkout request, and optimistically
    // clear the cart
    commit(types.CHECKOUT_REQUEST)
    // the shop API accepts a success callback and a failure callback
    shop.buyProducts(
      products,
      // handle success
      () => commit(types.CHECKOUT_SUCCESS),
      // handle failure
      () => commit(types.CHECKOUT_FAILURE, savedCartItems)
    )
  }
}
```

Note we are performing a flow of asynchronous operations, and recording the side effects (state mutations) of the action by committing them.

## Dispatching de Acciones en Componentes

Puedes enviar acciones en los componentes mediante `this.$store.dispatch('xxx')`, i utilizar el asistente `mapActions` que asigna métodos de componentes a las llamadas `store.dispatch` (requiere inyección de `store` en el raíz):

```js
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // map `this.increment()` a `this.$store.dispatch('increment')`

      // `mapActions` también admite payloads:
      'incrementBy' // map `this.incrementBy(amount)` a `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // map `this.add()` a `this.$store.dispatch('increment')`
    })
  }
}
```

## Componiendo Acciones

Las acciones a menudo son asincrónicas, entonces, ¿cómo sabemos cuándo se realiza una acción? Y más importante aún, ¿cómo podemos componer múltiples acciones juntas para manejar flujos asíncronos más complejos?

Lo primero que debe saber es que `store.dispatch` puede manejar una `Promise` devuelta por el controlador de acción desencadenada (triggered action handler) y también devolver una `Promise`:

```js
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}
```

Ahora podemos hacer:

```js
store.dispatch('actionA').then(() => {
  // ...
})
```

Y también en otra acción:

```js
actions: {
  // ...
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
```

Finalmente, si hacemos uso de `async / await` , podemos componer nuestras acciones de esta manera:

```js
// assuming `getData()` and `getOtherData()` return Promises

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // wait for `actionA` to finish
    commit('gotOtherData', await getOtherData())
  }
}
```

Es posible para un `store.dispatch` activar controladores de acción múltiple en diferentes módulos. En tal caso, el valor devuelto será una Promesa que se resuelve cuando se hayan resuelto todos los controladores desencadenados.