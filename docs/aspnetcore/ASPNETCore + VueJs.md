---


---

<h1 id="net-core-api--vuejs-spa">NET CORE Api + VueJs SPA</h1>
<p><strong>Tabla de contenidos</strong></p>
<ul>
<li><a href="#configurando-nuestra-api-con-ef">Configurando nuestra Api con EF</a></li>
<li><a href="#cors-y-nuestro-primer-listar-con-element-ui">CORS y nuestro primer listar con Element-UI</a></li>
<li><a href="#registrando-un-nuevo-estudiante">Registrando un nuevo estudiante</a></li>
<li><a href="#actualizando-y-eliminado-un-estudiante">Actualizando y eliminado un estudiante</a></li>
</ul>
<h2 id="configurando-nuestra-api-con-ef">Configurando nuestra Api con EF</h2>
<h2 id="páginas-de-ayuda-de-asp.net-core-web-api-mediante-swagger">Páginas de ayuda de <a href="http://ASP.NET">ASP.NET</a> Core Web API mediante Swagger</h2>
<blockquote>
<p>Para generar páginas de ayuda y una documentación de calidad para la API web, por medio de <a href="https://swagger.io/">Swagger</a> con la implementación de .NET Core <a href="https://github.com/domaindrivendev/Swashbuckle.AspNetCore">Swashbuckle.AspNetCore</a>, lo único que tiene que hacer es agregar un par de paquetes de NuGet y modificar el archivo <em>Startup.cs</em>.</p>
</blockquote>
<ul>
<li>En la ventana  <strong>Consola del Administrador de paquetes</strong>:</li>
</ul>
<pre><code>Install-Package Swashbuckle.AspNetCore
</code></pre>
<ul>
<li>
<p>En el cuadro de diálogo  <strong>Administrar paquetes NuGet</strong>:</p>
<ul>
<li>Haga clic con el botón derecho en el proyecto en el  <strong>Explorador de soluciones</strong>  &gt;  <strong>Administrar paquetes NuGet</strong>.</li>
<li>Establezca el  <strong>origen del paquete</strong>  en “<a href="http://nuget.org">nuget.org</a>”.</li>
<li>Escriba “Swashbuckle.AspNetCore” en el cuadro de búsqueda.</li>
<li>Seleccione el paquete “Swashbuckle.AspNetCore” en la pestaña  <strong>Examinar</strong>  y haga clic en  <strong>Instalar</strong>.</li>
</ul>
</li>
</ul>
<h3 id="agregar-y-configurar-swagger-en-el-middleware">Agregar y configurar Swagger en el middleware</h3>
<p>Agregue el generador de Swagger a la colección de servicios en el método  <code>ConfigureServices</code>  de  <em>Startup.cs</em>:</p>
<pre class=" language-c"><code class="prism # language-c">public <span class="token keyword">void</span> <span class="token function">ConfigureServices</span><span class="token punctuation">(</span>IServiceCollection services<span class="token punctuation">)</span>
<span class="token punctuation">{</span>
	var connection <span class="token operator">=</span> Configuration<span class="token punctuation">.</span><span class="token function">GetConnectionString</span><span class="token punctuation">(</span><span class="token string">"Dev"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    services<span class="token punctuation">.</span>AddDbContext<span class="token operator">&lt;</span>SchoolDbContext<span class="token operator">&gt;</span><span class="token punctuation">(</span>options <span class="token operator">=</span><span class="token operator">&gt;</span>
	    options<span class="token punctuation">.</span><span class="token function">UseSqlServer</span><span class="token punctuation">(</span>connection<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    services<span class="token punctuation">.</span><span class="token function">AddMvc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">// Register the Swagger generator, defining one or more Swagger documents</span>
    services<span class="token punctuation">.</span><span class="token function">AddSwaggerGen</span><span class="token punctuation">(</span>c <span class="token operator">=</span><span class="token operator">&gt;</span>
    <span class="token punctuation">{</span>
        c<span class="token punctuation">.</span><span class="token function">SwaggerDoc</span><span class="token punctuation">(</span><span class="token string">"v1"</span><span class="token punctuation">,</span> new Info <span class="token punctuation">{</span>
            Version <span class="token operator">=</span> <span class="token string">"v1"</span><span class="token punctuation">,</span>
            Title <span class="token operator">=</span> <span class="token string">"Students API"</span><span class="token punctuation">,</span>
            Description <span class="token operator">=</span> <span class="token string">"A simple example ASP.NET Core Web API"</span><span class="token punctuation">,</span>
            TermsOfService <span class="token operator">=</span> <span class="token string">"None"</span><span class="token punctuation">,</span>
            Contact <span class="token operator">=</span> new Contact <span class="token punctuation">{</span> Name <span class="token operator">=</span> <span class="token string">"Sara Lissette Luis Ibáñez"</span><span class="token punctuation">,</span> Email <span class="token operator">=</span> <span class="token string">"lissette.ibnz@gmail.com"</span><span class="token punctuation">,</span> Url <span class="token operator">=</span> <span class="token string">"https://github.com/LissetteIbnz"</span> <span class="token punctuation">}</span><span class="token punctuation">,</span>
            License <span class="token operator">=</span> new License <span class="token punctuation">{</span> Name <span class="token operator">=</span> <span class="token string">"Use under ISC"</span><span class="token punctuation">,</span> Url <span class="token operator">=</span> <span class="token string">"https://github.com/LissetteIbnz/aspnetcore-api-vuejs-spa/blob/master/LICENSE.md"</span> <span class="token punctuation">}</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token comment">// Set the comments path for the Swagger JSON and UI.</span>
        var basePath <span class="token operator">=</span> AppContext<span class="token punctuation">.</span>BaseDirectory<span class="token punctuation">;</span>
        var xmlPath <span class="token operator">=</span> Path<span class="token punctuation">.</span><span class="token function">Combine</span><span class="token punctuation">(</span>basePath<span class="token punctuation">,</span> <span class="token string">"API.xml"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        c<span class="token punctuation">.</span><span class="token function">IncludeXmlComments</span><span class="token punctuation">(</span>xmlPath<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Agregue la instrucción using siguiente para la clase  <code>Info</code>:</p>
<pre class=" language-c"><code class="prism # language-c">using Swashbuckle<span class="token punctuation">.</span>AspNetCore<span class="token punctuation">.</span>Swagger<span class="token punctuation">;</span>
</code></pre>
<p>En el método  <code>Configure</code>  de  <em>Startup.cs</em>, habilite el middleware para servir el documento JSON generado y la IU de Swagger:</p>
<pre class=" language-c"><code class="prism # language-c">public <span class="token keyword">void</span> <span class="token function">Configure</span><span class="token punctuation">(</span>IApplicationBuilder app<span class="token punctuation">)</span>
<span class="token punctuation">{</span>
    <span class="token comment">// Enable middleware to serve generated Swagger as a JSON endpoint.</span>
    app<span class="token punctuation">.</span><span class="token function">UseSwagger</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">// Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.), specifying the Swagger JSON endpoint.</span>
    app<span class="token punctuation">.</span><span class="token function">UseSwaggerUI</span><span class="token punctuation">(</span>c <span class="token operator">=</span><span class="token operator">&gt;</span>
    <span class="token punctuation">{</span>
        c<span class="token punctuation">.</span><span class="token function">SwaggerEndpoint</span><span class="token punctuation">(</span><span class="token string">"/swagger/v1/swagger.json"</span><span class="token punctuation">,</span> <span class="token string">"My API V1"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    app<span class="token punctuation">.</span><span class="token function">UseMvc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Este documento le guía por la interfaz de usuario de Swagger, que se puede visualizar yendo a <code>http://localhost:&lt;random_port&gt;/swagger</code>:</p>
<p><a href="https://docs.microsoft.com/es-es/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio">Más info sobre la configuración</a></p>
<p><a href="//anexsoft.com/p/188/netcore-api-vuejs-spa-configurando-nuestra-api-con-entity-framework">//anexsoft.com/p/188/netcore-api-vuejs-spa-configurando-nuestra-api-con-entity-framework</a></p>
<h3 id="model">Model</h3>
<blockquote>
<p>Representa nuestro modelos de la capa de dominio, es decir las tablas mapeadas a clases.</p>
</blockquote>
<p><img src="https://docs.microsoft.com/es-es/aspnet/core/data/ef-mvc/intro/_static/data-model-diagram.png" alt="enter image description here"></p>
<p>Para crear nuestro modelo, agregaremos a la solución un nuevo proyecto de <strong>Biblioteca de clases (.Net Core)</strong>.<br>
Seguidamente, crearemos tres clases para nuestras tres entidades: <em>Course, Enrollment y Student</em>.</p>
<p><em>Model.Course.cs</em></p>
<pre class=" language-c"><code class="prism # language-c">using System<span class="token punctuation">.</span>Collections<span class="token punctuation">.</span>Generic<span class="token punctuation">;</span>
using System<span class="token punctuation">.</span>ComponentModel<span class="token punctuation">.</span>DataAnnotations<span class="token punctuation">.</span>Schema<span class="token punctuation">;</span>

namespace Model
<span class="token punctuation">{</span>
    public class Course
    <span class="token punctuation">{</span>
        <span class="token punctuation">[</span><span class="token function">DatabaseGenerated</span><span class="token punctuation">(</span>DatabaseGeneratedOption<span class="token punctuation">.</span>None<span class="token punctuation">)</span><span class="token punctuation">]</span>
        public <span class="token keyword">int</span> CourseID <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
        public string Title <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
        public <span class="token keyword">int</span> Credits <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>

        public ICollection<span class="token operator">&lt;</span>Enrollment<span class="token operator">&gt;</span> Enrollments <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

</code></pre>
<p><em>Model.Enrollment.cs</em></p>
<pre class=" language-c"><code class="prism # language-c">namespace Model
<span class="token punctuation">{</span>
    public <span class="token keyword">enum</span> Grade
    <span class="token punctuation">{</span>
        A<span class="token punctuation">,</span> B<span class="token punctuation">,</span> C<span class="token punctuation">,</span> D<span class="token punctuation">,</span> F
    <span class="token punctuation">}</span>

    public class Enrollment
    <span class="token punctuation">{</span>
        public <span class="token keyword">int</span> EnrollmentID <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
        public <span class="token keyword">int</span> CourseID <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
        public <span class="token keyword">int</span> StudentID <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
        public Grade<span class="token operator">?</span> Grade <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>

        public Course Course <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
        public Student Student <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

</code></pre>
<p><em>Model.Student.cs</em></p>
<pre class=" language-c"><code class="prism # language-c">using System<span class="token punctuation">;</span>
using System<span class="token punctuation">.</span>Collections<span class="token punctuation">.</span>Generic<span class="token punctuation">;</span>

namespace Model
<span class="token punctuation">{</span>
    public class Student
    <span class="token punctuation">{</span>
        public <span class="token keyword">int</span> StudentId <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
        public string Name <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
        public string LastName <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
        public string Bio <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
        public DateTime EnrollmentDate <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>

        public ICollection<span class="token operator">&lt;</span>Enrollment<span class="token operator">&gt;</span> Enrollments <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

</code></pre>
<blockquote>
<p>De forma predeterminada, Entity Framework interpreta como la clave principal una propiedad que se denomine <code>ID</code> o <code>classnameID</code>.<br>
La propiedad  <code>Enrollments</code>  es una propiedad de navegación.  Las propiedades de navegación contienen otras entidades relacionadas con esta entidad.<br>
Si una propiedad de navegación puede contener varias entidades (como en las relaciones de varios a varios o uno a varios), su tipo debe ser una lista a la que se puedan agregar las entradas, eliminarlas y actualizarlas, como  <code>ICollection&lt;T&gt;</code>.  Puede especificar  <code>ICollection&lt;T&gt;</code>  o un tipo como  <code>List&lt;T&gt;</code>  o  <code>HashSet&lt;T&gt;</code>.  Si especifica  <code>ICollection&lt;T&gt;</code>, EF crea una colección  <code>HashSet&lt;T&gt;</code>  de forma predeterminada.</p>
</blockquote>
<h3 id="persistence">Persistence</h3>
<p>Agregaremos un nuevo proyecto tipo <strong>Biblioteca de clases (.Net Core)</strong> y seguidamente, crearemos la clase que contendrá nuestro <a href="https://docs.microsoft.com/es-es/aspnet/core/data/ef-mvc/intro#create-the-database-context">contexto de base de datos</a><br>
Nuestra clase que será encargada de manejar las migraciones y la conexión a la base de datos.Para hacer funcionar esta parte debemos agregar los siguientes package del nuget y luego crear nuestro DbContext.</p>
<ul>
<li>EntityFrameworkCore.SqlServer</li>
<li>EntityFrameworkCore.Tools</li>
</ul>
<p><em>Persistence.SchoolDbContext.cs</em></p>
<pre class=" language-c"><code class="prism # language-c">using Microsoft<span class="token punctuation">.</span>EntityFrameworkCore<span class="token punctuation">;</span>
using Model<span class="token punctuation">;</span>

namespace Persistence
<span class="token punctuation">{</span>
    public class SchoolDbContext <span class="token punctuation">:</span> DbContext
    <span class="token punctuation">{</span>
        public <span class="token function">SchoolDbContext</span><span class="token punctuation">(</span>DbContextOptions<span class="token operator">&lt;</span>SchoolDbContext<span class="token operator">&gt;</span> options<span class="token punctuation">)</span> <span class="token punctuation">:</span> <span class="token function">base</span><span class="token punctuation">(</span>options<span class="token punctuation">)</span>
        <span class="token punctuation">{</span>
        <span class="token punctuation">}</span>

        public DbSet<span class="token operator">&lt;</span>Course<span class="token operator">&gt;</span> Courses <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
        public DbSet<span class="token operator">&lt;</span>Enrollment<span class="token operator">&gt;</span> Enrollments <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>
        public DbSet<span class="token operator">&lt;</span>Student<span class="token operator">&gt;</span> Students <span class="token punctuation">{</span> get<span class="token punctuation">;</span> set<span class="token punctuation">;</span> <span class="token punctuation">}</span>

        <span class="token comment">/**
         * El comportamiento predeterminado de EF es crear los nombres de las tablas en plural.
         * Se invalidará este comportamiento mediante la especificación de nombres tabla en 
         * singular en DbContext.
         */</span>
        protected override <span class="token keyword">void</span> <span class="token function">OnModelCreating</span><span class="token punctuation">(</span>ModelBuilder modelBuilder<span class="token punctuation">)</span>
        <span class="token punctuation">{</span>
            modelBuilder<span class="token punctuation">.</span>Entity<span class="token operator">&lt;</span>Course<span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">ToTable</span><span class="token punctuation">(</span><span class="token string">"Course"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            modelBuilder<span class="token punctuation">.</span>Entity<span class="token operator">&lt;</span>Enrollment<span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">ToTable</span><span class="token punctuation">(</span><span class="token string">"Enrollment"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            modelBuilder<span class="token punctuation">.</span>Entity<span class="token operator">&lt;</span>Student<span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">ToTable</span><span class="token punctuation">(</span><span class="token string">"Student"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

</code></pre>
<blockquote>
<p>Este código crea una propiedad  <code>DbSet</code>  para cada conjunto de entidades.  En la terminología de Entity Framework, un conjunto de entidades suele corresponderse con una tabla de base de datos, mientras que una entidad lo hace con una fila de la tabla.</p>
</blockquote>
<h4 id="registro-del-contexto-con-inserción-de-dependencias">Registro del contexto con inserción de dependencias</h4>
<p>No olvidemos registrar dicha dependencia en nuestro StartUp para que este disponible para todo el proyecto.</p>
<pre class=" language-c"><code class="prism # language-c">public <span class="token keyword">void</span> <span class="token function">ConfigureServices</span><span class="token punctuation">(</span>IServiceCollection services<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    var connection <span class="token operator">=</span> Configuration<span class="token punctuation">.</span><span class="token function">GetConnectionString</span><span class="token punctuation">(</span><span class="token string">"Dev"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    services<span class="token punctuation">.</span>AddDbContext<span class="token operator">&lt;</span>SchoolDbContext<span class="token operator">&gt;</span><span class="token punctuation">(</span>options <span class="token operator">=</span><span class="token operator">&gt;</span> options<span class="token punctuation">.</span><span class="token function">UseSqlServer</span><span class="token punctuation">(</span>connection<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    services<span class="token punctuation">.</span><span class="token function">AddMvc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p><em>API.appsettings.json</em></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
  <span class="token string">"ConnectionStrings"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
    <span class="token string">"DefaultConnection"</span><span class="token punctuation">:</span> <span class="token string">"Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity1;Trusted_Connection=True;MultipleActiveResultSets=true"</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>
  <span class="token string">"Logging"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
    <span class="token string">"IncludeScopes"</span><span class="token punctuation">:</span> <span class="token boolean">false</span><span class="token punctuation">,</span>
    <span class="token string">"LogLevel"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
      <span class="token string">"Default"</span><span class="token punctuation">:</span> <span class="token string">"Warning"</span>
    <span class="token punctuation">}</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h4 id="agregue-código-para-inicializar-la-base-de-datos-con-datos-de-prueba">Agregue código para inicializar la base de datos con datos de prueba</h4>
<p>Entity Framework creará una base de datos vacía por usted.  En esta sección, escribirá un método que se llama después de crear la base de datos para rellenarla con datos de prueba.</p>
<p>Aquí usará el método  <code>EnsureCreated</code>  para crear automáticamente la base de datos.</p>
<p>En la carpeta  <em>Data</em>, cree un archivo de clase denominado  <em>DbInitializer.cs</em>  y reemplace el código de plantilla con el código siguiente, que hace que se cree una base de datos cuando es necesario y carga datos de prueba en la nueva base de datos.</p>
<p><em>Persistence.Data.DbInitializer.cs</em></p>
<pre class=" language-c"><code class="prism # language-c">using Model<span class="token punctuation">;</span>
using System<span class="token punctuation">;</span>
using System<span class="token punctuation">.</span>Collections<span class="token punctuation">.</span>Generic<span class="token punctuation">;</span>
using System<span class="token punctuation">.</span>Linq<span class="token punctuation">;</span>
using System<span class="token punctuation">.</span>Text<span class="token punctuation">;</span>

namespace Persistence<span class="token punctuation">.</span>Data
<span class="token punctuation">{</span>
    public class DbInitializer
    <span class="token punctuation">{</span>
        public <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">Initialize</span><span class="token punctuation">(</span>SchoolDbContext context<span class="token punctuation">)</span>
        <span class="token punctuation">{</span>
            context<span class="token punctuation">.</span>Database<span class="token punctuation">.</span><span class="token function">EnsureCreated</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            <span class="token comment">// Look for any students.</span>
            <span class="token keyword">if</span> <span class="token punctuation">(</span>context<span class="token punctuation">.</span>Students<span class="token punctuation">.</span><span class="token function">Any</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
            <span class="token punctuation">{</span>
                <span class="token keyword">return</span><span class="token punctuation">;</span>   <span class="token comment">// DB has been seeded</span>
            <span class="token punctuation">}</span>

            var students <span class="token operator">=</span> new Student<span class="token punctuation">[</span><span class="token punctuation">]</span>
            <span class="token punctuation">{</span>
            new Student<span class="token punctuation">{</span>Name<span class="token operator">=</span><span class="token string">"Carson"</span><span class="token punctuation">,</span>LastName<span class="token operator">=</span><span class="token string">"Alexander"</span><span class="token punctuation">,</span>EnrollmentDate<span class="token operator">=</span>DateTime<span class="token punctuation">.</span><span class="token function">Parse</span><span class="token punctuation">(</span><span class="token string">"2005-09-01"</span><span class="token punctuation">)</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Student<span class="token punctuation">{</span>Name<span class="token operator">=</span><span class="token string">"Meredith"</span><span class="token punctuation">,</span>LastName<span class="token operator">=</span><span class="token string">"Alonso"</span><span class="token punctuation">,</span>EnrollmentDate<span class="token operator">=</span>DateTime<span class="token punctuation">.</span><span class="token function">Parse</span><span class="token punctuation">(</span><span class="token string">"2002-09-01"</span><span class="token punctuation">)</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Student<span class="token punctuation">{</span>Name<span class="token operator">=</span><span class="token string">"Arturo"</span><span class="token punctuation">,</span>LastName<span class="token operator">=</span><span class="token string">"Anand"</span><span class="token punctuation">,</span>EnrollmentDate<span class="token operator">=</span>DateTime<span class="token punctuation">.</span><span class="token function">Parse</span><span class="token punctuation">(</span><span class="token string">"2003-09-01"</span><span class="token punctuation">)</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Student<span class="token punctuation">{</span>Name<span class="token operator">=</span><span class="token string">"Gytis"</span><span class="token punctuation">,</span>LastName<span class="token operator">=</span><span class="token string">"Barzdukas"</span><span class="token punctuation">,</span>EnrollmentDate<span class="token operator">=</span>DateTime<span class="token punctuation">.</span><span class="token function">Parse</span><span class="token punctuation">(</span><span class="token string">"2002-09-01"</span><span class="token punctuation">)</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Student<span class="token punctuation">{</span>Name<span class="token operator">=</span><span class="token string">"Yan"</span><span class="token punctuation">,</span>LastName<span class="token operator">=</span><span class="token string">"Li"</span><span class="token punctuation">,</span>EnrollmentDate<span class="token operator">=</span>DateTime<span class="token punctuation">.</span><span class="token function">Parse</span><span class="token punctuation">(</span><span class="token string">"2002-09-01"</span><span class="token punctuation">)</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Student<span class="token punctuation">{</span>Name<span class="token operator">=</span><span class="token string">"Peggy"</span><span class="token punctuation">,</span>LastName<span class="token operator">=</span><span class="token string">"Justice"</span><span class="token punctuation">,</span>EnrollmentDate<span class="token operator">=</span>DateTime<span class="token punctuation">.</span><span class="token function">Parse</span><span class="token punctuation">(</span><span class="token string">"2001-09-01"</span><span class="token punctuation">)</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Student<span class="token punctuation">{</span>Name<span class="token operator">=</span><span class="token string">"Laura"</span><span class="token punctuation">,</span>LastName<span class="token operator">=</span><span class="token string">"Norman"</span><span class="token punctuation">,</span>EnrollmentDate<span class="token operator">=</span>DateTime<span class="token punctuation">.</span><span class="token function">Parse</span><span class="token punctuation">(</span><span class="token string">"2003-09-01"</span><span class="token punctuation">)</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Student<span class="token punctuation">{</span>Name<span class="token operator">=</span><span class="token string">"Nino"</span><span class="token punctuation">,</span>LastName<span class="token operator">=</span><span class="token string">"Olivetto"</span><span class="token punctuation">,</span>EnrollmentDate<span class="token operator">=</span>DateTime<span class="token punctuation">.</span><span class="token function">Parse</span><span class="token punctuation">(</span><span class="token string">"2005-09-01"</span><span class="token punctuation">)</span><span class="token punctuation">}</span>
            <span class="token punctuation">}</span><span class="token punctuation">;</span>
            <span class="token function">foreach</span> <span class="token punctuation">(</span>Student s in students<span class="token punctuation">)</span>
            <span class="token punctuation">{</span>
                context<span class="token punctuation">.</span>Students<span class="token punctuation">.</span><span class="token function">Add</span><span class="token punctuation">(</span>s<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            context<span class="token punctuation">.</span><span class="token function">SaveChanges</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            var courses <span class="token operator">=</span> new Course<span class="token punctuation">[</span><span class="token punctuation">]</span>
            <span class="token punctuation">{</span>
            new Course<span class="token punctuation">{</span>CourseID<span class="token operator">=</span><span class="token number">1050</span><span class="token punctuation">,</span>Title<span class="token operator">=</span><span class="token string">"Chemistry"</span><span class="token punctuation">,</span>Credits<span class="token operator">=</span><span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Course<span class="token punctuation">{</span>CourseID<span class="token operator">=</span><span class="token number">4022</span><span class="token punctuation">,</span>Title<span class="token operator">=</span><span class="token string">"Microeconomics"</span><span class="token punctuation">,</span>Credits<span class="token operator">=</span><span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Course<span class="token punctuation">{</span>CourseID<span class="token operator">=</span><span class="token number">4041</span><span class="token punctuation">,</span>Title<span class="token operator">=</span><span class="token string">"Macroeconomics"</span><span class="token punctuation">,</span>Credits<span class="token operator">=</span><span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Course<span class="token punctuation">{</span>CourseID<span class="token operator">=</span><span class="token number">1045</span><span class="token punctuation">,</span>Title<span class="token operator">=</span><span class="token string">"Calculus"</span><span class="token punctuation">,</span>Credits<span class="token operator">=</span><span class="token number">4</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Course<span class="token punctuation">{</span>CourseID<span class="token operator">=</span><span class="token number">3141</span><span class="token punctuation">,</span>Title<span class="token operator">=</span><span class="token string">"Trigonometry"</span><span class="token punctuation">,</span>Credits<span class="token operator">=</span><span class="token number">4</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Course<span class="token punctuation">{</span>CourseID<span class="token operator">=</span><span class="token number">2021</span><span class="token punctuation">,</span>Title<span class="token operator">=</span><span class="token string">"Composition"</span><span class="token punctuation">,</span>Credits<span class="token operator">=</span><span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Course<span class="token punctuation">{</span>CourseID<span class="token operator">=</span><span class="token number">2042</span><span class="token punctuation">,</span>Title<span class="token operator">=</span><span class="token string">"Literature"</span><span class="token punctuation">,</span>Credits<span class="token operator">=</span><span class="token number">4</span><span class="token punctuation">}</span>
            <span class="token punctuation">}</span><span class="token punctuation">;</span>
            <span class="token function">foreach</span> <span class="token punctuation">(</span>Course c in courses<span class="token punctuation">)</span>
            <span class="token punctuation">{</span>
                context<span class="token punctuation">.</span>Courses<span class="token punctuation">.</span><span class="token function">Add</span><span class="token punctuation">(</span>c<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            context<span class="token punctuation">.</span><span class="token function">SaveChanges</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            var enrollments <span class="token operator">=</span> new Enrollment<span class="token punctuation">[</span><span class="token punctuation">]</span>
            <span class="token punctuation">{</span>
            new Enrollment<span class="token punctuation">{</span>StudentID<span class="token operator">=</span><span class="token number">1</span><span class="token punctuation">,</span>CourseID<span class="token operator">=</span><span class="token number">1050</span><span class="token punctuation">,</span>Grade<span class="token operator">=</span>Grade<span class="token punctuation">.</span>A<span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Enrollment<span class="token punctuation">{</span>StudentID<span class="token operator">=</span><span class="token number">1</span><span class="token punctuation">,</span>CourseID<span class="token operator">=</span><span class="token number">4022</span><span class="token punctuation">,</span>Grade<span class="token operator">=</span>Grade<span class="token punctuation">.</span>C<span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Enrollment<span class="token punctuation">{</span>StudentID<span class="token operator">=</span><span class="token number">1</span><span class="token punctuation">,</span>CourseID<span class="token operator">=</span><span class="token number">4041</span><span class="token punctuation">,</span>Grade<span class="token operator">=</span>Grade<span class="token punctuation">.</span>B<span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Enrollment<span class="token punctuation">{</span>StudentID<span class="token operator">=</span><span class="token number">2</span><span class="token punctuation">,</span>CourseID<span class="token operator">=</span><span class="token number">1045</span><span class="token punctuation">,</span>Grade<span class="token operator">=</span>Grade<span class="token punctuation">.</span>B<span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Enrollment<span class="token punctuation">{</span>StudentID<span class="token operator">=</span><span class="token number">2</span><span class="token punctuation">,</span>CourseID<span class="token operator">=</span><span class="token number">3141</span><span class="token punctuation">,</span>Grade<span class="token operator">=</span>Grade<span class="token punctuation">.</span>F<span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Enrollment<span class="token punctuation">{</span>StudentID<span class="token operator">=</span><span class="token number">2</span><span class="token punctuation">,</span>CourseID<span class="token operator">=</span><span class="token number">2021</span><span class="token punctuation">,</span>Grade<span class="token operator">=</span>Grade<span class="token punctuation">.</span>F<span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Enrollment<span class="token punctuation">{</span>StudentID<span class="token operator">=</span><span class="token number">3</span><span class="token punctuation">,</span>CourseID<span class="token operator">=</span><span class="token number">1050</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Enrollment<span class="token punctuation">{</span>StudentID<span class="token operator">=</span><span class="token number">4</span><span class="token punctuation">,</span>CourseID<span class="token operator">=</span><span class="token number">1050</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Enrollment<span class="token punctuation">{</span>StudentID<span class="token operator">=</span><span class="token number">4</span><span class="token punctuation">,</span>CourseID<span class="token operator">=</span><span class="token number">4022</span><span class="token punctuation">,</span>Grade<span class="token operator">=</span>Grade<span class="token punctuation">.</span>F<span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Enrollment<span class="token punctuation">{</span>StudentID<span class="token operator">=</span><span class="token number">5</span><span class="token punctuation">,</span>CourseID<span class="token operator">=</span><span class="token number">4041</span><span class="token punctuation">,</span>Grade<span class="token operator">=</span>Grade<span class="token punctuation">.</span>C<span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Enrollment<span class="token punctuation">{</span>StudentID<span class="token operator">=</span><span class="token number">6</span><span class="token punctuation">,</span>CourseID<span class="token operator">=</span><span class="token number">1045</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
            new Enrollment<span class="token punctuation">{</span>StudentID<span class="token operator">=</span><span class="token number">7</span><span class="token punctuation">,</span>CourseID<span class="token operator">=</span><span class="token number">3141</span><span class="token punctuation">,</span>Grade<span class="token operator">=</span>Grade<span class="token punctuation">.</span>A<span class="token punctuation">}</span><span class="token punctuation">,</span>
            <span class="token punctuation">}</span><span class="token punctuation">;</span>
            <span class="token function">foreach</span> <span class="token punctuation">(</span>Enrollment e in enrollments<span class="token punctuation">)</span>
            <span class="token punctuation">{</span>
                context<span class="token punctuation">.</span>Enrollments<span class="token punctuation">.</span><span class="token function">Add</span><span class="token punctuation">(</span>e<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            context<span class="token punctuation">.</span><span class="token function">SaveChanges</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

</code></pre>
<p>En  <em>Program.cs</em>, modifique el método  <code>Main</code>  para que haga lo siguiente al iniciar la aplicación:</p>
<ul>
<li>Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</li>
<li>Llamar al método de inicialización, pasándolo al contexto.</li>
<li>Eliminar el contexto cuando el método de inicialización haya finalizado.</li>
</ul>
<p><em>API.Program.cs</em></p>
<pre class=" language-c"><code class="prism # language-c">using Microsoft<span class="token punctuation">.</span>AspNetCore<span class="token punctuation">;</span>
using Microsoft<span class="token punctuation">.</span>AspNetCore<span class="token punctuation">.</span>Hosting<span class="token punctuation">;</span>
using Microsoft<span class="token punctuation">.</span>Extensions<span class="token punctuation">.</span>DependencyInjection<span class="token punctuation">;</span>
using Microsoft<span class="token punctuation">.</span>Extensions<span class="token punctuation">.</span>Logging<span class="token punctuation">;</span>
using Persistence<span class="token punctuation">;</span>
using Persistence<span class="token punctuation">.</span>Data<span class="token punctuation">;</span>
using System<span class="token punctuation">;</span>

namespace API
<span class="token punctuation">{</span>
    public class Program
    <span class="token punctuation">{</span>
        public <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">Main</span><span class="token punctuation">(</span>string<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span>
        <span class="token punctuation">{</span>
            var host <span class="token operator">=</span> <span class="token function">BuildWebHost</span><span class="token punctuation">(</span>args<span class="token punctuation">)</span><span class="token punctuation">;</span>

            <span class="token function">using</span> <span class="token punctuation">(</span>var scope <span class="token operator">=</span> host<span class="token punctuation">.</span>Services<span class="token punctuation">.</span><span class="token function">CreateScope</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
            <span class="token punctuation">{</span>
                var services <span class="token operator">=</span> scope<span class="token punctuation">.</span>ServiceProvider<span class="token punctuation">;</span>
                try
                <span class="token punctuation">{</span>
                    var context <span class="token operator">=</span> services<span class="token punctuation">.</span>GetRequiredService<span class="token operator">&lt;</span>SchoolDbContext<span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                    DbInitializer<span class="token punctuation">.</span><span class="token function">Initialize</span><span class="token punctuation">(</span>context<span class="token punctuation">)</span><span class="token punctuation">;</span>
                <span class="token punctuation">}</span>
                <span class="token function">catch</span> <span class="token punctuation">(</span>Exception ex<span class="token punctuation">)</span>
                <span class="token punctuation">{</span>
                    var logger <span class="token operator">=</span> services<span class="token punctuation">.</span>GetRequiredService<span class="token operator">&lt;</span>ILogger<span class="token operator">&lt;</span>Program<span class="token operator">&gt;&gt;</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                    logger<span class="token punctuation">.</span><span class="token function">LogError</span><span class="token punctuation">(</span>ex<span class="token punctuation">,</span> <span class="token string">"An error occurred while seeding the database."</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                <span class="token punctuation">}</span>
            <span class="token punctuation">}</span>

            host<span class="token punctuation">.</span><span class="token function">Run</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        public <span class="token keyword">static</span> IWebHost <span class="token function">BuildWebHost</span><span class="token punctuation">(</span>string<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token operator">=</span><span class="token operator">&gt;</span>
            WebHost<span class="token punctuation">.</span><span class="token function">CreateDefaultBuilder</span><span class="token punctuation">(</span>args<span class="token punctuation">)</span>
                <span class="token punctuation">.</span>UseStartup<span class="token operator">&lt;</span>Startup<span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                <span class="token punctuation">.</span><span class="token function">Build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

</code></pre>
<h3 id="api">API</h3>
<p>Nuestra capa REST que implementa los recursos a exponer como API.</p>
<pre class=" language-c"><code class="prism # language-c">namespace api<span class="token punctuation">.</span>Controllers
<span class="token punctuation">{</span>
    <span class="token punctuation">[</span><span class="token function">Route</span><span class="token punctuation">(</span><span class="token string">"[controller]"</span><span class="token punctuation">)</span><span class="token punctuation">]</span>
    public class StudentController <span class="token punctuation">:</span> Controller
    <span class="token punctuation">{</span>
        private readonly IStudentService _studentService<span class="token punctuation">;</span>

        public <span class="token function">StudentController</span><span class="token punctuation">(</span>IStudentService studentService<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            _studentService <span class="token operator">=</span> studentService<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token comment">// GET api/values</span>
        <span class="token punctuation">[</span>HttpGet<span class="token punctuation">]</span>
        public IActionResult <span class="token function">Get</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token function">Ok</span><span class="token punctuation">(</span>
                _studentService<span class="token punctuation">.</span><span class="token function">GetAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
            <span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token comment">// GET api/values/5</span>
        <span class="token punctuation">[</span><span class="token function">HttpGet</span><span class="token punctuation">(</span><span class="token string">"{id}"</span><span class="token punctuation">)</span><span class="token punctuation">]</span>
        public IActionResult <span class="token function">Get</span><span class="token punctuation">(</span><span class="token keyword">int</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token function">Ok</span><span class="token punctuation">(</span>
                _studentService<span class="token punctuation">.</span><span class="token function">Get</span><span class="token punctuation">(</span>id<span class="token punctuation">)</span>
            <span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token comment">// POST api/values</span>
        <span class="token punctuation">[</span>HttpPost<span class="token punctuation">]</span>
        public IActionResult <span class="token function">Post</span><span class="token punctuation">(</span><span class="token punctuation">[</span>FromBody<span class="token punctuation">]</span> Student model<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token function">Ok</span><span class="token punctuation">(</span>
                _studentService<span class="token punctuation">.</span><span class="token function">Add</span><span class="token punctuation">(</span>model<span class="token punctuation">)</span>
            <span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token comment">// PUT api/values/5</span>
        <span class="token punctuation">[</span>HttpPut<span class="token punctuation">]</span>
        public IActionResult <span class="token function">Put</span><span class="token punctuation">(</span><span class="token punctuation">[</span>FromBody<span class="token punctuation">]</span> Student model<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token function">Ok</span><span class="token punctuation">(</span>
                _studentService<span class="token punctuation">.</span><span class="token function">Add</span><span class="token punctuation">(</span>model<span class="token punctuation">)</span>
            <span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token comment">// DELETE api/values/5</span>
        <span class="token punctuation">[</span><span class="token function">HttpDelete</span><span class="token punctuation">(</span><span class="token string">"{id}"</span><span class="token punctuation">)</span><span class="token punctuation">]</span>
        public IActionResult <span class="token function">Delete</span><span class="token punctuation">(</span><span class="token keyword">int</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">return</span> <span class="token function">Ok</span><span class="token punctuation">(</span>
                _studentService<span class="token punctuation">.</span><span class="token function">Delete</span><span class="token punctuation">(</span>id<span class="token punctuation">)</span>
            <span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Con respecto a las rutas no hemos escrito ni unas reglas, así que estamos usando el ruteo REST. En un proyecto real prefiero modificar la escritura desde el StartUp tal como lo genera un proyecto MVC.</p>
<pre class=" language-c"><code class="prism # language-c">app<span class="token punctuation">.</span><span class="token function">UseMvc</span><span class="token punctuation">(</span>routes <span class="token operator">=</span><span class="token operator">&gt;</span>
<span class="token punctuation">{</span>
    routes<span class="token punctuation">.</span><span class="token function">MapRoute</span><span class="token punctuation">(</span>
        name<span class="token punctuation">:</span> <span class="token string">"default"</span><span class="token punctuation">,</span>
        template<span class="token punctuation">:</span> <span class="token string">"{controller=Home}/{action=Index}/{id?}"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<h3 id="services">Services</h3>
<p>Nuestros servicios que vamos a declarar que se encargarán de realizar las consultas a la base de datos.</p>
<pre class=" language-c"><code class="prism # language-c">namespace Service
<span class="token punctuation">{</span>
    public interface IStudentService
    <span class="token punctuation">{</span>
        IEnumerable<span class="token operator">&lt;</span>Student<span class="token operator">&gt;</span> <span class="token function">GetAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        bool <span class="token function">Add</span><span class="token punctuation">(</span>Student model<span class="token punctuation">)</span><span class="token punctuation">;</span>
        bool <span class="token function">Delete</span><span class="token punctuation">(</span><span class="token keyword">int</span> id<span class="token punctuation">)</span><span class="token punctuation">;</span>
        bool <span class="token function">Update</span><span class="token punctuation">(</span>Student model<span class="token punctuation">)</span><span class="token punctuation">;</span>
        Student <span class="token function">Get</span><span class="token punctuation">(</span><span class="token keyword">int</span> id<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    public class StudentService <span class="token punctuation">:</span> IStudentService
    <span class="token punctuation">{</span>
        private readonly StudentDbContext _studentDbContext<span class="token punctuation">;</span>

        public <span class="token function">StudentService</span><span class="token punctuation">(</span>
            StudentDbContext studentDbContext
        <span class="token punctuation">)</span> <span class="token punctuation">{</span>
            _studentDbContext <span class="token operator">=</span> studentDbContext<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        public IEnumerable<span class="token operator">&lt;</span>Student<span class="token operator">&gt;</span> <span class="token function">GetAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            var result <span class="token operator">=</span> new List<span class="token operator">&lt;</span>Student<span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            try
            <span class="token punctuation">{</span>
                result <span class="token operator">=</span> _studentDbContext<span class="token punctuation">.</span>Student<span class="token punctuation">.</span><span class="token function">ToList</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            <span class="token function">catch</span> <span class="token punctuation">(</span>System<span class="token punctuation">.</span>Exception<span class="token punctuation">)</span>
            <span class="token punctuation">{</span>
                
            <span class="token punctuation">}</span>

            <span class="token keyword">return</span> result<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        public Student <span class="token function">Get</span><span class="token punctuation">(</span><span class="token keyword">int</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            var result <span class="token operator">=</span> new <span class="token function">Student</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

            try
            <span class="token punctuation">{</span>
                result <span class="token operator">=</span> _studentDbContext<span class="token punctuation">.</span>Student<span class="token punctuation">.</span><span class="token function">Single</span><span class="token punctuation">(</span>x <span class="token operator">=</span><span class="token operator">&gt;</span> x<span class="token punctuation">.</span>StudentId <span class="token operator">==</span> id<span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            <span class="token function">catch</span> <span class="token punctuation">(</span>System<span class="token punctuation">.</span>Exception<span class="token punctuation">)</span>
            <span class="token punctuation">{</span>

            <span class="token punctuation">}</span>

            <span class="token keyword">return</span> result<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        public bool <span class="token function">Add</span><span class="token punctuation">(</span>Student model<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            try
            <span class="token punctuation">{</span>
                _studentDbContext<span class="token punctuation">.</span><span class="token function">Add</span><span class="token punctuation">(</span>model<span class="token punctuation">)</span><span class="token punctuation">;</span>
                _studentDbContext<span class="token punctuation">.</span><span class="token function">SaveChanges</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            <span class="token function">catch</span> <span class="token punctuation">(</span>System<span class="token punctuation">.</span>Exception<span class="token punctuation">)</span>
            <span class="token punctuation">{</span>
                <span class="token keyword">return</span> false<span class="token punctuation">;</span>
            <span class="token punctuation">}</span>

            <span class="token keyword">return</span> true<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        public bool <span class="token function">Update</span><span class="token punctuation">(</span>Student model<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            try
            <span class="token punctuation">{</span>
                var originalModel <span class="token operator">=</span> _studentDbContext<span class="token punctuation">.</span>Student<span class="token punctuation">.</span><span class="token function">Single</span><span class="token punctuation">(</span>x <span class="token operator">=</span><span class="token operator">&gt;</span>
                    x<span class="token punctuation">.</span>StudentId <span class="token operator">==</span> model<span class="token punctuation">.</span>StudentId
                <span class="token punctuation">)</span><span class="token punctuation">;</span>

                originalModel<span class="token punctuation">.</span>Name <span class="token operator">=</span> model<span class="token punctuation">.</span>Name<span class="token punctuation">;</span>
                originalModel<span class="token punctuation">.</span>LastName <span class="token operator">=</span> model<span class="token punctuation">.</span>LastName<span class="token punctuation">;</span>
                originalModel<span class="token punctuation">.</span>Bio <span class="token operator">=</span> model<span class="token punctuation">.</span>Bio<span class="token punctuation">;</span>

                _studentDbContext<span class="token punctuation">.</span><span class="token function">Update</span><span class="token punctuation">(</span>originalModel<span class="token punctuation">)</span><span class="token punctuation">;</span>
                _studentDbContext<span class="token punctuation">.</span><span class="token function">SaveChanges</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            <span class="token function">catch</span> <span class="token punctuation">(</span>System<span class="token punctuation">.</span>Exception<span class="token punctuation">)</span>
            <span class="token punctuation">{</span>
                <span class="token keyword">return</span> false<span class="token punctuation">;</span>
            <span class="token punctuation">}</span>

            <span class="token keyword">return</span> true<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        public bool <span class="token function">Delete</span><span class="token punctuation">(</span><span class="token keyword">int</span> id<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            try
            <span class="token punctuation">{</span>
                _studentDbContext<span class="token punctuation">.</span><span class="token function">Entry</span><span class="token punctuation">(</span>new Student <span class="token punctuation">{</span> StudentId <span class="token operator">=</span> id <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span>State <span class="token operator">=</span> EntityState<span class="token punctuation">.</span>Deleted<span class="token punctuation">;</span> <span class="token punctuation">;</span>
                _studentDbContext<span class="token punctuation">.</span><span class="token function">SaveChanges</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            <span class="token function">catch</span> <span class="token punctuation">(</span>System<span class="token punctuation">.</span>Exception<span class="token punctuation">)</span>
            <span class="token punctuation">{</span>
                <span class="token keyword">return</span> false<span class="token punctuation">;</span>
            <span class="token punctuation">}</span>

            <span class="token keyword">return</span> true<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Resultado carpetas</strong></p>
<pre class=" language-cmd"><code class="prism  language-cmd">└── src
├── components
│ ├── a.vue 
│ └── b.vue 
├── containers 
│ └── index.vue 
├── index.js 
└── jsconfig.json
</code></pre>
<h2 id="cors-y-nuestro-primer-listar-con-element-ui">CORS y nuestro primer listar con Element-UI</h2>
<h3 id="habilitando-cors">Habilitando CORS</h3>
<p>Lo primero que debemos hacer es habilitar CORS para permitir realizar consultas AJAX a otro servidor que no pertenezca a nuestro dominio actual. Para esto nos vamos a StartUp y creamos un Policy o una política.</p>
<pre class=" language-c"><code class="prism # language-c">public <span class="token keyword">void</span> <span class="token function">ConfigureServices</span><span class="token punctuation">(</span>IServiceCollection services<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    var connection <span class="token operator">=</span> Configuration<span class="token punctuation">.</span><span class="token function">GetConnectionString</span><span class="token punctuation">(</span><span class="token string">"Dev"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    services<span class="token punctuation">.</span>AddDbContext<span class="token operator">&lt;</span>StudentDbContext<span class="token operator">&gt;</span><span class="token punctuation">(</span>options <span class="token operator">=</span><span class="token operator">&gt;</span> options<span class="token punctuation">.</span><span class="token function">UseSqlServer</span><span class="token punctuation">(</span>connection<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    services<span class="token punctuation">.</span>AddTransient<span class="token operator">&lt;</span>IStudentService<span class="token punctuation">,</span> StudentService<span class="token operator">&gt;</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    services<span class="token punctuation">.</span><span class="token function">AddCors</span><span class="token punctuation">(</span>options <span class="token operator">=</span><span class="token operator">&gt;</span>
    <span class="token punctuation">{</span>
        options<span class="token punctuation">.</span><span class="token function">AddPolicy</span><span class="token punctuation">(</span><span class="token string">"AllowSpecificOrigin"</span><span class="token punctuation">,</span> builder <span class="token operator">=</span><span class="token operator">&gt;</span>
            builder<span class="token punctuation">.</span><span class="token function">AllowAnyHeader</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                   <span class="token punctuation">.</span><span class="token function">AllowAnyMethod</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
                   <span class="token punctuation">.</span><span class="token function">AllowAnyOrigin</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
        <span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    services<span class="token punctuation">.</span><span class="token function">AddMvc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Lo que estamos diciéndole a esta política es que para cualquiera header, método (put, post, delete, get) u orígen todos estan permitodos. No hay restricciones, es decir que es una política de libre mercado (jaja).</p>
<p>Y luego le decimos a NETCore que queremos usar la política creada.</p>
<pre class=" language-c"><code class="prism # language-c">public <span class="token keyword">void</span> <span class="token function">Configure</span><span class="token punctuation">(</span>IApplicationBuilder app<span class="token punctuation">,</span> IHostingEnvironment env<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span>env<span class="token punctuation">.</span><span class="token function">IsDevelopment</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        app<span class="token punctuation">.</span><span class="token function">UseDeveloperExceptionPage</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>

    app<span class="token punctuation">.</span><span class="token function">UseCors</span><span class="token punctuation">(</span><span class="token string">"AllowSpecificOrigin"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    app<span class="token punctuation">.</span><span class="token function">UseMvc</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<h3 id="creando-nuestro-servicio-en-vuejs-spa">Creando nuestro servicio en VueJs SPA</h3>
<p>En la carpeta src/services vamos a crear nuestro servicio con la finalidad de poder centralizar la lógica y que este sea reutilizable en cualquier parte de nuestro proyecto. Adicionalmente, vamos a registrarlo con Vuex para que sea accesible desde cualquier otro componente evitando la tarea de tener que hacer un import en cada componente.</p>
<p><em>StudentService.js</em></p>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">class</span> <span class="token class-name">StudentService</span> <span class="token punctuation">{</span>
    axios
    baseUrl

    <span class="token function">constructor</span><span class="token punctuation">(</span>axios<span class="token punctuation">,</span> apiUrl<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>axios <span class="token operator">=</span> axios
        <span class="token keyword">this</span><span class="token punctuation">.</span>baseUrl <span class="token operator">=</span> <span class="token template-string"><span class="token string">`</span><span class="token interpolation"><span class="token interpolation-punctuation punctuation">${</span>apiUrl<span class="token interpolation-punctuation punctuation">}</span></span><span class="token string">student`</span></span>
    <span class="token punctuation">}</span>

    <span class="token function">getAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">let</span> self <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> self<span class="token punctuation">.</span>axios<span class="token punctuation">.</span><span class="token keyword">get</span><span class="token punctuation">(</span><span class="token template-string"><span class="token string">`</span><span class="token interpolation"><span class="token interpolation-punctuation punctuation">${</span>self<span class="token punctuation">.</span>baseUrl<span class="token interpolation-punctuation punctuation">}</span></span><span class="token string">`</span></span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

<span class="token keyword">export</span> <span class="token keyword">default</span> StudentService
</code></pre>
<p>Luego en el archivo src/store/services.js declaramos nuestra ruta de la API y lo inyectamos a nuestro constructor.</p>
<p><em>services.js</em></p>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">import</span> Axios <span class="token keyword">from</span> <span class="token string">'axios'</span>
<span class="token keyword">import</span> exampleService <span class="token keyword">from</span> <span class="token string">'../services/ExampleService'</span>
<span class="token keyword">import</span> studentService <span class="token keyword">from</span> <span class="token string">'../services/StudentService'</span>

<span class="token keyword">let</span> apiUrl <span class="token operator">=</span> <span class="token string">'[http://localhost:64550/](http://localhost:64550/)'</span>

<span class="token comment">// Axios Configuration</span>
Axios<span class="token punctuation">.</span>defaults<span class="token punctuation">.</span>headers<span class="token punctuation">.</span>common<span class="token punctuation">.</span>Accept <span class="token operator">=</span> <span class="token string">'application/json'</span>

<span class="token keyword">export</span> <span class="token keyword">default</span> <span class="token punctuation">{</span>
    exampleService<span class="token punctuation">:</span> <span class="token keyword">new</span> <span class="token class-name">exampleService</span><span class="token punctuation">(</span>Axios<span class="token punctuation">)</span><span class="token punctuation">,</span>
    studentService<span class="token punctuation">:</span> <span class="token keyword">new</span> <span class="token class-name">studentService</span><span class="token punctuation">(</span>Axios<span class="token punctuation">,</span> apiUrl<span class="token punctuation">)</span>
<span class="token punctuation">}</span>
</code></pre>
<h3 id="creando-nuestro-componente">Creando nuestro componente</h3>
<p>Creamos una archivo en src/components/student/index.vue y declaramos lo siguiente.</p>
<pre class=" language-js"><code class="prism  language-js"><span class="token operator">&lt;</span>template<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>div<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>h2<span class="token operator">&gt;</span>Estudiantes<span class="token operator">&lt;</span><span class="token operator">/</span>h2<span class="token operator">&gt;</span>
    <span class="token operator">&lt;</span>el<span class="token operator">-</span>table v<span class="token operator">-</span>loading<span class="token operator">=</span><span class="token string">"loading"</span> <span class="token punctuation">:</span>data<span class="token operator">=</span><span class="token string">"data"</span> style<span class="token operator">=</span><span class="token string">"width: 100%"</span><span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>el<span class="token operator">-</span>table<span class="token operator">-</span>column prop<span class="token operator">=</span><span class="token string">"name"</span> label<span class="token operator">=</span><span class="token string">"Nombre"</span> sortable<span class="token operator">&gt;</span><span class="token operator">&lt;</span><span class="token operator">/</span>el<span class="token operator">-</span>table<span class="token operator">-</span>column<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>el<span class="token operator">-</span>table<span class="token operator">-</span>column prop<span class="token operator">=</span><span class="token string">"lastName"</span> label<span class="token operator">=</span><span class="token string">"Apellido"</span> width<span class="token operator">=</span><span class="token string">"200"</span> sortable<span class="token operator">&gt;</span><span class="token operator">&lt;</span><span class="token operator">/</span>el<span class="token operator">-</span>table<span class="token operator">-</span>column<span class="token operator">&gt;</span>
        <span class="token operator">&lt;</span>el<span class="token operator">-</span>table<span class="token operator">-</span>column prop<span class="token operator">=</span><span class="token string">"bio"</span> label<span class="token operator">=</span><span class="token string">"Acerca de mi"</span> width<span class="token operator">=</span><span class="token string">"400"</span> sortable<span class="token operator">&gt;</span><span class="token operator">&lt;</span><span class="token operator">/</span>el<span class="token operator">-</span>table<span class="token operator">-</span>column<span class="token operator">&gt;</span>
      <span class="token operator">&lt;</span><span class="token operator">/</span>el<span class="token operator">-</span>table<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>div<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span><span class="token operator">/</span>template<span class="token operator">&gt;</span>

<span class="token operator">&lt;</span>script<span class="token operator">&gt;</span> <span class="token keyword">export</span> <span class="token keyword">default</span> <span class="token punctuation">{</span>
  name<span class="token punctuation">:</span> <span class="token string">"StudentIndex"</span><span class="token punctuation">,</span>
  <span class="token function">data</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token punctuation">{</span>
        data<span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">,</span>
        loading<span class="token punctuation">:</span> <span class="token boolean">false</span>
    <span class="token punctuation">}</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>
  <span class="token function">created</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">let</span> self <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">;</span>
      self<span class="token punctuation">.</span><span class="token function">getAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>
  methods<span class="token punctuation">:</span> <span class="token punctuation">{</span>
    <span class="token function">getAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">let</span> self <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">;</span>

      self<span class="token punctuation">.</span>loading <span class="token operator">=</span> <span class="token boolean">true</span><span class="token punctuation">;</span>

      self<span class="token punctuation">.</span>$store<span class="token punctuation">.</span>state<span class="token punctuation">.</span>services<span class="token punctuation">.</span>studentService
        <span class="token punctuation">.</span><span class="token function">getAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
        <span class="token punctuation">.</span><span class="token function">then</span><span class="token punctuation">(</span>r <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
          self<span class="token punctuation">.</span>loading <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
          self<span class="token punctuation">.</span>data <span class="token operator">=</span> r<span class="token punctuation">.</span>data<span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span>r <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
          self<span class="token punctuation">.</span><span class="token function">$message</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
            message<span class="token punctuation">:</span> <span class="token string">"Ocurrió un error inesperado"</span><span class="token punctuation">,</span>
            type<span class="token punctuation">:</span> <span class="token string">"error"</span>
          <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span> 
<span class="token operator">&lt;</span><span class="token operator">/</span>script<span class="token operator">&gt;</span>
</code></pre>
<p>No olvidemos decirle a nuestro VueRouter (src/router.index.js) que X ruta pertenece a X componente.</p>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">import</span> Vue <span class="token keyword">from</span> <span class="token string">'vue'</span>
<span class="token keyword">import</span> Router <span class="token keyword">from</span> <span class="token string">'vue-router'</span>

<span class="token keyword">import</span> Default <span class="token keyword">from</span> <span class="token string">'@/components/Default'</span>
<span class="token keyword">import</span> ExampleIndex <span class="token keyword">from</span> <span class="token string">'@/components/example/Index'</span>
<span class="token keyword">import</span> ExampleView <span class="token keyword">from</span> <span class="token string">'@/components/example/View'</span>
<span class="token keyword">import</span> StudentIndex <span class="token keyword">from</span> <span class="token string">'@/components/students/Index'</span>
<span class="token keyword">import</span> StudentCreateOrUpdate <span class="token keyword">from</span> <span class="token string">'@/components/students/CreateOrUpdate'</span>

Vue<span class="token punctuation">.</span><span class="token function">use</span><span class="token punctuation">(</span>Router<span class="token punctuation">)</span>

<span class="token keyword">const</span> routes <span class="token operator">=</span> <span class="token punctuation">[</span>
  <span class="token punctuation">{</span> path<span class="token punctuation">:</span> <span class="token string">'/'</span><span class="token punctuation">,</span> name<span class="token punctuation">:</span> <span class="token string">'Default'</span><span class="token punctuation">,</span> component<span class="token punctuation">:</span> Default <span class="token punctuation">}</span><span class="token punctuation">,</span>
  <span class="token punctuation">{</span> path<span class="token punctuation">:</span> <span class="token string">'/example'</span><span class="token punctuation">,</span> name<span class="token punctuation">:</span> <span class="token string">'ExampleIndex'</span><span class="token punctuation">,</span> component<span class="token punctuation">:</span> ExampleIndex <span class="token punctuation">}</span><span class="token punctuation">,</span>
  <span class="token punctuation">{</span> path<span class="token punctuation">:</span> <span class="token string">'/example/:id'</span><span class="token punctuation">,</span> name<span class="token punctuation">:</span> <span class="token string">'ExampleView'</span><span class="token punctuation">,</span> component<span class="token punctuation">:</span> ExampleView <span class="token punctuation">}</span><span class="token punctuation">,</span>
  <span class="token punctuation">{</span> path<span class="token punctuation">:</span> <span class="token string">'/students/'</span><span class="token punctuation">,</span> name<span class="token punctuation">:</span> <span class="token string">'StudentIndex'</span><span class="token punctuation">,</span> component<span class="token punctuation">:</span> StudentIndex <span class="token punctuation">}</span><span class="token punctuation">,</span>
  <span class="token punctuation">{</span> path<span class="token punctuation">:</span> <span class="token string">'/students/add'</span><span class="token punctuation">,</span> name<span class="token punctuation">:</span> <span class="token string">'StudentCreate'</span><span class="token punctuation">,</span> component<span class="token punctuation">:</span> StudentCreateOrUpdate <span class="token punctuation">}</span><span class="token punctuation">,</span>
  <span class="token punctuation">{</span> path<span class="token punctuation">:</span> <span class="token string">'/students/:id/edit'</span><span class="token punctuation">,</span> name<span class="token punctuation">:</span> <span class="token string">'StudentEdit'</span><span class="token punctuation">,</span> component<span class="token punctuation">:</span> StudentCreateOrUpdate <span class="token punctuation">}</span><span class="token punctuation">,</span>
<span class="token punctuation">]</span>

<span class="token keyword">export</span> <span class="token keyword">default</span> <span class="token keyword">new</span> <span class="token class-name">Router</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
  routes
<span class="token punctuation">}</span><span class="token punctuation">)</span>
</code></pre>
<h2 id="registrando-un-nuevo-estudiante">Registrando un nuevo estudiante</h2>
<p>En esta lección vamos agregar un nuevo método a nuestro studentService para hacer un llamado a nuestro endpoint y registrar al nuevo estudiante.</p>
<p>StudentsCreateOrUpdate: Si el id está especificado es una actualización en caso contrario se trata de un nuevo registro.</p>
<p>En el fichero <code>studentService.js</code> registramos los endpoint de nuestra api:</p>
<pre class=" language-js"><code class="prism  language-js"><span class="token function">nameMethod</span><span class="token punctuation">(</span>model<span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">let</span> self <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">;</span>
	<span class="token keyword">return</span> self<span class="token punctuation">.</span>axios<span class="token punctuation">.</span><span class="token function">post</span><span class="token punctuation">(</span><span class="token template-string"><span class="token string">`</span><span class="token interpolation"><span class="token interpolation-punctuation punctuation">${</span>self<span class="token punctuation">.</span>baseUrl<span class="token interpolation-punctuation punctuation">}</span></span><span class="token string">`</span></span><span class="token punctuation">,</span> model<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<h3 id="studentservice">StudentService</h3>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">class</span> <span class="token class-name">StudentService</span> <span class="token punctuation">{</span>
    axios
    baseUrl
    <span class="token function">constructor</span><span class="token punctuation">(</span>axios<span class="token punctuation">,</span> apiUrl<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">this</span><span class="token punctuation">.</span>axios <span class="token operator">=</span> axios
        <span class="token keyword">this</span><span class="token punctuation">.</span>baseUrl <span class="token operator">=</span> <span class="token template-string"><span class="token string">`</span><span class="token interpolation"><span class="token interpolation-punctuation punctuation">${</span>apiUrl<span class="token interpolation-punctuation punctuation">}</span></span><span class="token string">student`</span></span>
    <span class="token punctuation">}</span>
    <span class="token function">getAll</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">let</span> self <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> self<span class="token punctuation">.</span>axios<span class="token punctuation">.</span><span class="token keyword">get</span><span class="token punctuation">(</span><span class="token template-string"><span class="token string">`</span><span class="token interpolation"><span class="token interpolation-punctuation punctuation">${</span>self<span class="token punctuation">.</span>baseUrl<span class="token interpolation-punctuation punctuation">}</span></span><span class="token string">`</span></span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token function">add</span><span class="token punctuation">(</span>model<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">let</span> self <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> self<span class="token punctuation">.</span>axios<span class="token punctuation">.</span><span class="token function">post</span><span class="token punctuation">(</span><span class="token template-string"><span class="token string">`</span><span class="token interpolation"><span class="token interpolation-punctuation punctuation">${</span>self<span class="token punctuation">.</span>baseUrl<span class="token interpolation-punctuation punctuation">}</span></span><span class="token string">`</span></span><span class="token punctuation">,</span> model<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h3 id="nuestro-formulario-con-element-ui">Nuestro formulario con Element-UI</h3>
<p>Luego vamos a crear nuestro formulario y le agregamos reglas de validación usando Element-UI.</p>
<pre class=" language-html"><code class="prism  language-html"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>template</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>div</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>h2</span><span class="token punctuation">&gt;</span></span>{{ pageTitle }}<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>h2</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>el-form</span> <span class="token attr-name">v-loading</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>loading<span class="token punctuation">"</span></span> <span class="token attr-name">:model</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>form<span class="token punctuation">"</span></span> <span class="token attr-name">:rules</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>rules<span class="token punctuation">"</span></span> <span class="token attr-name">ref</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>ruleForm<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>el-form-item</span> <span class="token attr-name">label</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Nombre<span class="token punctuation">"</span></span> <span class="token attr-name">prop</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>name<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>el-input</span> <span class="token attr-name">v-model</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>form.name<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>el-input</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>el-form-item</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>el-form-item</span> <span class="token attr-name">label</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Apellido<span class="token punctuation">"</span></span> <span class="token attr-name">prop</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>lastName<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>el-input</span> <span class="token attr-name">v-model</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>form.lastName<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>el-input</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>el-form-item</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>el-form-item</span> <span class="token attr-name">label</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>Acerca de mí<span class="token punctuation">"</span></span> <span class="token attr-name">prop</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>bio<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>el-input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>textarea<span class="token punctuation">"</span></span> <span class="token attr-name">v-model</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>form.bio<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>el-input</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>el-form-item</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>el-form-item</span><span class="token punctuation">&gt;</span></span>
        <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>el-button</span> <span class="token attr-name">@click</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>save<span class="token punctuation">"</span></span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>primary<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>Guardar<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>el-button</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>el-form-item</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>el-form</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>div</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>template</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<p>Y en la parte inferior con respecto al javascript vamos agregar los métodos de guardar, las propiedades de nuestro modelo estudiante y las reglas de validación.</p>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">export</span> <span class="token keyword">default</span> <span class="token punctuation">{</span>
  name<span class="token punctuation">:</span> <span class="token string">"StudentCreateOrUpdate"</span><span class="token punctuation">,</span>
  <span class="token function">data</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> <span class="token punctuation">{</span>
      loading<span class="token punctuation">:</span> <span class="token boolean">false</span><span class="token punctuation">,</span>
      form<span class="token punctuation">:</span> <span class="token punctuation">{</span>
        studentId<span class="token punctuation">:</span> <span class="token number">0</span><span class="token punctuation">,</span>
        name<span class="token punctuation">:</span> <span class="token keyword">null</span><span class="token punctuation">,</span>
        lastName<span class="token punctuation">:</span> <span class="token keyword">null</span><span class="token punctuation">,</span>
        bio<span class="token punctuation">:</span> <span class="token keyword">null</span>
      <span class="token punctuation">}</span><span class="token punctuation">,</span>
      rules<span class="token punctuation">:</span> <span class="token punctuation">{</span>
        name<span class="token punctuation">:</span> <span class="token punctuation">[</span>
          <span class="token punctuation">{</span> required<span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">,</span> message<span class="token punctuation">:</span> <span class="token string">"Debe ingresar un nombre"</span> <span class="token punctuation">}</span><span class="token punctuation">,</span>
          <span class="token punctuation">{</span> min<span class="token punctuation">:</span> <span class="token number">3</span><span class="token punctuation">,</span> message<span class="token punctuation">:</span> <span class="token string">"Debe contener como mínimo 3 caracteres"</span> <span class="token punctuation">}</span>
        <span class="token punctuation">]</span><span class="token punctuation">,</span>
        lastName<span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token punctuation">{</span> required<span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">,</span> message<span class="token punctuation">:</span> <span class="token string">"El apellido es requerido"</span> <span class="token punctuation">}</span><span class="token punctuation">]</span><span class="token punctuation">,</span>
        bio<span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token punctuation">{</span> required<span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">,</span> message<span class="token punctuation">:</span> <span class="token string">"Este campo es requerido"</span> <span class="token punctuation">}</span><span class="token punctuation">]</span>
      <span class="token punctuation">}</span>
    <span class="token punctuation">}</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>
  computed<span class="token punctuation">:</span> <span class="token punctuation">{</span>
    <span class="token function">pageTitle</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>form<span class="token punctuation">.</span>studentId <span class="token operator">===</span> <span class="token number">0</span> <span class="token operator">?</span> <span class="token string">"Nuevo estudiante"</span> <span class="token punctuation">:</span> <span class="token keyword">this</span><span class="token punctuation">.</span>form<span class="token punctuation">.</span>name<span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>
  <span class="token function">created</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
  methods<span class="token punctuation">:</span> <span class="token punctuation">{</span>
    <span class="token function">save</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">let</span> self <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">;</span>
      self<span class="token punctuation">.</span>$refs<span class="token punctuation">[</span><span class="token string">"ruleForm"</span><span class="token punctuation">]</span><span class="token punctuation">.</span><span class="token function">validate</span><span class="token punctuation">(</span>valid <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>valid<span class="token punctuation">)</span> <span class="token punctuation">{</span>
          self<span class="token punctuation">.</span>$store<span class="token punctuation">.</span>state<span class="token punctuation">.</span>services<span class="token punctuation">.</span>studentService
            <span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>self<span class="token punctuation">.</span>form<span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token function">then</span><span class="token punctuation">(</span>r <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
              self<span class="token punctuation">.</span>loading <span class="token operator">=</span> <span class="token boolean">false</span><span class="token punctuation">;</span>
              self<span class="token punctuation">.</span>$router<span class="token punctuation">.</span><span class="token function">push</span><span class="token punctuation">(</span><span class="token string">'/students'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span><span class="token punctuation">)</span>
            <span class="token punctuation">.</span><span class="token keyword">catch</span><span class="token punctuation">(</span>r <span class="token operator">=&gt;</span> <span class="token punctuation">{</span>
              self<span class="token punctuation">.</span><span class="token function">$message</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
                message<span class="token punctuation">:</span> <span class="token string">"Ocurrió un error inesperado"</span><span class="token punctuation">,</span>
                type<span class="token punctuation">:</span> <span class="token string">"error"</span>
              <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
      <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<h2 id="actualizando-y-eliminado-un-estudiante">Actualizando y eliminado un estudiante</h2>

