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

```json
"scripts": {
  "build": "webpack"
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

```json
"scripts" : {
  "dev" : "webpack --mode development" ,
  "build" : "webpack --mode production"
}
```

Ahora intenta ejecutar:

	npm run dev

y echa un vistazo a **./dist/main.js**. El código no está optimizado.

Ahora intenta ejecutar:

	npm run build

y echa un vistazo a **./dist/main.js** para ver un **paquete reducido**

**El modo de producción** permite todo tipo de optimizaciones desde el primer momento. Incluye minificación, scope hoisting (elevación de alcance), tree-shaking y más...

El modo de desarrollo, por otro lado, está optimizado para la velocidad y no hace más que proporcionar un paquete no minificado.

## webpack 4: anulando la entrada / salida predeterminada

Los puntos de entrada/salida se configuran en el archivo `package.json`

Aquí hay un ejemplo:

```json
"scripts": {
  "dev": "webpack --mode development ./foo/src/js/index.js --output ./foo/main.js" ,
  "build": "webpack --mode production ./foo/src/js/index.js --output ./foo/main.js"
}
```

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

```js
{
  "presets": [
    "env"
  ]
}
```

En este punto tenemos 2 opciones para configurar babel-loader:

-   usando un archivo de configuración para el webpack
-   usando `--module-bind` en tus scripts npm

El concepto de **configuración cero en webpack 4 se** aplica a:

-   el **punto de entrada**. Predeterminado a ./src/index.js
-   la **salida**. Predeterminado en ./dist/main.js
-   **modo de producción y desarrollo** (no es necesario crear 2 confs separados para producción y desarrollo)

Y es suficiente, pero para usar los loaders en el webpack 4, aún debes crear un archivo de configuración.

### webpack 4: uso de babel-loader con un archivo de configuración

Proporciona a webpack un archivo de configuración para usar babel-loader de la manera más clásica.

Cree un nuevo archivo llamado `webpack.config.js` y configura el loader:

```js
module.exports  =  {
  module:  {
    rules:  [
      {
        test:  /\.js$/,
        exclude:  /node_modules/,
        use:  {
          loader:  "babel-loader"
        }
      }
    ]
  }
};
```

No es necesario especificar el punto de entrada a menos que desees personalizarlo.

A continuación, abra **./src/index.js** y escriba algo de ES6:

```js
const arr = [1, 2, 3];
const iAmJavascriptES6 = () => console.log(...arr);
window.iAmJavascriptES6 = iAmJavascriptES6;
```

Finalmente crea el paquete con:

	npm run build

Ahora echa un vistazo a **./dist/main.js** para ver el código transpilado.

### webpack 4: uso de babel-loader sin un archivo de configuración

Hay otro método para usar cargadores webpack.

El indicador de `--module-bind` te permite especificar cargadores desde la línea de comando.

Para usar babel-loader sin un archivo de configuración, configura tus scripts npm en **package.json** así:

```json
"scripts": {
    "dev": "webpack --mode development --module-bind js=babel-loader",
    "build": "webpack --mode production --module-bind js=babel-loader"
  }
```

Y estás listo para ejecutar la compilación.

## webpack 4: configuración con React

> Primero será necesario instalar y configurar Babel.

Instalar React con:

	npm i react react-dom -D

A continuación, agrega babel-preset-react :

	npm i babel-preset-react -D

Configure el preset en **.babelrc** :

```js
{
	"presets": [
		"env",
		"react"
	]
}
```

Para poner a prueba las cosas, puedes crear un componente ficticio React en `./src/app.js` :

```js
import React from "react";
import ReactDOM from "react-dom";

const App = () => {
  return (
    <div>
      <p>React here!</p>
    </div>
  );
};

export default App;

ReactDOM.render(<App/>, document.getElementById("app"));
```

A continuación, importa el componente en `./src/index.js`:

	import App from "./app";

y ejecuta `npm run build` de nuevo.

## webpack 4: el HTML webpack plugin

Webpack necesita dos componentes adicionales para procesar HTML: html-webpack-plugin y html-loader.

Agregue las dependencias con:

	npm i html-webpack-plugin html-loader -D
	
Luego actualice la configuración del paquete web:

1.  const HtmlWebPackPlugin = require ( "html-webpack-plugin" ) ;

3.  módulo . exportaciones = {
4.  módulo : {
5.  reglas : \[
6.  {
7.  prueba : /\\.js$/ ,
8.  exclude : / node_modules / ,
9.  uso : {
10.  cargador : "babel-loader"
11.  }
12.  } ,
13.  {
14.  prueba : / \ . html $ / ,
15.  uso : \[
16.  {
17.  cargador : "html-loader" ,
18.  opciones : { minimize : true }
19.  }
20.  \]
21.  }
22.  \]
23.  } ,
24.  complementos : \[
25.  nuevo HtmlWebPackPlugin ( {
26.  plantilla : "./src/index.html" ,
27.  nombre de archivo : "./index.html"
28.  } )
29.  \]
30.  } ;

A continuación, cree un archivo HTML en ./src/index.html :

1.  <! DOCTYPE html>
2.  <html lang = "en" >

4.  <head >
5.  <meta charset = "utf-8" >
6.  <title \> webpack 4 quickstart </ title>
7.  </ head>

9.  <cuerpo >
10.  <div id = "app" >
11.  </ div>
12.  </ body>

14.  </ html>

ejecutar la compilación con:

1.  npm ejecutar compilación

y eche un vistazo a la carpeta ./dist . Debería ver el HTML resultante.

No es necesario incluir su Javascript dentro del archivo HTML: el paquete se inyectará automáticamente.

Abre ./dist/index.html en tu navegador: ¡deberías ver el componente React funcionando!

Como puede ver, nada ha cambiado en lo que respecta al manejo de HTML.

webpack 4 sigue siendo un paquete de módulos con el objetivo de Javascript.

Pero hay planes para agregar HTML como módulo (HTML como punto de entrada).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU1ODA1ODAzMF19
-->