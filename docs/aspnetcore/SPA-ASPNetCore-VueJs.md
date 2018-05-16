# Proyecto SPA Web con ASP.NET Core + VueJs

## Pasos para construirlo

1. [Crear proyecto ASP.NET Core MVC](#1.-crear-proyecto-asp.net-core-mvc)
2. [Instalar VueJS](#2.-instalar-vueJS)
3. [Conectar ASP.NET Core con VueJS](#3.-conectar-asp.net-core-con-vuejs)

### 1. Crear proyecto ASP.NET Core MVC

Creamos un nuevo proyecto web de tipo ASP.NET CORE 2 MVC en un carpeta externa a la API.

Una vez creado el proyecto, eliminaremos todos los archivos que añade la plantilla MVC al proyecto y que no nos serán necesarios, como los bundles, bowers y vistas que no usaremos, así como todos los archivos bajo `wwwroot`.

### 2. Instalar VueJS

Para añadir el framework VueJs, primero deberemos tener instalada la consola de Vue. En caso de no tenerla instalada, ejecutamos el comando `npm install vue-cli --global`.

> Para saber si está instalada ejecutamos en consola `vue --version`.

Seguidamente y dentro de la carpeta raíz que contendrá la aplicación web de nuestro cliente, ejecutamos en consola los siguientes comandos:

```cmd
vue init webpack-simple

? Generate project in current directory? Yes
? Project name spa-vuejs-aspnetcore
? Project description A Vue.js project with ASP.Net Core MVC
? Author Sara Lissette Luis Ibáñez <lissette.ibnz@gmail.com>
? License MIT
? Use sass? Yes
```

Finalizada la instalación de la plantilla, abrimos el proyecto con VSC, ejecutamos `npm i` y aceptamos agregar los archivos de C# necesarios para el proyecto .Net.
> Si se produce algún error al ejecutar `npm install`, cerrar los programas que puedan estar usando el repositorio/proyecto y desde consola como administrador, ejecutarlo nuevamente.

#### Configurar EsLint con AirBnb

Lo siguiente será configurar EsLint en nuestro proyecto. Para ello, ejecutaremos el siguiente comando:

`npm install eslint eslint-config-airbnb eslint-plugin-import  eslint-plugin-jsx-a11y eslint-plugin-react eslint-plugin-import babel-eslint eslint-plugin-html eslint-plugin-vue vue-eslint-parser`

Después, crearemos el archivo `.eslintrc.js` en el raíz de nuestra aplicación:

_.eslintrc.js_

```js
module.exports = {
  root: true,
  parser: 'vue-eslint-parser',
  parserOptions: {
    parser: 'babel-eslint',
    sourceType: 'module',
    allowImportExportEverywhere: true,
  },
  extends: [ 
    'airbnb',
    'plugin:vue/strongly-recommended'
  ],
  // We could also use the https://github.com/vuejs/eslint-plugin-vue
  // required to lint *.vue files
  plugins: [
    'html'
  ],
  // add your custom rules here
  'rules': {
    // allow paren-less arrow functions
    'arrow-parens': 0,
    // allow async-await
    'generator-star-spacing': 0,
    // allow debugger during development
    'no-debugger': process.env.NODE_ENV === 'production' ? 2 : 0
  }
}

```

### 3. Conectar ASP.NET Core con VueJS

Primero necesitamos instalar el paquete **aspnet-webpack** para habilitar la comunicación entre el framework de front-end y la aplicación back-end mediante webpack. También necesitaremos el paquete **webpack-hot-middleware** que es el módulo que se se ocupa de los mecanismos para conectar un navegador de cliente a un servidor webpack y recibir actualizaciones. Se suscribirá a los cambios del servidor y ejecutará esos cambios utilizando la API HMR de webpack.
Para instalar estos paquetes ejecutamos:

`npm i aspnet-webpack  webpack-hot-middleware -D`

Una vez instalados, actualizamos el fichero _webpack.config.js_ para establecer la configuración raíz del directorio:

```js
...
const bundleOutputDir = './wwwroot/dist';

module.exports = {
  entry: { 'main': './src/main.js' },
  output: {
    path: path.join(__dirname, bundleOutputDir),
    filename: '[name].js',
    publicPath: '/dist/'
  },
...

```

Ahora está realizada la configuración de Vue.js. Lo siguiente será configurar ASP.NET Core.

El paquete correspondiente **aspnet-webpack** en el lado ASP.NET Core es **Microsoft.AspNetCore.SpaServices**. Instálalo a través del administrador de paquetes NuGet y registra un middleware en _Startup.cs_:

```cs
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
  ...

  if (env.IsDevelopment())
  {
    ...

    var options = new WebpackDevMiddlewareOptions() { HotModuleReplacement = true };
    app.UseWebpackDevMiddleware(options);
  }
  else

  ...

  app.UseMvc(routes =>
  {
    routes.MapRoute(
      name: "default",
      template: "{controller=Home}/{action=Index}/{id?}");

    // Setup additional routing for SPA
    routes.MapSpaFallbackRoute(
      name: "spa-fallback",
      defaults: new { controller = "Home", action = "Index" });
  });
```

Configuramos los siguientes archivos con el contenido a continuación:

_Views/\_ViewImports.cshtml_

```cs
@using WebApplication
@addTagHelper *, Microsoft.AspNetCore.SpaServices
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers

```

_Views/Shared/\_Layout.cshtml_

```cs
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"]</title>

    <link rel="stylesheet" href="~/dist/vendor.css" asp-append-version="true" />
    <environment names="Staging,Production">
        <link rel="stylesheet" href="~/dist/site.css" asp-append-version="true" />
    </environment>
</head>

<body>
    @RenderBody()

    <script src="~/dist/vendor.js" asp-append-version="true"></script>
    @RenderSection("scripts", required: false)
</body>

</html>

```

_Views/Shared/Error.cshtml_

```cs
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

```

_Views/Home/Index.cshtml_

```cs
@{ ViewData["Title"] = "Home Page"; }

<div id="app"></div>

@section scripts {
    <script src="~/dist/main.js" asp-append-version="true"></script>
}

```

Finalizada esta configuración, podemos ejecutar en el cliente F5 y se cargará nuestra aplicación de Vue.js con ASP.NET Core.

[Volver a la tabla de contenido inicial](SPA-API-ASPNetCore-VueJs.md)