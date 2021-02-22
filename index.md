## ¿Cómo implementar un ABM con Blazor usando Entity Framework Core? Demostración detallada

Tomando como base el ejemplo de [Mukesh Murugan](https://codewithmukesh.com/blog/blazor-crud-with-entity-framework-core/) vamos a describir detalladamente la creacion de un abm con blazor y Entity Framework Core

La creación de una aplicación ABM es como el Hola mundo para desarrolladores intermedios. Ayuda a comprender las operaciones más comunes de cualquier stack de tecnologias en particular. En este tutorial, creamos una aplicación Blazor ABM del lado del cliente que usa Entity Framework Core como su capa de acceso a datos.

Blazor es la novedad del mundo .NET . Es muy importante para un desarrollador estar al tanto de lo que esta increíble tecnología contiene en sí misma. ¿Qué mejor forma de comprender sus habilidades que hacerlo aprendiendo a implementar funciones de ABM utilizando Blazor y Entity Framework Core?

## Qué estaremos construyendo
Pensé que sería bueno si les mostrara lo que vamos a construir antes de saltar al tutorial para que tengan una buena idea del alcance de este tutorial y lo cubriremos. Tendremos una implementación simple de Blazor ABM en la entidad Programador usando Blazor WebAssembly y Entity Framework Core. También intentaré demostrar ciertas buenas prácticas al desarrollar aplicaciones ABM de Blazor.

## Creando el proyecto Blazor ABM
Comencemos con la aplicación Blazor WebAssembly ABM creando una nueva aplicación Blazor. Siga las capturas de pantalla a continuación. 

Puedes ver que Visual Studio genera automáticamente 3 proyectos diferentes, es decir, Cliente, Servidor y Compartido (shared).
1. Cliente: donde reside la aplicación Blazor junto con los componentes de Razor.
2. Servidor: se usa principalmente como un contenedor que tiene características ASP.NET Core (lo usamos aquí para EF Core, controladores Api [Api controllers] y DB).
3. Compartido: como sugiere el nombre, todos los modelos de entidad se definirán aquí.

**Básicamente, el servidor tendrá los puntos de entrada de la API (End Points) a los que puede acceder el proyecto cliente. Tenga en cuenta que esta es una aplicación única y se ejecuta en el mismo puerto. Por lo tanto, no surge la necesidad de acceso a CORS. Todos estos proyectos se ejecutarán directamente en su navegador y no necesitan un servidor dedicado.**

Para comenzar con nuestra aplicación Blazor ABM, agreguemos un modelo de Programador a nuestro proyecto compartido en la carpeta Modelos (crea la carpeta). 
```
   public class Programador
    {
        public int Id { get; set; }
        public string Nombre { get; set; }
        public string Apellido { get; set; }
        public string Email { get; set; }
        public decimal Experiencia { get; set; }
    }
```
## Entity Framework Core
Ahora, vaya al proyecto del servidor e instale los siguientes paquetes necesarios para habilitar EF Core.
```
Install-Package Microsoft.EntityFrameworkCore
Install-Package Microsoft.EntityFrameworkCore.Design
Install-Package Microsoft.EntityFrameworkCore.Tools
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```
Vaya a appsettings.json en el Proyecto de servidor y agregue la Cadena de conexión.
```
"ConnectionStrings": {
    "DefaultConnection": "<Connection String Here>"
  },
  
```
##Agregar contexto de aplicación
Necesitaremos un contexto de base de datos para trabajar con los datos. Cree una nueva clase en el proyecto de servidor en Data / AplicacionDBContext.cs
```
 public class AplicacionDbContext:DbContext
    {
        public AplicacionDbContext(DbContextOptions<AplicacionDbContext> options) : base(options)
        {
        }
        public DbSet<Programador> Programadores { get; set; }
```
Agregaremos DbSet de Programadores a nuestro contexto. Usando esta clase de contexto, podremos realizar operaciones en nuestra base de datos que generaremos mas adelante.

#Configurando
Necesitaremos agregar EF Core y definir su cadena de conexión. Navegue hasta Startup.cs que se encuentra en el proyecto del servidor y agregue la siguiente línea al método ConfigureServices.
```
services.AddDbContext<AplicacionDbContext>(op =>
                op.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
```
## Generación / migración de la base de datos
Ahora que nuestro Entity Framework Core está configurado y listo en la aplicación ASP.NET Core, hagamos las migraciones y actualicemos nuestra base de datos. Para esto, abra la Consola del Administrador de paquetes y escriba lo siguiente.
```
add-migration Initial
update-database

```
### Importante: asegúrese de haber elegido el proyecto del servidor como predeterminado.

## Controller de Programadores.
Ahora que nuestra base de datos y EF Core están configurados, crearemos un controlador API que entregará los datos a nuestro cliente Blazor. Cree un controlador API vacío, Controllers/ProgramadorController.cs

```
[Route("api/[controller]")]
    [ApiController]
    public class ProgramadorController : ControllerBase
    {
        private readonly AplicacionDbContext _context;

        public ProgramadorController(AplicacionDbContext context)
        {
            _context = context;
        }
    }
        
```

Aquí hemos inyectado una nueva instancia de ApplicationDBContent al constructor del controlador. Continuemos agregando cada uno de los end points o metodos para nuestras operaciones de ABM.

GET
Un método para obtener todos los programadores de la instancia de contexto.
```
 [HttpGet]
        public async Task<IActionResult> Get()
        {
            var programadores = await _context.Programadores.ToListAsync();
            return Ok(programadores);
        }

  [HttpGet("{id}")]
        public async Task<IActionResult> Get(int id)
        {
            var programador = await _context.Programadores.FirstOrDefaultAsync(a => a.Id == id);
            return Ok(programador);
        }
```


```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/fajouri/fajouri.blog.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
