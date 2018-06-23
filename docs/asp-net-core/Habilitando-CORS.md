# CORS y nuestro primer listar con Element-UI

## Habilitando CORS

Lo primero que debemos hacer es habilitar CORS para permitir realizar consultas AJAX a otro servidor que no pertenezca a nuestro dominio actual. Para esto nos vamos a StartUp y creamos un Policy o una política.

```c#
public void ConfigureServices(IServiceCollection services) {
    var connection = Configuration.GetConnectionString("Dev");
    services.AddDbContext<StudentDbContext>(options => options.UseSqlServer(connection));

    services.AddTransient<IStudentService, StudentService>();

    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin", builder =>
            builder.AllowAnyHeader()
                   .AllowAnyMethod()
                   .AllowAnyOrigin()
        );
    });

    services.AddMvc();
}
```

Lo que estamos diciéndole a esta política es que para cualquier header, método (put, post, delete, get) u origen todos están permitidos. No hay restricciones.

Y luego le decimos a NETCore que queremos usar la política creada.

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env) {
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseCors("AllowSpecificOrigin");

    app.UseMvc();
}
```

[Volver a la tabla de contenidos inicial](SPA-API-ASPNetCore-VueJs.md)