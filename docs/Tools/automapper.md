¿Qué es AutoMapper?
Según la documentación, AutoMapper es Mapeador de Objeto a Objeto, es decir recibo una instancia de un objeto y genera otra instancia de otro objeto con diferente estructura. Interesante no? pues resulta que esta herramienta gratuita, disponible en codeplex - http://automapper.codeplex.com/ – es una librería que nos puede ahorra muchísimo código cuando estamos desarrollando aplicaciones .NET.

Este producto tiene muchas características que se pueden aplicar en el día a día, ya que la conversión de objetos es algo que hacemos casi de forma “instintiva” en nuestro esfuerzo por desarrollar software. Por ejemplo, supongamos que tenemos un servicio que retorna un tipo específico que existe en nuestro dominio de negocio y que sin embargo contiene propiedades que no queremos exponer a los que estén interesados en consumir este servicio. Esta entidad es un empleado que nuestra empresa da en outsourcing a otras compañías. Como se puede suponer, la empresa no tiene interés de exponerle al comprador del servicio cuánto gana ni el nivel que tiene catalogado dentro de la organización. Para lograr esto con automapper, iniciamos por definir las clases que vamos a utilizar. La primera Empleado que es la clases interna, y la segunda EmpleadoOferta que es la clase que le vamos a presentar al cliente.

public class Empleado
{
    public int Id { get; set; }
    public string Nombre { get; set; }
    public string Especialidad { get; set; }
    public double SalarioXHora { get; set; }
    public string Nivel { get; set; }
    public double Experiencia { get; set; }

}



public class EmpleadoOferta
{
    public int Id { get; set; }
    public string Nombre { get; set; }
    public string AnnosExperiencia { get; set; }
    public string Especialidad { get; set; }
}



Seguidamente en el método Main de una aplicación de consola ejemplo, procedemos a crear instancias de la clase empleado y las auto inicializamos



static void Main(string[] args)
        {
            Empleado empleado1 = new Empleado
                                       {
                                           Id = 1,
                                           Nombre = "Gabriel Fernandez",
                                           Especialidad = "ASP.NET",
                                           Experiencia = 4,
                                           Nivel = "Sennior 1",
                                           SalarioXHora = 14
                                       };
            Empleado empleado2 = new Empleado
                                     {
                                         Id = 2,
                                         Nombre = "Gabriela Montés",
                                         Experiencia = 2,
                                         Especialidad = "Silverlight",
                                         Nivel = "Junior 3",
                                         SalarioXHora = 9
                                     };


Ahora si, llegó el momento de utilizar automapper, en este caso vamos a configurar por código la transformación deseada.



//Se configura el mapeo
            Mapper.CreateMap<Empleado, EmpleadoOferta>()
                .ForMember(dest => dest.Nombre, fte => fte.MapFrom(src => src.Nombre))
                .ForMember(dest => dest.Id, fte => fte.MapFrom(src => src.Id))
                .ForMember(dest => dest.AnnosExperiencia, fte => fte.MapFrom(src => src.Experiencia))
                .ForMember(dest => dest.Especialidad, fte => fte.MapFrom(src => src.Especialidad));


Como podemos ver en el código anterior, para configurar el mapeo se utilizan expresiones lambda. En primera instancia creamos un mapa indicándole cuál es la clase fuente y cual es la clase destino



Mapper.CreateMap<Empleado, EmpleadoOferta>()



Inmediatamente después procedemos a crear el mapeo que vamos a necesitar en nuestro código; este mapeo se hace expresando a través de expresiones lambda el campo que corresponde a la clase destino, desde la clase fuente – en nuestro caso fte:



.ForMember(dest => dest.Nombre, fte => fte.MapFrom(src => src.Nombre))



Ahora creamos un método que mapea e imprime el valor de cada una de las clases, la fuente y la transformada



private static void ProcesarEmpleado(Empleado empleado1)
{
    Console.WriteLine("--------------Iniciando Mapeo------------------");

    Console.WriteLine("-----Empleado para uso Interno-----");
    Console.WriteLine("Id Empleado {0}", empleado1.Id);
    Console.WriteLine("Nombre {0}", empleado1.Nombre);
    Console.WriteLine("Experiencia del Empleado {0}", empleado1.Experiencia);
    Console.WriteLine("Especialidad del Empleado {0}", empleado1.Especialidad);
    Console.WriteLine("Nivel del Empleado {0}", empleado1.Nivel);
    Console.WriteLine("Salario por hora ${0}", empleado1.SalarioXHora);

    EmpleadoOferta empleadoOferta1 = Mapper.Map<Empleado, EmpleadoOferta>(empleado1);

    Console.WriteLine("----- Empleado para la Oferta -----");
    Console.WriteLine("Id Empleado {0}", empleadoOferta1.Id);
    Console.WriteLine("Nombre {0}", empleadoOferta1.Nombre);
    Console.WriteLine("Experiencia del Empleado {0}", empleadoOferta1.AnnosExperiencia);
    Console.WriteLine("Especialidad del Empleado {0}", empleadoOferta1.Especialidad);

    Console.WriteLine("--------------Mapeo Finalizado------------------");
}


Quizá la parte más importante de este método es la invocación Mapper.Map, la cual como podemos ver en el código resaltado recibe vía tipos parametrizados el tipo de las clases que se desea transformar – así también por reflección puede determinar cual mapeo usar. Seguidamente procedemos a enviar por parámetro la instancia del tipo de la clase origen, en nuestro caso empleado1 que es una instancia de Empleado. Como podemos ver en el código, el método estático Map, retorna una instancia del tipo destino con la transformación aplicada, por esta razón, creamos una variable de tipo EmpleadoOferta y le asignamos el resultado del mapeo. Desde el método Main invocamos al método ProcesarEmpleado con las instancias de Empleado que creamos anteriormente.



ProcesarEmpleado(empleado1);
ProcesarEmpleado(empleado2);



El resultado de la ejecución del codigo anterior es el siguiente:



image



Ahora supongamos que queremos hacer una transformación un poquito más compleja, por ejemplo un cálculo en medio de la transformación. Supongamos que ahora ahora deseamos exponer en la nueva clase la tarifa por hora del empleado consultado. Para este caso modificamos la clase EmpleadoOferta para que tenga una propiedad más para que nos sirva para este propósito, esta propiedad es CostoAlCliente.



public class EmpleadoOferta
{
    public int Id { get; set; }
    public string Nombre { get; set; }
    public string AnnosExperiencia { get; set; }
    public string Especialidad { get; set; }
    public double CostoAlCliente { get; set; }
}



En nuestro caso, vamos a agregarle un 40% de ganancia por cada hora de trabajo del recurso que el cliente desea tener, para lo cual vamos a modificar el mapeo para que la clase mapeada tenga este valor calculado. El código modificado es el siguiente:



//Se configura el mapeo
            Mapper.CreateMap<Empleado, EmpleadoOferta>()
                .ForMember(dest => dest.Nombre, fte => fte.MapFrom(src => src.Nombre))
                .ForMember(dest => dest.Id, fte => fte.MapFrom(src => src.Id))
                .ForMember(dest => dest.AnnosExperiencia, fte => fte.MapFrom(src => src.Experiencia))
                .ForMember(dest => dest.Especialidad, fte => fte.MapFrom(src => src.Especialidad))
                .ForMember(dest => dest.CostoAlCliente, fte => fte.MapFrom(src => (src.SalarioXHora * 0.40) + src.SalarioXHora));



Como podemos ver en el código anterior, se agrego una nueva propiedad al mapeo, pero en este caso, la expresión lambda se modificó para que haga el cálculo del CostoAlCliente de este empleado. Para entender más las expresiones lambda pueden leer este post que escribí tiempo atrás. Por último vamos a modificar el método para imprimir las entidades antes y después del mapeo.



EmpleadoOferta empleadoOferta1 = Mapper.Map<Empleado, EmpleadoOferta>(empleado1);

Console.WriteLine("----- Empleado para la Oferta -----");
Console.WriteLine("Id Empleado {0}", empleadoOferta1.Id);
Console.WriteLine("Nombre {0}", empleadoOferta1.Nombre);
Console.WriteLine("Experiencia del Empleado {0}", empleadoOferta1.AnnosExperiencia);
Console.WriteLine("Especialidad del Empleado {0}", empleadoOferta1.Especialidad);
Console.WriteLine("Costo al cliente {0}", empleadoOferta1.CostoAlCliente);



Al ejecutar el código modificado el resultado obtenido es el siguiente:



image



Como podemos ver en la figura anterior, se puede ver el cáculo hecho en el mapeo al calcular a partir del salario por hora un sobre precio del 40%.

