# Item 3: Documentación de Funcionalidades con Patrones GoF

## Información del Documento
- **Propósito**: Documentar funcionalidades donde aplican los patrones GoF seleccionados
- **Metodología**: Análisis problema-solución-implementación por patrón
- **Lenguaje de implementación**: C# (.NET 8.0)
- **Fecha**: Diciembre 2024

---

## Tabla de Análisis de Patrones GoF

| Patrón | Problema Identificado | Por qué el Patrón Ayuda | Cómo se Implementa |
|--------|----------------------|-------------------------|-------------------|
| **Factory Method** | Creación rígida de productos con validaciones específicas por categoría | Permite crear productos especializados sin modificar código cliente, extensible para nuevas categorías | Interface IProductFactory con factories concretas por categoría |
| **Strategy** | Algoritmos de pricing fijos, difíciles de cambiar y probar | Intercambia algoritmos de pricing en tiempo de ejecución, facilita A/B testing | Interface IPricingStrategy con estrategias concretas |
| **Observer** | Comunicación acoplada entre componentes para eventos | Desacopla publishers de subscribers, permite múltiples observadores | EventManager con IEventPublisher/IEventSubscriber |
| **Singleton** | Configuración duplicada y inconsistente entre componentes | Garantiza una sola instancia de configuración global | ConfigurationManager con instancia única thread-safe |
| **Command** | Operaciones complejas sin capacidad de deshacer | Encapsula operaciones como objetos, permite undo/redo y logging | ICommand con CommandInvoker y comandos concretos |
| **Decorator** | Funcionalidades transversales mezcladas con lógica de negocio | Añade responsabilidades dinámicamente sin modificar clases base | ServiceDecorator con decoradores específicos |

---

## 1. Factory Method Pattern

### 1.1 Problema Identificado
**¿Cuál es el problema?**
En el sistema PoliMarket actual, la creación de productos se realiza directamente en `ProductosComponent.CreateProductAsync()` sin considerar las diferencias entre categorías de productos (Electrónicos, Ropa, Alimentos). Cada categoría requiere validaciones específicas y propiedades particulares, pero el código actual trata todos los productos de manera genérica.

### 1.2 Por qué el Patrón Ayuda
**¿Por qué el patrón puede ayudar a solucionar el problema?**
El Factory Method permite crear objetos especializados sin que el código cliente conozca las clases concretas. Esto proporciona:
- **Extensibilidad**: Nuevas categorías sin modificar código existente
- **Validaciones específicas**: Cada factory maneja sus propias reglas de negocio
- **Polimorfismo**: Interface común para todas las factories
- **Mantenibilidad**: Lógica de creación centralizada por categoría

### 1.3 Implementación del Patrón
**¿Cómo se puede implementar el patrón?**

#### 1.3.1 Interface Factory
```csharp
namespace PoliMarket.Patterns.Factory
{
    public interface IProductFactory
    {
        Producto CreateProduct(CreateProductRequest request);
        bool ValidateProductData(CreateProductRequest request);
        List<string> GetSupportedCategories();
    }
}
```

#### 1.3.2 Factory Manager
```csharp
namespace PoliMarket.Patterns.Factory
{
    public class ProductFactoryManager
    {
        private readonly Dictionary<string, IProductFactory> _factories;
        private readonly ILogger<ProductFactoryManager> _logger;

        public ProductFactoryManager(ILogger<ProductFactoryManager> logger)
        {
            _logger = logger;
            _factories = new Dictionary<string, IProductFactory>();
            RegisterDefaultFactories();
        }

        public void RegisterFactory(string categoria, IProductFactory factory)
        {
            _factories[categoria.ToLower()] = factory;
            _logger.LogInformation("Factory registered for category: {Categoria}", categoria);
        }

        public IProductFactory GetFactory(string categoria)
        {
            if (_factories.TryGetValue(categoria.ToLower(), out var factory))
            {
                return factory;
            }
            throw new ArgumentException($"No factory found for category: {categoria}");
        }

        public Producto CreateProduct(CreateProductRequest request)
        {
            var factory = GetFactory(request.Categoria);
            
            if (!factory.ValidateProductData(request))
            {
                throw new ValidationException($"Invalid product data for category: {request.Categoria}");
            }

            var product = factory.CreateProduct(request);
            _logger.LogInformation("Product created: {ProductId} in category: {Categoria}", 
                product.Id, request.Categoria);
            
            return product;
        }

        private void RegisterDefaultFactories()
        {
            RegisterFactory("Electronics", new ElectronicProductFactory());
            RegisterFactory("Clothing", new ClothingProductFactory());
            RegisterFactory("Food", new FoodProductFactory());
        }
    }
}
```

#### 1.3.3 Concrete Factory - Electronics
```csharp
namespace PoliMarket.Patterns.Factory
{
    public class ElectronicProductFactory : IProductFactory
    {
        public Producto CreateProduct(CreateProductRequest request)
        {
            var product = new ProductoElectronico
            {
                Id = GenerateProductId("ELEC"),
                Nombre = request.Nombre,
                Descripcion = request.Descripcion,
                Precio = request.Precio,
                Categoria = "Electronics",
                Stock = request.Stock,
                StockMinimo = request.StockMinimo,
                UnidadMedida = request.UnidadMedida,
                FechaCreacion = DateTime.UtcNow,
                Estado = true,
                // Electronic-specific properties
                Voltaje = request.Specifications.GetValueOrDefault("voltaje", "110V").ToString(),
                GarantiaMeses = Convert.ToInt32(request.Specifications.GetValueOrDefault("garantiaMeses", 12)),
                Marca = request.Specifications.GetValueOrDefault("marca", "").ToString(),
                Modelo = request.Specifications.GetValueOrDefault("modelo", "").ToString(),
                Potencia = Convert.ToDouble(request.Specifications.GetValueOrDefault("potencia", 0))
            };

            return product;
        }

        public bool ValidateProductData(CreateProductRequest request)
        {
            // Electronic-specific validations
            if (!request.Specifications.ContainsKey("voltaje"))
                return false;
            
            if (!request.Specifications.ContainsKey("garantiaMeses"))
                return false;

            var garantia = Convert.ToInt32(request.Specifications["garantiaMeses"]);
            if (garantia < 6 || garantia > 60)
                return false;

            return true;
        }

        public List<string> GetSupportedCategories()
        {
            return new List<string> { "Electronics" };
        }

        private string GenerateProductId(string prefix)
        {
            return $"{prefix}{DateTime.UtcNow:yyyyMMddHHmmss}{Random.Shared.Next(100, 999)}";
        }
    }
}
```

#### 1.3.4 Integration with ProductosComponent
```csharp
namespace PoliMarket.Components.Products
{
    public class ProductosComponent : IProductosComponent
    {
        private readonly ProductFactoryManager _factoryManager;
        private readonly ILogger<ProductosComponent> _logger;
        private readonly PoliMarketDbContext _context;

        public ProductosComponent(
            ProductFactoryManager factoryManager,
            ILogger<ProductosComponent> logger,
            PoliMarketDbContext context)
        {
            _factoryManager = factoryManager;
            _logger = logger;
            _context = context;
        }

        public async Task<ApiResponse<Producto>> CreateProductAsync(CreateProductRequest request)
        {
            try
            {
                _logger.LogInformation("Creating product using Factory Method: {ProductName}", request.Nombre);

                // Use Factory Method pattern
                var producto = _factoryManager.CreateProduct(request);

                // Save to database
                _context.Productos.Add(producto);
                await _context.SaveChangesAsync();

                return ApiResponse<Producto>.SuccessResult(producto, "Producto creado exitosamente");
            }
            catch (ValidationException ex)
            {
                _logger.LogWarning("Product validation failed: {Message}", ex.Message);
                return ApiResponse<Producto>.ErrorResult("Datos de producto inválidos", ex.Message);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error creating product");
                return ApiResponse<Producto>.ErrorResult("Error interno del servidor", ex.Message);
            }
        }
    }
}
```

---

## 2. Strategy Pattern

### 2.1 Problema Identificado
**¿Cuál es el problema?**
En `VentasComponent.CalculateTotalAsync()`, el cálculo de precios está hardcodeado sin considerar diferentes estrategias de pricing como descuentos VIP, descuentos por volumen, precios estacionales, o métodos de pago. Esto hace el sistema inflexible para cambios de negocio y dificulta el testing de diferentes estrategias de pricing.

### 2.2 Por qué el Patrón Ayuda
**¿Por qué el patrón puede ayudar a solucionar el problema?**
El Strategy Pattern permite:
- **Flexibilidad en runtime**: Cambiar algoritmos de pricing dinámicamente
- **Extensibilidad**: Agregar nuevas estrategias sin modificar código existente
- **Testing**: Probar diferentes estrategias de manera aislada
- **Configurabilidad**: Estrategias basadas en reglas de negocio configurables

### 2.3 Implementación del Patrón
**¿Cómo se puede implementar el patrón?**

#### 2.3.1 Strategy Interface
```csharp
namespace PoliMarket.Patterns.Strategy
{
    public interface IPricingStrategy
    {
        double CalculatePrice(double basePrice, PricingContext context);
        string GetStrategyName();
        bool IsApplicable(PricingContext context);
        double GetDiscountPercentage(PricingContext context);
    }

    public class PricingContext
    {
        public Cliente Cliente { get; set; }
        public Producto Producto { get; set; }
        public int Cantidad { get; set; }
        public DateTime FechaVenta { get; set; }
        public string MetodoPago { get; set; }
        public List<Venta> HistorialCompras { get; set; } = new();
        
        public bool IsVIPClient() => Cliente.TipoCliente == "VIP";
        public double GetTotalPurchaseHistory() => HistorialCompras.Sum(v => v.Total);
    }
}
```
