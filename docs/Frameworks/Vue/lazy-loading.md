# Lazy Loading in Vue using Webpack's Code Splitting

## Lazy load in Vue components

Es tan simple como usar una función `import` cuando se registra un componente:

```js
Vue.component('AsyncCmp', () => import('./AsyncCmp'))
```

Y usando registro local:

```js
new Vue({
  // ...
  components: {
    'AsyncCmp': () => import('./AsyncCmp')
  }
})
```

Al envolver la función de importación en una función flecha, Vue la ejecutará sólo cuando se solicite, cargando el módulo en ese momento.

Si en la importación del componente usa una exportación con nombre, puede usar la desestructuración del objeto en la Promesa devuelta. Por ejemplo, para el componente UiAlert de KeenUI:

```js
components: {
  UiAlert: () => import('keen-ui').then(({ UiAlert }) => UiAlert)
}
```

Otra forma de hacerlo:

```js
const InputComponent = () => import('./ui/Input.vue').then(m => m.default);

@Component({
  components: {
    InputComponent,
  }
})
```