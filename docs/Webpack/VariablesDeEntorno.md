# VARIABLES DE ENTORNO

Para crear variables de entorno en nuestro Front-End y que las pueda utilizar webpack, sólo debemos acceder a los ficheros de configuración de production y development y agregarlas en el módulo de exportación.
Los strings deben estar entre comillas simples y comillas dobles `'"string"'`.

_dev.env.js_

```js
···

module.exports = {
    NODE_ENV: '"development"',
    API: '"http://localhost/api"'
})

···
```

_prod.env.js_

```js
···

module.exports = merge(prodEnv, {
    NODE_ENV: '"production"',
    API: '"http://dominio.com/api"'
})

···
```

Lo siguiente será usarlo en nuestro código (en este caso usando VueJs) en la propiedad `mounted()` de nuestro componente padre podemos agregar el código de referencia de la variable de entorno:

```js
···

<script>
export default {
  mounted() {
    console.log(process.env.API);
  },
  name: 'HelloWorld',
}
</script>
```

Y por último, decirle a webpack que entorno queremos iniciar mediante la ejecución de la tarea correspondiente:

`npm run dev`