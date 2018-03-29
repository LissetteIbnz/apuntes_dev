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

En la siguiente sección, veremos otra característica interesante de webpack 4: modo de **producción y desarrollo** .


<!--stackedit_data:
eyJoaXN0b3J5IjpbMTcwNzg1MDYyXX0=
-->