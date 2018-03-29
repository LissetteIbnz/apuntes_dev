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

	npm run dev

y echa un vistazo a **./dist/main.js**. El código no está optimizado.

Ahora intenta ejecutar:

	npm run build

y eche un vistazo a **./dist/main.js** para ver un **paquete reducido**

**El modo de producción** permite todo tipo de optimizaciones desde el primer momento. Incluye minificación, scope hoisting (elevación de alcance), tree-shaking y más...

El modo de desarrollo, por otro lado, está optimizado para la velocidad y no hace más que proporcionar un paquete no minificado.

## webpack 4: anulando la entrada / salida predeterminada

Los puntos de entrada/salida se configuran en el archivo `package.json`

Aquí hay un ejemplo:

    1.  "scripts" : {
    2.  	"dev" : "webpack --mode development ./foo/src/js/index.js --output ./foo/main.js" ,
    3.  	"build" : "webpack --mode production ./foo/src/js/index.js --output ./foo/main.js"
    4.  }

## webpack 4: transpiling Javascript ES6 con Babel

Como no todos los navegadores saben cómo lidiar con ES6 necesitamos algún tipo de transformación.

Este paso de transformación se llama **transpiling**. Transpiling es el acto de tomar ES6 y hacerlo comprensible para los navegadores más antiguos.

Webpack no sabe cómo hacer la transformación, pero tiene **loaders (cargadores)**: piensa en ellos como transformadores.

**babel-loader** es el cargador de webpack para transpilar ES6 y superior, hasta ES5.

Para comenzar a usar el loader, necesitamos instalar un conjunto de dependencias. En particular:

-   babel-core
-   babel-loader
-   babel-preset-env para compilar código Javascript ES6 hasta ES5

Vamos a hacerlo:

	npm i babel-core babel-loader babel-preset-env -D

Luego configura Babel creando un nuevo archivo llamado **.babelrc** dentro de la carpeta del proyecto:

	1.  {
	2.  	"presets": [
	3.  		"env"
	4.  		]
	5.  }

En este punto tenemos 2 opciones para configurar babel-loader:

-   usando un archivo de configuración para el webpack
-   usando `--module-bind` en tus scripts npm

El concepto de **configuración cero en webpack 4 se** aplica a:

-   el **punto de entrada** . Predeterminado a ./src/index.js
-   la **salida** . Predeterminado en ./dist/main.js
-   **modo de producción y desarrollo** (no es necesario crear 2 confs separados para producción y desarrollo)

Y es suficiente, pero para usar los loadersen el webpack 4, aún debes crear un archivo de configuración.

Por ahora, todavía debes confiar en **webpack.config.js** . Vamos a ver…

### paquete web 4: uso de babel-loader con un archivo de configuración

Proporcione a webpack un archivo de configuración para usar babel-loader de la manera más clásica.

Cree un nuevo archivo llamado webpack.config.js y configure el cargador:

1.  módulo . exportaciones = {
2.  módulo : {
3.  reglas : \[
4.  {
5.  prueba : / \ . js $ / ,
6.  exclude : / node_modules / ,
7.  uso : {
8.  cargador : "babel-loader"
9.  }
10.  }
11.  \]
12.  }
13.  } ;

No es necesario especificar el punto de entrada a menos que desee personalizarlo.

A continuación, abra **./src/index.js** y escriba algo de ES6:

1.  const arr = \[ 1 , 2 , 3 \] ;
2.  const iAmJavascriptES6 = ( ) = \> consola . log ( . . . arr ) ;
3.  ventana . iAmJavascriptES6 = iAmJavascriptES6 ;

Finalmente crea el paquete con:

1.  npm ejecutar compilación

Ahora eche un vistazo a **./dist/main.js** para ver el código transpilado.

### paquete web 4: uso de babel-loader sin un archivo de configuración

Hay otro método para usar cargadores webpack.

El indicador de --module-bind le permite especificar cargadores desde la línea de comando. Gracias Cezar por señalar esto.

La bandera no es webpack 4 específica. Estaba allí desde la versión 3.

Para usar babel-loader sin un archivo de configuración, configure sus scripts npm en **package.json** así:

1.  "scripts" : {
2.  "dev" : "webpack --mode development --module-bind js = babel-loader" ,
3.  "build" : "webpack --mode production --module-bind js = babel-loader"
4.  }

Y estás listo para ejecutar la compilación.

No soy fanático de este método (no me gustan los scripts de Fat NPM) pero de todos modos es interesante.
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDc1MzEyMDcyXX0=
-->