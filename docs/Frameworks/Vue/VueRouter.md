# VUE-ROUTER

Usar $route en tu componente genera un acoplamiento estrecho con la ruta, lo cual limita la flexibilidad del componente dado que solo puede utilizarse en ciertas URL.

Para desacoplar el componente del enrutador utiliza props:

Acoplado a $route

const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User }
  ]
})
Desacoplado con props

const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User, props: true },

    // utilizando vistas con nombre, tienes que definir la opción prop para cada una de ellas:
    {
      path: '/user/:id',
      components: { default: User, sidebar: Sidebar },
      props: { default: true, sidebar: false }
    }
  ]
})
Esto te permite utilizar el componente en cualquier lugar, lo cual hace al mismo reutilizable y más sencillo de testear.

Modo boolean
Cuando props tiene asignado el valor true, route.params serán asignados como las props del componente.

Modo objeto
Cuando props es un objeto, este será asignado tal cual como las props del componente. Útil para cuando las props son estáticas.

const router = new VueRouter({
  routes: [
    { path: '/promotion/from-newsletter', component: Promotion, props: { newsletterPopup: false } }
  ]
})
Modo función
Puedes crear una función que retorne props. Esto te permite convertir los parámetros a otro tipo, combinar valores estáticos con valores basados en rutas, etc.

const router = new VueRouter({
  routes: [
    { path: '/search', component: SearchUser, props: (route) => ({ query: route.query.q }) }
  ]
})


Ejemplo
```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Hello from './Hello.vue'

Vue.use(VueRouter)

function dynamicPropsFn (route) {
  const now = new Date()
  return {
    name: (now.getFullYear() + parseInt(route.params.years)) + '!'
  }
}

const router = new VueRouter({
  mode: 'history',
  base: __dirname,
  routes: [
    { path: '/', component: Hello }, // No props, no nothing
    { path: '/hello/:name', component: Hello, props: true }, // Pass route.params to props
    { path: '/static', component: Hello, props: { name: 'world' }}, // static values
    { path: '/dynamic/:years', component: Hello, props: dynamicPropsFn }, // custom logic for mapping between route and props
    { path: '/attrs', component: Hello, props: { name: 'attrs' }}
  ]
})

new Vue({
  router,
  template: `
    <div id="app">
      <h1>Route props</h1>
      <ul>
        <li><router-link to="/">/</router-link></li>
        <li><router-link to="/hello/you">/hello/you</router-link></li>
        <li><router-link to="/static">/static</router-link></li>
        <li><router-link to="/dynamic/1">/dynamic/1</router-link></li>
        <li><router-link to="/attrs">/attrs</router-link></li>
      </ul>
      <router-view class="view" foo="123"></router-view>
    </div>
  `
}).$mount('#app')
```

https://github.com/vuejs/vue-router/tree/dev/examples