# Registrar componentes por uso

## Forma usual (no eficiente)

1. Importar el componente hijo
2. Registrarlo en el objeto `components` del componente padre.
3. Añadir el componente a la función plantilla o renderizado.

```js
<template>
  <some-random-thing />
</template>
<script>
import SomeRandomThing from './components/SomeRandomThing'
export default {
  components: {
    SomeRandomThing,
  },
}
</script>
```

Imagina un componente `Header` que contiene la información y el diseño del encabezado de nuestra aplicación. Ahora que esta información puede estar relacionada con el usuario o con la empresa dependiendo de algún valor.
Tenemos los componentes `UserInfo` y `CompanyInfo`. Queremos mostrar uno u otro dependiendo de ese valor de configuración.

## Versión 1: Forma usual

```js

<template>
  <div>
    <company-info v-if="isCompany" />
    <user-info v-else />
    ...
  </div>
</template>

<script>
import UserInfo from './components/UserInfo'
import CompanyInfo from './components/CompanyInfo'
export default {
  components: {
    UserInfo,
    CompanyInfo,
  },
  props: {
    isCompany: { type: Boolean, default: false },
  },
}
</script>
```

## Versión 2: <component /> al rescate

Hay un componente integrado en `Vue` llamado [Componente](https://vuejs.org/v2/guide/components.html#Dynamic-Components). Este componente <component /> actúa como un marcador de posición para otro componente y acepta una prop especial `:is` con el nombre del componente que debe representar.

```js
<template>
  <div>
    <component :is="componentName" />
  </div>
</template>

<script>
import UserInfo from './components/UserInfo'
import CompanyInfo from './components/CompanyInfo'
export default {
  components: {
    UserInfo,
    CompanyInfo,
  },
  props: {
    isCompany: { type: Boolean, default: false },
  },
  computed: {
    componentName () {
      return this.isCompany ? 'company-info' : 'user-info'
    },
  },
}
</script>
```

## Versión 3: dynamic imports + <component /> (and code splitting for free!)

Let’s see how [dynamic imports](https://webpack.js.org/guides/code-splitting/#dynamic-imports) and <component /> can play together:

```js
<template>
  <div>
    <component :is="componentInstance" />
  </div>
</template>

<script>
export default {
  props: {
    isCompany: { type: Boolean, default: false },
  },
  computed: {
    componentInstance () {
      const name = this.isCompany ? 'UserInfo' : 'CompanyInfo'
      return () => import(`./components/${name}`)
    }
  }
}
</script>
```