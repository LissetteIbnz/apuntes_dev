# Páginas de ayuda API mediante Swagger

> Para generar páginas de ayuda y una documentación de calidad para la API web, por medio de [Swagger](https://swagger.io/) con la implementación de .NET Core [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore), lo único que tiene que hacer es agregar un par de paquetes de NuGet y modificar el archivo _Startup.cs_.

- En la ventana  **Consola del Administrador de paquetes**:

```cmd
Install-Package Swashbuckle.AspNetCore
```

- En el cuadro de diálogo  **Administrar paquetes NuGet**:
  - Haga clic con el botón derecho en el proyecto en el  **Explorador de soluciones**  >  **Administrar paquetes NuGet**.
  - Establezca el  **origen del paquete**  en "nuget.org".
  - Escriba "Swashbuckle.AspNetCore" en el cuadro de búsqueda.
  - Seleccione el paquete "Swashbuckle.AspNetCore" en la pestaña  **Examinar**  y haga clic en  **Instalar**.

## Agregar y configurar Swagger en el middleware

Agregue el generador de Swagger a la colección de servicios en el método  `ConfigureServices`  de  _Startup.cs_:

```c#
public void ConfigureServices(IServiceCollection services)
{
	var connection = Configuration.GetConnectionString("Dev");
    services.AddDbContext<SchoolDbContext>(options =>
	    options.UseSqlServer(connection));
    services.AddMvc();

    // Register the Swagger generator, defining one or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new Info {
            Version = "v1",
            Title = "Students API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = "None",
            Contact = new Contact { Name = "Sara Lissette Luis Ibáñez", Email = "lissette.ibnz@gmail.com", Url = "https://github.com/LissetteIbnz" },
            License = new License { Name = "Use under ISC", Url = "https://github.com/LissetteIbnz/aspnetcore-api-vuejs-spa/blob/master/LICENSE.md" }
        });

        // Set the comments path for the Swagger JSON and UI.
        var basePath = AppContext.BaseDirectory;
        var xmlPath = Path.Combine(basePath, "API.xml");
        c.IncludeXmlComments(xmlPath);
    });
}
```

Agregue la instrucción using siguiente para la clase  `Info`:

```c#
using Swashbuckle.AspNetCore.Swagger;
```

## [Comentarios XML](https://docs.microsoft.com/es-es/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio#xml-comments)

Los comentarios XML se pueden habilitar con los métodos siguientes:

- En el  **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione  **Propiedades**.
- Seleccione la casilla  **Archivo de documentación XML**  en la sección  **Salida**  de la pestaña  **Compilar**:

![Pestaña Compilar de las propiedades del proyecto](https://docs.microsoft.com/es-es/aspnet/core/tutorials/web-api-help-pages-using-swagger/_static/swagger-xml-comments.png)

En el método  `Configure`  de  _Startup.cs_, habilite el middleware para servir el documento JSON generado y la IU de Swagger:

```c#
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.), specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
    });

    app.UseMvc();
}
```

Ahora modificaremos las tareas para que nuestra API abra, de forma predeterminada, la interfaz de usuario de Swagger, que se puede visualizar yendo a `http://localhost:<random_port>/swagger`:

_API.launchSettings.json_

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:53423/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "launchUrl": "swagger",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "API": {
      "commandName": "Project",
      "launchBrowser": true,
      "launchUrl": "swagger",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:53424/"
    }
  }
}

```

[Más info sobre la configuración anterior](https://docs.microsoft.com/es-es/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio)

[Volver a la tabla de contenidos inicial](SPA-API-ASPNetCore-VueJs.md)