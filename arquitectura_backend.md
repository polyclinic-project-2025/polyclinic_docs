# Arquitectura del Proyecto

## Estructura de Carpetas

**Solution/**
- **[Domain/](#domain-layer)**
  - **[Entities/](#entities)**
    - `Entity.cs`
  - **[Repositories/](repositories)**
    - `IEntityRepository.cs`
- **[Application/](#application-layer)**
  - **[DTOs/](#dtos)**
    - **[Request/](#request)**
      - `CreateEntityDto.cs`
    - **[Response/](#response)**
      - `EntityDto.cs`
  - **[Common/](#common)**
    - `Result.cs`
  - **[Services/](#services)**
    - **[Interfaces/](#interfaces)**
      - `IEntityService.cs`
    - **[Implementations/](#implementations)**
      - `EntityService.cs`
- **[Infrastructure/](#infrastructure-layer)**
  - **[Persistence/](#persistence)**
    - `AppDbContext.cs`
  - **[Repositories/](#repositories)**
    - `EntityRepository.cs`
- **[Presentation/](#presentation-layer)**
  - **[Controllers/](#controllers)**
    - `EntityController.cs`
  - `Program.cs`
  - `appsettings.json`

---

## Domain Layer

**Responsabilidad:** Contiene la lógica de negocio pura y las entidades del dominio.

**Características:**
- No depende de ninguna otra capa
- Define interfaces (contratos) que otras capas implementarán

**Contenido:**
## Entities: 

Implementa los objetos del dominio, es decir las entidades del negocio concebidas puras. La entidad se traduce como una clase donde sus atributos son propiedades y la lógica de negocio pura se puede verificar mediante métodos de clase.

```
    public class Entity
    {
        public type_1 Attribute_1 {get; private set;}

        public Entity(type_1 attribute_1, type_1 attribute_n) 
        {
            //initializations;
        }
    }
```

## Repositories: 
Interfaces para acceso a datos. Cada interfaz de repositorio define el contrato de las operaciones CRUD que se pueden ejecutar sobre la entidad. Hace uso de `Entities` dado que las operaciones se realizan sobre un objeto de dominio.

```
    using Domain.Entities

    public interface IEntityRepository
    {
        Task Add(Entity ent);
        Task Remove(Entity ent);
        Task<Enumerable<Entity>> GetByAttribute_1Contains(type_1 attribute_1);
    }
```

[⬆️ Volver a la estructura](#estructura-de-carpetas)

---

## Application Layer

**Responsabilidad:** Orquesta la lógica de aplicación y casos de uso.

**Características:**
- Depende solo de Domain
- Contiene la lógica de aplicación
- Define DTOs y servicios

**Contenido:**
## DTOs: 
Objetos diseñados exclusivamente para la transferencia de datos entre diferentes capas de la aplicacion. Dada una entidad compleja se crean los dto como solución a posibles problemas: 
- la sobrecarga de datos: cuando se requiere informacion d la entidad se recibe TODO incluso lo que no se necesita.
- seguridad: si se almacenan datos sensibles a diferentes publicos estos serían expuestos.
- acoplamiento: si se realizan cambios en la entidad se rompe la API
- permite diferentes validaciones

### **Características:**

**Solo propiedades** (sin métodos de lógica)  
**Planos y simples** (sin relaciones complejas)  
**Inmutables preferiblemente** (records en C#)  
**Específicos por caso de uso**  

## **Mapeo: Entidad ↔ DTO**

Flujo típico:
1. Cliente envía CreateEntityDto (dto request)
2. Servicio lo convierte a objeto Entity
3. Se guarda en base de datos
4. Se convierte a EntityDto (dto response)
5. Se devuelve al cliente

### Request:
DTOs de entrada: son los objetos que se reciben para la creacion, actualizacion, registro y demás operaciones sobre la entidad en cuestión.

### Response:
DTOs de salida: son los objetos que se retornan tras una peticion de visualizaciones específicas, obtención de datos no sensibles y demás operaciones que requieran la salida de datos. 

```
    public record CreateEntityDto(type_1 attribute_1, type_2 attribute_2);
    public record EntityDto(type_2 attribute_2);
```


## Common: 
Utilidades compartidas. 
Implementa patrones como el Result Pattern que encapsula el resultado de una operación, incluyendo tanto el éxito como el fracaso, en un objeto único. Especialmente útil para eliminar el uso de excepciones.

```
    public class Result<T>
    {
        public bool IsSuccess { get; }
        public string Message { get; }
        public T? Value { get; }

        private Result(bool isSuccess, string message, T? value)
        {
            IsSuccess = isSuccess;
            Message = message;
            Value = value;
        }

        public static Result<T> Success(T value) => new(true, string.Empty, value);
        public static Result<T> Failure(string message) => new(false, message, default);
    }
```


## Services: 
Lógica de casos de uso. Define las interfaz/contrato con los casos de uso de un objeto conceptual del negocio e implementa dicha interfaz incluyendo lógica de negocio y restricciones, haciendo uso de los dto para recibir y retornar datos y del contrato de repositorio.

## Interfaces:

```
    using Application.DTOs;
    using Application.Common;
    
    public interface IEntityService
    {
        Task<Result<EntityDto>> Add(CreateEntityDto dto);
        Task<Result<Enumerable<Entity>>> GetByAttribute_1Contanis(type_1 attribute_1);
    }
```

## Implementations:

```
    using Application.DTOs;
    using Application.Common;
    using Domain.Entities;
    using Domain.Repositories;

    public class EntityService: IEntityService
    {
        private readonly IEntityRepository _repository;

        public EntityService(IEntityRepository repository)
        {
            _repository = repository;
        }

        public async Task<Result<EntityDto>> Add(CreateEntityDto dto)
        {
            if (string.IsNullOrWhiteSpace(dto.Attribute_2))
            return Result<EntityDto>.Failure("No puede ser vacío");
        }

        public async Task<Result<Enumerable<Entity>>> GetByAttribute_1Contains(type_1 attribute_1)
        {
            var entities = await _repository.GetByAttribute_1Contains(attribute_1);
            var entitiesDto = entities.Select(e => new EntityDto(e.Attribute_2));
            return Result<IEnumerable<EntityDto>>.Success(entitiesDto);
        }
    }
```

[⬆️ Volver a la estructura](#estructura-de-carpetas)

---

## Infrastructure Layer

**Responsabilidad:** Implementaciones técnicas y acceso a recursos externos.

**Características:**
- Implementa las interfaces definidas en Domain
- Acceso a base de datos, APIs externas, etc.
- Detalles técnicos de implementación

**Contenido:**
## Persistence: 
Configuración de Entity Framework. En este módulo se define el DbContext, en como un puente o traductor entre la aplicación y la base de datos. Permite interactuar con la base de datos usando objetos. DbContext representa una sesión con la base de datos donde definimos la estructura y aplicamos los requisitos necesarios para la creación de una tabla.

**Nota:** ModelBuilder es quien se encarga de configurar el modelo conceptual de la base de datos. Herramienta principal para definir como los objetos(entidades) se mapean a tablas y relaciones en la base de datos.

```
    using Domain.Entities

    public class AppDbContext: DbContext
    {
        public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)

        public DbSet<Entity_1> TableName_1 {get; set;}
        public DbSet<Entity_2> TableName_2 {get; set;}
        
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            //Settings 

            //Setting Entity_1
            modelBuilder.Entity<Entity_1>( entity => 
                entity.HasKey(e => e.Attribute_1);

                entity.Property(e => e.Attribute_2)
                    .IsRequired()
                    .HasMaxLength(len)
            )

            //Setting Entity2
        }
    }
```

## Repositories: 
Implementación de repositorios de la capa dominio. Si persistencia nos permitía definir/configurar los modelos conceptuales, la implementacion de un repositorio nos permite mapear peticiones/operaciones CRUD entrantes a operaciones reales en la base de datos y salvar estos cambios. 

```
    using Domain.Entities
    using Domain.Repositories
    using Infrastructure.Persistence

    public class EntityRepository: IEntityRepository
    {
        private readonly AppDbContext _context;

        public EntityRepository(AppDbContext context)
        {
            _context = context;
        }

        public async Task Add(Entity ent)
        {
            await _context.TableName.Add(ent);
            await _context.SaveChangesAsync();
        }

        public async Task<IEnumerable<Entity>> GetByAttribute_1Contains(type_1 attribute_1)
        {
            return await _context.TableName
                .Where(x => x.Attribute_1.Contains(attribute_1))
                .ToListAsync();
        }
    }

```

[⬆️ Volver a la estructura](#estructura-de-carpetas)

---

## Presentation Layer

**Responsabilidad:** Punto de entrada de la aplicación (API).

**Características:**
- Controladores REST
- Configuración de la aplicación
- Inyección de dependencias

**Contenido:**
## Controllers: 
Endpoints de la API, que son los puntos finales de coomunicación en una api donde los clientes pueden enviar solicitudes para realizar operaciones específicas. Un endpoint sería básicamente una url única que corresponde a una operación específica en la api. Cada servicio tiene un controlador que traduce peticiones a casos de uso.

**Componentes:**
- Verbo HTTP (`HTTP Method`): GET(obtener recursos), POST(crear recursos), PUT(actualizar recursos completamente), PATCH(actualizar recursos parcialmente), DELETE(eliminar recursos)
- Ruta(`Route`): `[HttpGet("entities/{attribute_1}/attibute_2")]` Ruta: GET /api/entities/attribute_1-value/attribute_2
- Parámettros: `FromQuery`, `FromBody`
- Respuestas(`Responses`): `BadRequest`, `NotFound`, `CreatedAtAction`

```
    using Application.Services;
using Application.DTOs;

[ApiController]
[Route("api/[controller]")]
public class PelisController : ControllerBase
{
    private readonly IEntityService _entityService;

    public EntityController(IEntityService entityService)
    {
        _entityService = entityService;
    }

    [HttpPost]
    public async Task<IActionResult> Add([FromBody] CreateEntityDto dto)
    {
        var _result = await _entityService.Add(dto);
        if(result.IsSuccess)
        {
            if (result.IsSuccess)
            return CreatedAtAction(
                nameof(GetById), 
                new { id = result.Value!.Id }, 
                new { Value = result.Value }); //construye la url para acceder al recurso y retorna además el recurso.
        }
        return BadRequest(new { Message = result.Message })
    }

}
```


- `Program.cs`: Configuración y startup

Agrega al contenedor de dependencias de .Net las dependencias necesarias para inyección de dependencias clásica a través de los constructores. De esta forma gestiona la creación y ciclo de vida de las dependencias de la aplicación.

**Métodos de Registro:**
- `Transient`: ciclo de vida: se crea una nueva instancia cada vez que se solicita Uso: servicios sin estado
- `Scoped`: ciclo de vida: una instancia por request Uso: servicios que necesitan mantener estado durante una petición http
- `Singleton`: ciclo de vida: una unica instancia durante toda la aplicación Uso: servicios costosos de crear o que mantienen estado global

Estos métodos proveen de la implementación especificada al módulo dependiente del contrato de dicha implementación.

```
    var builder = WebApplication.CreateBuilder(args);

    // Infrastructure - Database
    builder.Services.AddDbContext<AppDbContext>(options =>
        options.UseNpgsql(
            builder.Configuration.GetConnectionString("DefaultConnection"),
            b => b.MigrationsAssembly("Presentation")));

    // Infrastructure - Repositories
    builder.Services.AddScoped<IEntityRepository, EntityRepository>();

    // Application - Services
    builder.Services.AddScoped<IEntityService, EntityService>();

    // Presentation - Controllers
    builder.Services.AddControllers();
```

- `appsettings.json`: Configuración

[⬆️ Volver a la estructura](#estructura-de-carpetas)