# Consumo API REST Vue.js con Axios

Axios es una gran biblioteca de cliente http. Utiliza promesas por defecto y se ejecuta tanto en el cliente como en el servidor (lo que hace que sea apropiado para obtener datos durante la representación del lado del servidor). También es bastante fácil de usar con Vue. Debido a que usa promesas, puede combinarlo con async / await para obtener una API increíblemente concisa y fácil de usar.

## Instalación

### Yarn

```cmd
$ yarn add axios
```

### NPM

```cmd
$ npm install axios --save
```

## Poblando datos con una solicitud GET

Si bien, en general, se trata de una práctica deficiente, puede usar Axios directamente en sus componentes para obtener datos de un método, un enlace de ciclo de vida o cualquier momento.

_ExampleComponent.vue_

```html
<template>
  <ul v-if="posts && posts.length">
    <li v-for="post of posts">
      <p><strong>{{post.title}}</strong></p>
      <p>{{post.body}}</p>
    </li>
  </ul>

  <ul v-if="errors && errors.length">
    <li v-for="error of errors">
      {{error.message}}
    </li>
  </ul>
</template>
```

```js
<script>
import axios from 'axios';

export default {
  data() {
    return {
      posts: [],
      errors: []
    }
  },

  // Fetches posts when the component is created.
  created() {
    axios.get(`http://jsonplaceholder.typicode.com/posts`)
    .then(response => {
      // JSON responses are automatically parsed.
      this.posts = response.data
    })
    .catch(e => {
      this.errors.push(e)
    })

    // async / await version (created() becomes async created())
    //
    // try {
    //   const response = await axios.get(`http://jsonplaceholder.typicode.com/posts`)
    //   this.posts = response.data
    // } catch (e) {
    //   this.errors.push(e)
    // }
  }
}
</script>
```
## Agregar datos con una solicitud POST

Podemos enviar solicitudes POST, PUT, PATCH y DELETE con la misma facilidad.

(Tenga en cuenta que es una idea terrible enviar solicitudes en cada evento de entrada, al menos hacer algo de estrangulamiento o antirrebote ).

ExampleComponent.vue

<template>
  <input type="text" v-model="postBody" @change="postPost()"/>
  <ul v-if="errors && errors.length">
    <li v-for="error of errors">
      {{error.message}}
    </li>
  </ul>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      postBody: '',
      errors: []
    }
  },

  // Pushes posts to the server when called.
  postPost() {
    axios.post(`http://jsonplaceholder.typicode.com/posts`, {
      body: this.postBody
    })
    .then(response => {})
    .catch(e => {
      this.errors.push(e)
    })

    // async / await version (postPost() becomes async postPost())
    //
    // try {
    //   await axios.post(`http://jsonplaceholder.typicode.com/posts`, {
    //     body: this.postBody
    //   })
    // } catch (e) {
    //   this.errors.push(e)
    // }
  }
}
</script>
Una instancia de base común
Una capacidad que con frecuencia se pasa por alto pero que es muy útil, que Axios proporciona es la capacidad de crear una instancia base que le permite compartir una URL base común y la configuración en todas las llamadas a la instancia. Esto es útil si todas sus llamadas son a un servidor en particular, o si necesita compartir encabezados, como un encabezado de autorización.

http-common.js

import axios from 'axios';

export const HTTP = axios.create({
  baseURL: `http://jsonplaceholder.typicode.com/`,
  headers: {
    Authorization: 'Bearer {token}'
  }
})
Ahora puede usar HTTP como tal,

<script>
import {HTTP} from './http-common';

export default {
  data() {
    return {
      posts: [],
      errors: []
    }
  },

  created() {
    HTTP.get(`posts`)
    .then(response => {
      this.posts = response.data
    })
    .catch(e => {
      this.errors.push(e)
    })
  }
}
</script>
Referencia
Eso es solo arañar la superficie de lo que Axios puede hacer. Consulte el repositorio de GitHub para obtener más información y documentación.