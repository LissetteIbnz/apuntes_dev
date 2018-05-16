# NET CORE API + VueJs SPA

**Tabla de contenidos**

- [Configurando nuestra Api con EF](#configurando-nuestra-api-con-ef)
    - [SharedContracts](#sharedcontracts)
    - [Persistence](#persistence)
        - [Inyectar nuestro contexto con DI](#inyectar-nuestro-contexto-con-di)
- [Agregando el código para inicializar la base de datos con datos de prueba](#agregando-el-código-para-inicializar-la-base-de-datos-con-datos-de-prueba)
- [Services](#services)
- [API](#api)
- [Páginas de ayuda API mediante Swagger](Doc-API-Swagger.md)
- [Crear el proyecto SPA ASP.NET Core con Vue.Js](SPA-ASPNetCore-VueJs.md)
- [CORS y nuestro primer listar con Element-UI](#cors-y-nuestro-primer-listar-con-element-ui)
- [Registrando un nuevo estudiante](#registrando-un-nuevo-estudiante)
- [Actualizando y eliminado un estudiante](#actualizando-y-eliminado-un-estudiante)

## Configurando nuestra API con EF

Lo primero será crear un nuevo proyecto de Aplicación web ASP.NET Core 2.0 con la plantilla API. Lo llamaremos API y será el encargado de recibir las peticiones del cliente y resolverlas adecuadamente.
Seguidamente, configuraremos la estructura de nuestro proyecto.

### SharedContracts

> Representa nuestro modelo de la capa de dominio, es decir las tablas mapeadas a clases y las operaciones que podrán ejecutarse en él.

![enter image description here](https://docs.microsoft.com/es-es/aspnet/core/data/ef-mvc/intro/_static/data-model-diagram.png)

Para crear nuestro modelo, agregaremos a la solución un nuevo proyecto de **Biblioteca de clases (.Net Core)** llamado SharedContracts.
Seguidamente, crearemos una carpeta llamada DataContracts y en ella, tres clases para nuestras tres entidades: _Course, Enrollment y Student_.

_SharedContracts.DataContracts.Course.cs_

```c#
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations.Schema;

namespace SharedContracts
{
    public class Course
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int CourseID { get; set; }
        public string Title { get; set; }
        public int Credits { get; set; }

        public ICollection<Enrollment> Enrollments { get; set; }
    }
}

```

_SharedContracts.DataContracts.Enrollment.cs_

```c#
namespace SharedContracts
{
    public enum Grade
    {
        A, B, C, D, F
    }

    public class Enrollment
    {
        public int EnrollmentID { get; set; }
        public int CourseID { get; set; }
        public int StudentID { get; set; }
        public Grade? Grade { get; set; }

        public Course Course { get; set; }
        public Student Student { get; set; }
    }
}

```

_SharedContracts.DataContracts.Student.cs_

```c#
using System;
using System.Collections.Generic;

namespace SharedContracts
{
    public class Student
    {
        public int StudentId { get; set; }
        public string Name { get; set; }
        public string LastName { get; set; }
        public string Bio { get; set; }
        public DateTime EnrollmentDate { get; set; }

        public ICollection<Enrollment> Enrollments { get; set; }
    }
}

```

> De forma predeterminada, Entity Framework interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.
> La propiedad  `Enrollments`  es una propiedad de navegación.  Las propiedades de navegación contienen otras entidades relacionadas con esta entidad.
> Si una propiedad de navegación puede contener varias entidades (como en las relaciones de varios a varios o uno a varios), su tipo debe ser una lista a la que se puedan agregar las entradas, eliminarlas y actualizarlas, como  `ICollection<T>`.  Puede especificar  `ICollection<T>`  o un tipo como  `List<T>`  o  `HashSet<T>`.  Si especifica  `ICollection<T>`, EF crea una colección  `HashSet<T>`  de forma predeterminada.

Ahora crearemos la interfaz que contendrá las operaciones que podrán realizarse con nuestras entidades. Primero, crearemos la carpeta que contendrá las interfaz con los métodos. Esta carpeta se llamará OperationContracts.

_SharedContracts.OperationContracts.IStudentService.cs_

```c#
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace SharedContracts
{
    public interface IStudentService : IDisposable
    {
        Task<List<Student>> GetAllAsync();
        Task<Student> GetByIdAsync(int id);
        Task AddAsync(Student model);
        Task UpdateAsync(Student model);
        Task DeleteAsync(int id);
    }
}

```

### Persistence

Agregaremos un nuevo proyecto tipo **Biblioteca de clases (.Net Core)** y seguidamente, crearemos la clase que contendrá nuestro [contexto de base de datos](https://docs.microsoft.com/es-es/aspnet/core/data/ef-mvc/intro#create-the-database-context)
Nuestra clase será encargada de manejar las migraciones y la conexión a la base de datos. Para hacer funcionar esta parte debemos agregar los siguientes package de nuget y luego crear nuestro DbContext.

- EntityFrameworkCore.SqlServer
- EntityFrameworkCore.Tools

_Persistence.SchoolDbContext.cs_

```c#
using Microsoft.EntityFrameworkCore;
using SharedContracts;

namespace Persistence
{
    public class SchoolDbContext : DbContext
    {
        public SchoolDbContext(DbContextOptions<SchoolDbContext> options) : base(options)
        {
        }

        public DbSet<Course> Courses { get; set; }
        public DbSet<Enrollment> Enrollments { get; set; }
        public DbSet<Student> Students { get; set; }

        /**
         * El comportamiento predeterminado de EF es crear los nombres de las tablas en plural.
         * Se invalidará este comportamiento mediante la especificación de nombres tabla en 
         * singular en DbContext.
         */
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Course>().ToTable("Course");
            modelBuilder.Entity<Enrollment>().ToTable("Enrollment");
            modelBuilder.Entity<Student>().ToTable("Student");
        }
    }
}

```

> Este código crea una propiedad  `DbSet`  para cada conjunto de entidades.  En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.

#### Inyectar nuestro contexto con DI

No olvidemos registrar dicha dependencia en nuestro StartUp para que este disponible para todo el proyecto.

```c#
public void ConfigureServices(IServiceCollection services) {
    var connection = Configuration.GetConnectionString("Dev");
    services.AddDbContext<SchoolDbContext>(options => options.UseSqlServer(connection));

    services.AddMvc();
}
```

_API.appsettings.json_

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity1;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  }
}
```

### Agregando el código para inicializar la base de datos con datos de prueba

En esta sección crearemos el código necesario para inicializar nuestro base de datos con datos de prueba.
En la carpeta  _Data_, cree un archivo de clase denominado  _DbInitializer.cs_  y reemplace el código de plantilla con el código siguiente, que hace que se cree una base de datos cuando es necesario y carga datos de prueba en la nueva base de datos.

_Persistence.Data.DbInitializer.cs_

```c#
using SharedContracts;
using System;
using System.Linq;

namespace Persistence.Data
{
    public class DbInitializer
    {
        public static void Initialize(SchoolDbContext context)
        {
            context.Database.EnsureCreated();

            // Look for any students.
            if (context.Students.Any())
            {
                return;   // DB has been seeded
            }

            var students = new Student[]
            {
            new Student{Name="Carson",LastName="Alexander",EnrollmentDate=DateTime.Parse("2005-09-01")},
            new Student{Name="Meredith",LastName="Alonso",EnrollmentDate=DateTime.Parse("2002-09-01")},
            new Student{Name="Arturo",LastName="Anand",EnrollmentDate=DateTime.Parse("2003-09-01")},
            new Student{Name="Gytis",LastName="Barzdukas",EnrollmentDate=DateTime.Parse("2002-09-01")},
            new Student{Name="Yan",LastName="Li",EnrollmentDate=DateTime.Parse("2002-09-01")},
            new Student{Name="Peggy",LastName="Justice",EnrollmentDate=DateTime.Parse("2001-09-01")},
            new Student{Name="Laura",LastName="Norman",EnrollmentDate=DateTime.Parse("2003-09-01")},
            new Student{Name="Nino",LastName="Olivetto",EnrollmentDate=DateTime.Parse("2005-09-01")}
            };
            foreach (Student s in students)
            {
                context.Students.Add(s);
            }
            context.SaveChanges();

            var courses = new Course[]
            {
            new Course{CourseID=1050,Title="Chemistry",Credits=3},
            new Course{CourseID=4022,Title="Microeconomics",Credits=3},
            new Course{CourseID=4041,Title="Macroeconomics",Credits=3},
            new Course{CourseID=1045,Title="Calculus",Credits=4},
            new Course{CourseID=3141,Title="Trigonometry",Credits=4},
            new Course{CourseID=2021,Title="Composition",Credits=3},
            new Course{CourseID=2042,Title="Literature",Credits=4}
            };
            foreach (Course c in courses)
            {
                context.Courses.Add(c);
            }
            context.SaveChanges();

            var enrollments = new Enrollment[]
            {
            new Enrollment{StudentID=1,CourseID=1050,Grade=Grade.A},
            new Enrollment{StudentID=1,CourseID=4022,Grade=Grade.C},
            new Enrollment{StudentID=1,CourseID=4041,Grade=Grade.B},
            new Enrollment{StudentID=2,CourseID=1045,Grade=Grade.B},
            new Enrollment{StudentID=2,CourseID=3141,Grade=Grade.F},
            new Enrollment{StudentID=2,CourseID=2021,Grade=Grade.F},
            new Enrollment{StudentID=3,CourseID=1050},
            new Enrollment{StudentID=4,CourseID=1050},
            new Enrollment{StudentID=4,CourseID=4022,Grade=Grade.F},
            new Enrollment{StudentID=5,CourseID=4041,Grade=Grade.C},
            new Enrollment{StudentID=6,CourseID=1045},
            new Enrollment{StudentID=7,CourseID=3141,Grade=Grade.A},
            };
            foreach (Enrollment e in enrollments)
            {
                context.Enrollments.Add(e);
            }
            context.SaveChanges();
        }
    }
}

```

Ahora, para inicializar la base de datos, podemos hacerlo mediante las migraciones con el PM o agregando la inicialización de la base de datos en el `Program.cs`.

#### a) Por medio de migraciones

En el proyecto Persistence, agregamos el paquete `Microsoft.EntityFrameworkCore.SqlServer` y después, en la Consola del Administrador de paquetes, elegimos el proyecto Persistence y ejecutamos los siguientes comandos:

    PM> add-migration Initialize
    To undo this action, use Remove-Migration.
    PM> update-database
    Applying migration '20180429114542_Initialize'.
    Done.

#### b) Mediante el `main` en  `Program.cs`

En esta sección, escribirá un método que se llama después de crear la base de datos para rellenarla con datos de prueba.
Aquí usará el método  `EnsureCreated`  para crear automáticamente la base de datos.
En  _Program.cs_, modifique el método  `Main`  para que haga lo siguiente al iniciar la aplicación:

- Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.
- Llamar al método de inicialización, pasándolo al contexto.
- Eliminar el contexto cuando el método de inicialización haya finalizado.

_API.Program.cs_

```c#
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Persistence;
using Persistence.Data;
using System;

namespace API
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = BuildWebHost(args);

            using (var scope = host.Services.CreateScope())
            {
                var services = scope.ServiceProvider;
                try
                {
                    var context = services.GetRequiredService<SchoolDbContext>();
                    DbInitializer.Initialize(context);
                }
                catch (Exception ex)
                {
                    var logger = services.GetRequiredService<ILogger<Program>>();
                    logger.LogError(ex, "An error occurred while seeding the database.");
                }
            }

            host.Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}

```

> Si quieres ver la base de datos creada, sólo debes abrir la ventana de Explorador de objetos de SQL Server.

### Services

Vamos a declarar los servicios que se encargarán de realizar las consultas a la base de datos. Para este ejemplo, optaremos por acceder a la base de datos de forma asíncrona.
Primeramente, implementaremos la interfaz para la entidad Student.

_Services.StudentRepository_

```c#
using Microsoft.EntityFrameworkCore;
using Persistence;
using SharedContracts;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace Service
{
    public class StudentRepository : IStudentService
    {
        private readonly SchoolDbContext _schoolDbContext;

        public StudentRepository(SchoolDbContext schoolDbContext)
        {
            _schoolDbContext = schoolDbContext;
        }

        public async Task<List<Student>> GetAllAsync()
        {
            return await _schoolDbContext.Students.AsNoTracking().ToListAsync();
        }

        public async Task<Student> GetByIdAsync(int id)
        {
            return await _schoolDbContext.Students.FindAsync(id);
        }

        public async Task AddAsync(Student model)
        {
            _schoolDbContext.Add(model);
            await _schoolDbContext.SaveChangesAsync();
        }

        public async Task UpdateAsync(Student model)
        {

            var originalModel = await _schoolDbContext.Students.FindAsync(model.StudentId);

            originalModel.Name = model.Name;
            originalModel.LastName = model.LastName;
            originalModel.Bio = model.Bio;

            _schoolDbContext.Update(originalModel);
            await _schoolDbContext.SaveChangesAsync();
        }

        public async Task DeleteAsync(int id)
        {
            var student = await _schoolDbContext.Students.FindAsync(id);
            _schoolDbContext.Students.Remove(student);
            await _schoolDbContext.SaveChangesAsync();
        }

        private bool _disposed;

        protected virtual void Dispose(bool disposing)
        {
            if (!_disposed)
            {
                if (disposing)
                {
                    _schoolDbContext.Dispose();
                }
            }
            _disposed = true;
        }

        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }
    }
}

```

### API

Nuestra capa REST que implementa los recursos a exponer como API.

```c#
using Microsoft.AspNetCore.Mvc;
using SharedContracts;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace API.Controllers
{
    [Produces("application/json")]
    [Route("api/Students")]
    public class StudentsController : Controller
    {
        private readonly IStudentService _studentService;

        public StudentsController(IStudentService studentService)
        {
            _studentService = studentService;
        }

        // GET: api/Students
        [HttpGet]
        public async Task<List<Student>> Get()
        {
            return await _studentService.GetAllAsync();
        }

        // GET: api/Students/5
        [HttpGet("{id}")]
        public async Task<IActionResult> Get(int id)
        {
            if (!ModelState.IsValid)
            {
                return BadRequest(ModelState);
            }

            var student = await _studentService.GetByIdAsync(id);

            if (student == null)
            {
                return NotFound(id);
            }

            return Ok(student);
        }

        // POST: api/Students/5
        [HttpPost]
        public async Task<IActionResult> Post([FromBody] Student model)
        {
            if (!ModelState.IsValid)
            {
                return BadRequest(ModelState);
            }
            await _studentService.AddAsync(model);
            return CreatedAtAction("GetStudent", new { id = model.StudentId }, model);
        }

        // PUT: api/Students/5
        [HttpPut("{id}")]
        public async Task<IActionResult> Put([FromBody] Student student)
        {
            if (await _studentService.GetByIdAsync(student.StudentId) == null)
            {
                return NotFound(student.StudentId);
            }

            if (!ModelState.IsValid)
            {
                return BadRequest(ModelState);
            }

            await _studentService.UpdateAsync(student);
            return Ok();
        }

        // DELETE: api/Students/5
        [HttpDelete("{id}")]
        public async Task<IActionResult> Delete([FromRoute] int id)
        {
            if (!ModelState.IsValid)
            {
                return BadRequest(ModelState);
            }

            var student = await _studentService.GetByIdAsync(id);
            if (student == null)
            {
                return NotFound();
            }

            await _studentService.DeleteAsync(id);

            return Ok();
        }
    }
}
```

**Resultado carpetas**

```cmd
└── src
├── components
│ ├── a.vue 
│ └── b.vue 
├── containers 
│ └── index.vue 
├── index.js 
└── jsconfig.json
```


### Creando el Frontend

https://marczak.io/posts/netcore-vuejs/

### Requisitos previos

1.  [.NET Core 2.0 SDK](https://www.microsoft.com/net/learn/get-started/windows)
2.  [Código de Visual Studio](https://code.visualstudio.com/)
    1.  C # Extension
    2.  Extensión de Vetur
3.  [NodeJS versión 8.9.4](https://nodejs.org/en/)

## Construyendo la plantilla

Ahora que se cumplen los requisitos previos, se puede inicializar un proyecto.

1.  Abra el terminal e inicialice el proyecto. Para esta demo, se usará la plantilla MVC .NET Core.
    
```bash
cd E:\
mkdir ASPNetCore.VueSPA.Web
cd ASPNetCore.VueSPA.Web
dotnet new mvc
code .
```
    
Configure **Startup.cs** para las rutas de Web Pack & SPA fallback.

```diff
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.SpaServices.Webpack;

namespace MarczakIO.VueSPA.Web
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
        }

        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
+               app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions
+               {
+                   HotModuleReplacement = true
+               });
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }

            app.UseStaticFiles();

            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");

+               routes.MapSpaFallbackRoute(
+                   name: "spa-fallback",
+                   defaults: new { controller = "Home", action = "Index" });
            });
        }
    }
}
```

1.  1.  Esto hace que WebPack reemplace los archivos automáticamente cuando los edita en el editor usando Hot Module Replacement y,
    2.  Siempre que no se encuentre una ruta de controlador, se redirigirá una ruta de regreso a Inicio / Índice para que la aplicación pueda usar URL nativas **[https://myserver.com/MyApp/MyView/MyAction en](https://myserver.com/MyApp/MyView/MyAction)** lugar de hashbang navigation **[https://myserver.com/#! / MyApp / MyView / MyAction](https://myserver.com/#!/MyApp/MyView/MyAction)** . Todavía es necesario deshabilitar la navegación hashbang que está habilitada de forma predeterminada, verifique el siguiente punto para eso.
2.  Agregue el archivo de configuración del paquete web al catálogo raíz llamado **webpack.config.js** . Este archivo es responsable de configurar cómo compilará webpack javascript, vue, scss, css y otros archivos en el paquete del proyecto. Una vez creado, pega el contenido.
    
    ```js
    var path = require('path')
    var webpack = require('webpack')
    const bundleOutputDir = './wwwroot/dist';
    
    module.exports = {
     context: __dirname,
      entry: { main: './App/index.js' },
      module: {
        rules: [
          {
            test: /\.css$/,
            use: [
              'vue-style-loader',
              'css-loader'
            ],
          },
          {
            test: /\.vue$/,
            loader: 'vue-loader',
            options: {
              loaders: {
                'scss': [
                  'vue-style-loader',
                  'css-loader',
                  'sass-loader'
                ],
                'sass': [
                  'vue-style-loader',
                  'css-loader',
                  'sass-loader?indentedSyntax'
                ]
              }
            }
          },
          {
            test: /\.js$/,
            loader: 'babel-loader',
            exclude: /node_modules/
          },
          {
            test: /\.(png|jpg|gif|svg)$/,
            loader: 'file-loader',
            options: {
              name: '[name].[ext]?[hash]'
            }
          }
        ]
      },
      resolve: {
        alias: {
          'vue$': 'vue/dist/vue.esm.js'
        },
        extensions: ['*', '.js', '.vue', '.json']
      },
      devServer: {
        historyApiFallback: true,
        noInfo: true,
        overlay: true
      },
      performance: {
        hints: false
      },output: {
        path: path.join(__dirname, bundleOutputDir),
        filename: '[name].js',
        publicPath: 'dist/'
    },
      devtool: '#eval-source-map'
    }
    
    if (process.env.NODE_ENV === 'production') {
      module.exports.devtool = '#source-map'
      module.exports.plugins = (module.exports.plugins || []).concat([
        new webpack.DefinePlugin({
          'process.env': {
            NODE_ENV: '"production"'
          }
        }),
        new webpack.optimize.UglifyJsPlugin({
          sourceMap: true,
          compress: {
            warnings: false
          }
        }),
        new webpack.LoaderOptionsPlugin({
          minimize: true
        })
      ])
    }
    ```
    
    1.  La línea 3 define dónde debe estar toda la salida del paquete. En el caso de ASP.NET, siempre debe ir a wwwroot, pero el resto es opcional.
        
        ```js
         const bundleOutputDir = './wwwroot/dist';
         
        ```
        
    2.  La línea 7 define dónde buscar la raíz de la aplicación. Tenga en cuenta que esta es la ruta del proyecto dentro del directorio del espacio de trabajo.
        
        ```js
         entry: { main: './App/index.js' },
         
        ```
        
3.  Agregue **.babelrc** en el directorio raíz para configurar el motor Babel para JavaScript
    
    ```js
    {
        "presets": [
            ["env", { "modules": false }],
            "stage-3"
        ]
    }
    ```
    
4.  Agregue **package.json** en el directorio raíz para configurar paquetes de nodo NPM
    
    ```js
    {
      "name": "lingaro.office.planner",
      "private": true,
      "version": "0.0.0",
      "devDependencies": {
        "@types/webpack-env": "^1.13.5",
        "aspnet-webpack": "^2.0.3",
        "babel-plugin-transform-object-rest-spread": "^6.26.0",
        "css-loader": "^0.25.0",
        "event-source-polyfill": "^0.0.7",
        "extract-text-webpack-plugin": "^2.1.2",
        "file-loader": "^0.9.0",
        "isomorphic-fetch": "^2.2.1",
        "jquery": "^3.3.1",
        "node-sass": "^4.5.3",
        "sass-loader": "^6.0.6",
        "style-loader": "^0.13.1",
        "url-loader": "^0.5.7",
        "webpack": "^2.7.0",
        "webpack-hot-middleware": "^2.21.0"
      },
      "dependencies": {
        "babel-core": "^6.26.0",
        "babel-loader": "^7.1.2",
        "babel-polyfill": "^6.26.0",
        "babel-preset-env": "^1.6.1",
        "babel-preset-stage-3": "^6.24.1",
        "vue": "^2.5.13",
        "vue-loader": "^14.0.3",
        "vue-router": "^3.0.1",
        "vue-template-compiler": "^2.5.13"
      },
      "browserslist": [
        "> 1%",
        "last 2 versions",
        "not ie <= 8"
      ]
    }
    ```
1.  Abra Terminal haciendo clic en **Ver> Terminal integrado** o **presionando Ctrl + `**  ![Captura de pantalla](https://marczak.io/images/netcore-vuejs/2.png)
    1.  Ejecute el comando de **restauración dotnet** en el terminal para restaurar paquetes .NET perdidos ![Captura de pantalla](https://marczak.io/images/netcore-vuejs/3.png)
    2.  Ejecute el comando **npm install** en el terminal para restaurar los módulos de nodo faltantes ![Captura de pantalla](https://marczak.io/images/netcore-vuejs/5.png)
2.  Modifique la vista **_Layout.cshtml** en **Vistas /** Catálogo **compartido**
    
    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - Lingaro.OfficePlanner.Web</title>
        <base href="~/" />
    </head>
    <body>
        @RenderBody()
    
        @RenderSection("scripts", required: false)
    </body>
    </html>
    ```
    
3.  Modifique la vista **Index.cshtml** en **Views / Home** catalog
    
    ```HTML
    <!DOCTYPE html>
    @{
        ViewData["Title"] = "Home Page";
    }
    
    <div id='app'>Loading...</div>
    
    @section scripts {
        <script src="~/dist/main.js" asp-append-version="true"></script>
    }
    ```
    1.  Agregue la carpeta **src** en el directorio raíz y cree los siguientes archivos:
    1.  Cree **index.js** , este es el archivo raíz principal para la aplicación SPA sobre la que se compilará el paquete web. Vale la pena señalar que el parámetro mod en VueRouter lo está estableciendo en el historial, lo que significa que desactiva la navegación hashbang.
        
        ```js
        import Vue from 'vue'
        import VueRouter from 'vue-router'
        import App from './App.vue'
        
        Vue.config.productionTip = false
        Vue.use(VueRouter)
        
        const routes = [
          { 
            path: '/', 
            component: App
          }
        ]
        
        const router = new VueRouter({
          routes,
          mode: 'history'
        })
        
        new Vue({
          el: '#app',
          template: "<div><router-view></router-view></div>",
          router
        })
        ```
        
    2.  Crea **App.vue** . Con la extensión vetur todo el código se resaltará correctamente para esos archivos.
        
        ```js
        <template>
            <div id="home">
                <h1>Hello World!</h1>
            </div>
        </template>
        
        <script>
        export default { }
        </script>
        
        <style lang="scss">
        
        </style>
        ```
        
2.  Presione F5 y vea la primera plantilla de la aplicación SPA funcionando. En la primera ejecución, VS Code preguntará en qué entorno iniciar la aplicación, en cuyo caso elige .NET Core.
    
    ![Captura de pantalla](https://marczak.io/images/netcore-vuejs/6.png)
    
    ![Captura de pantalla](https://marczak.io/images/netcore-vuejs/7.png)
    

#### Para resumir lo que se ha hecho

-   Se creó una aplicación Core MVC .NET estándar utilizando la plantilla MVC para inicializar el proyecto
-   WebPack se ha inicializado para que compile y vuelva a cargar los archivos .vue, .js, .css para la aplicación SPA.
-   Babel se inicializó con los estándares más nuevos de ES2015 que se pueden usar en javascript
-   Se definieron rutas .NET para que SPA pueda usar navegación URL nativa en lugar de hashbang (index.js habilitó esta configuración)













Dependencias
webpack
npm install webpack-dev-middleware --save-dev

### Creando nuestro servicio en VueJs SPA

En la carpeta src/services vamos a crear nuestro servicio con la finalidad de poder centralizar la lógica y que este sea reutilizable en cualquier parte de nuestro proyecto. Adicionalmente, vamos a registrarlo con Vuex para que sea accesible desde cualquier otro componente evitando la tarea de tener que hacer un import en cada componente.

*StudentService.js*

```js
class StudentService {
    axios
    baseUrl

    constructor(axios, apiUrl) {
        this.axios = axios
        this.baseUrl = `${apiUrl}student`
    }

    getAll() {
        let self = this;
        return self.axios.get(`${self.baseUrl}`);
    }
}

export default StudentService
```

Luego en el archivo src/store/services.js declaramos nuestra ruta de la API y lo inyectamos a nuestro constructor.

*services.js*

```js
import Axios from 'axios'
import exampleService from '../services/ExampleService'
import studentService from '../services/StudentService'

let apiUrl = '[http://localhost:64550/](http://localhost:64550/)'

// Axios Configuration
Axios.defaults.headers.common.Accept = 'application/json'

export default {
    exampleService: new exampleService(Axios),
    studentService: new studentService(Axios, apiUrl)
}
```

### Creando nuestro componente

Creamos una archivo en src/components/student/index.vue y declaramos lo siguiente.

```js
<template>
<div>
    <h2>Estudiantes</h2>
    <el-table v-loading="loading" :data="data" style="width: 100%">
        <el-table-column prop="name" label="Nombre" sortable></el-table-column>
        <el-table-column prop="lastName" label="Apellido" width="200" sortable></el-table-column>
        <el-table-column prop="bio" label="Acerca de mi" width="400" sortable></el-table-column>
      </el-table>
</div>
</template>

<script> export default {
  name: "StudentIndex",
  data() {
    return {
        data: [],
        loading: false
    };
  },
  created() {
      let self = this;
      self.getAll();
  },
  methods: {
    getAll() {
      let self = this;

      self.loading = true;

      self.$store.state.services.studentService
        .getAll()
        .then(r => {
          self.loading = false;
          self.data = r.data;
        }).catch(r => {
          self.$message({
            message: "Ocurrió un error inesperado",
            type: "error"
          });
        });
    }
  }
}; 
</script>
```

No olvidemos decirle a nuestro VueRouter (src/router.index.js) que X ruta pertenece a X componente.

```js
import Vue from 'vue'
import Router from 'vue-router'

import Default from '@/components/Default'
import ExampleIndex from '@/components/example/Index'
import ExampleView from '@/components/example/View'
import StudentIndex from '@/components/students/Index'
import StudentCreateOrUpdate from '@/components/students/CreateOrUpdate'

Vue.use(Router)

const routes = [
  { path: '/', name: 'Default', component: Default },
  { path: '/example', name: 'ExampleIndex', component: ExampleIndex },
  { path: '/example/:id', name: 'ExampleView', component: ExampleView },
  { path: '/students/', name: 'StudentIndex', component: StudentIndex },
  { path: '/students/add', name: 'StudentCreate', component: StudentCreateOrUpdate },
  { path: '/students/:id/edit', name: 'StudentEdit', component: StudentCreateOrUpdate },
]

export default new Router({
  routes
})
```

## Registrando un nuevo estudiante

En esta lección vamos agregar un nuevo método a nuestro studentService para hacer un llamado a nuestro endpoint y registrar al nuevo estudiante.

StudentsCreateOrUpdate: Si el id está especificado es una actualización en caso contrario se trata de un nuevo registro.

En el fichero `studentService.js` registramos los endpoint de nuestra api:
```js
nameMethod(model) {
	let self = this;
	return self.axios.post(`${self.baseUrl}`, model);
};
```

### StudentService

```js
class StudentService {
    axios
    baseUrl
    constructor(axios, apiUrl) {
        this.axios = axios
        this.baseUrl = `${apiUrl}student`
    }
    getAll() {
        let self = this;
        return self.axios.get(`${self.baseUrl}`);
    }
    add(model) {
        let self = this;
        return self.axios.post(`${self.baseUrl}`, model);
    }
}
```

### Nuestro formulario con Element-UI

Luego vamos a crear nuestro formulario y le agregamos reglas de validación usando Element-UI.

```html
<template>
<div>
    <h2>{{ pageTitle }}</h2>
    <el-form v-loading="loading" :model="form" :rules="rules" ref="ruleForm">
      <el-form-item label="Nombre" prop="name">
        <el-input v-model="form.name"></el-input>
      </el-form-item>
      <el-form-item label="Apellido" prop="lastName">
        <el-input v-model="form.lastName"></el-input>
      </el-form-item>
      <el-form-item label="Acerca de mí" prop="bio">
        <el-input type="textarea" v-model="form.bio"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="save" type="primary">Guardar</el-button>
      </el-form-item>
    </el-form>
</div>
</template>
```

Y en la parte inferior con respecto al javascript vamos agregar los métodos de guardar, las propiedades de nuestro modelo estudiante y las reglas de validación.

```js
export default {
  name: "StudentCreateOrUpdate",
  data() {
    return {
      loading: false,
      form: {
        studentId: 0,
        name: null,
        lastName: null,
        bio: null
      },
      rules: {
        name: [
          { required: true, message: "Debe ingresar un nombre" },
          { min: 3, message: "Debe contener como mínimo 3 caracteres" }
        ],
        lastName: [{ required: true, message: "El apellido es requerido" }],
        bio: [{ required: true, message: "Este campo es requerido" }]
      }
    };
  },
  computed: {
    pageTitle() {
      return this.form.studentId === 0 ? "Nuevo estudiante" : this.form.name;
    }
  },
  created() {},
  methods: {
    save() {
      let self = this;
      self.$refs["ruleForm"].validate(valid => {
        if (valid) {
          self.$store.state.services.studentService
            .add(self.form)
            .then(r => {
              self.loading = false;
              self.$router.push('/students');
            })
            .catch(r => {
              self.$message({
                message: "Ocurrió un error inesperado",
                type: "error"
              });
            });
        }
      });
    }
  }
};
```

## Actualizando y eliminado un estudiante
