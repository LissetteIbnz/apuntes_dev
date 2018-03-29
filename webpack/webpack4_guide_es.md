# WEBPACK CONFIG PROD

> Desde 0 a configuración de modo producción

## TABLA DE CONTENIDO

1. webpack 4: comenzar con cero config
2. webpack 4: modo de producción y desarrollo
3. webpack 4: anulando la entrada/salida predeterminada
4. webpack 4: transpilar Javascript ES6 con Babel
5. webpack 4: configurar con React
6. webpack 4: plugin de webpack HTML
7. webpack 4: extracción de CSS a un archivo
8. webpack 4: servidor de desarrollo webpack
9. webpack 4: recursos

### webpack 4: comenzar con cero config

> webpack 4 no necesita un archivo de configuración por defecto.

Crea un nuevo directorio y muévete hacia él:

    mkdir webpack-config-prod && cd $ _

Inicializa un paquete.json ejecutando:

    npm init -y

y tira del webpack4 en:

    npm install webpack --save-dev

o lo que es lo mismo `npm i webpack -D`

Necesitamos `webpack-cli` también, que vive como un paquete separado:

    npm i webpack-cli -D

Ahora abra `package.json` y agregue un script de compilación:

```javascript
"scripts" : {
  "build" : "webpack"
}
```

Cierra el archivo y guárdalo.

Intenta ejecutar:

    npm run build

y mira lo que pasa:

```bash
ERROR in Entry module not found: Error: Can't resolve './src' in '~/webpack-4-quickstart'
```
Webpack4 está buscando un punto de entrada `./src`
El **entry point** es el archivo que busca webpack para comenzar a construir su paquete de Javascript.

En la versión anterior de webpack, el punto de entrada debía definirse dentro de un archivo de configuración denominado `webpack.config.js`. Pero a partir del **webpack 4 no es necesario definir el punto de entrada** : ¡cogerá **./src/index.js** como valor predeterminado!

Sigamos... ahora creamos el archivo `./src/index.js` con el siguiente contenido:
	
	console.log(`I'm a silly entry point`);

Y ejecutar `npm run build`de nuevo.
Recibiremos el bundle en la ruta `~/webpack-config-prod/dist/main.js`
Observa que no es necesario indicarle un destino de salida. Por defecto utilizará `dist/main.js`. Por tanto, el primer cambio más importante es que **webpack4 no necesita archivo de configuración**.

Se verá en **./src/index.js** como el punto de entrada predeterminado. Además, sacará el bundle en **./dist/main.js** .

## webpack 4: modo producción y desarrollo

Tener 2 archivos de configuración es un patrón común en webpack.

Un proyecto típico puede tener:

-   un **archivo de configuración para desarrollo** , para definir el webpack-dev-server y otras cosas
-   un **archivo de configuración para producción** , para definir **UglifyJSPlugin** , sourcemaps, etc.

Los modos de trabajo se configuran en el archivo de `package.json` de la siguiente manera:

1.  "scripts" : {
2.  	"dev" : "webpack --mode development" ,
3.  	"build" : "webpack --mode production"
4.  }

Ahora intenta ejecutar:

1.  npm run dev

y eche un vistazo a **./dist/main.js** . ¿Que ves? Sí, lo sé, un paquete aburrido ... ¡no minúsculo!

Ahora intenta ejecutar:

1.  npm ejecutar compilación

y eche un vistazo a **./dist/main.js** . ¿Qué ves ahora? ¡Un **paquete reducido** !

¡Sí!

**El modo de producción** permite todo tipo de optimizaciones desde el primer momento. Incluye minificación, elevación de alcance, sacudida de árboles y más.

El modo de desarrollo, por otro lado, está optimizado para la velocidad y no hace más que proporcionar un paquete no minificado.

Así que aquí está la segunda noticia: webpack 4 introduce el modo de **producción** y **desarrollo** .

¡En el paquete web 4 puedes vivir sin una sola línea de configuración! ¡Simplemente defina el indicador --mode y obtendrá todo gratis!


<!--stackedit_data:
eyJoaXN0b3J5IjpbNDMzOTA0ODEzXX0=
-->