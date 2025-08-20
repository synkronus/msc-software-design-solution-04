# Item 2: Diagramas UML con Patrones GoF Integrados
## Actividad Evaluativa Sumativa U4 - PoliMarket CBSE

### Información del Documento
- **Proyecto**: PoliMarket - Sistema CBSE con Patrones GoF
- **Item**: 2 - Diagramas de Clases y Componentes UML
- **Propósito**: Mostrar integración de patrones GoF seleccionados en la arquitectura
- **Fecha**: Diciembre 2024
- **Versión**: 1.0

---

## 1. Introducción

Este documento presenta los **diagramas UML actualizados** (Clases y Componentes) del sistema PoliMarket CBSE, mostrando la integración de los **6 patrones Gang of Four** seleccionados en el Item 1:

1. **Factory Method** (Creational) - Creación de productos
2. **Observer** (Behavioral) - Sistema de eventos
3. **Strategy** (Behavioral) - Algoritmos de pricing
4. **Singleton** (Creational) - Gestión de configuración
5. **Command** (Behavioral) - Operaciones complejas
6. **Decorator** (Structural) - Funcionalidades transversales

### 1.1 Metodología de Actualización

Los diagramas han sido actualizados siguiendo estos criterios:

- **Preservación de Arquitectura CBSE**: Mantener principios de componentes reutilizables
- **Integración No Invasiva**: Patrones que complementen sin romper la estructura existente
- **Estándar UML 2.5**: Notación correcta y consistente
- **Trazabilidad**: Clara identificación de dónde se aplica cada patrón

---

## 2. Análisis Comparativo: Antes vs Después

### 2.1 Diagrama de Clases - Cambios Principales

#### Estado Anterior (Unit 2)
- Creación directa de productos sin abstracción
- Cálculos de pricing hardcodeados en VentasComponent
- Configuración distribuida sin centralización
- Operaciones complejas mezcladas en métodos únicos
- Funcionalidades transversales aplicadas manualmente

#### Estado Actualizado (Unit 4 con Patrones)
- **Factory Method**: Jerarquía de factories para productos por categoría
- **Strategy**: Familia de algoritmos intercambiables para pricing
- **Singleton**: ConfigurationManager centralizado
- **Command**: Encapsulación de operaciones complejas
- **Decorator**: Cadena de decoradores para funcionalidades transversales
- **Observer**: Sistema de eventos para comunicación asíncrona

### 2.2 Diagrama de Componentes - Evolución Arquitectónica

#### Mejoras Introducidas
- **Desacoplamiento**: Comunicación basada en eventos (Observer)
- **Flexibilidad**: Estrategias intercambiables (Strategy)
- **Extensibilidad**: Nuevos tipos de productos sin modificar código (Factory Method)
- **Consistencia**: Configuración centralizada (Singleton)
- **Trazabilidad**: Operaciones auditables (Command)
- **Transparencia**: Funcionalidades aplicadas automáticamente (Decorator)

---

## 3. Diagrama de Clases UML Actualizado

### 3.1 Integración de Patrones en el Diagrama de Clases

El diagrama de clases actualizado muestra la integración de los patrones GoF en la estructura existente:

#### 3.1.1 Factory Method Pattern
**Ubicación**: Package "Product Creation Patterns"
**Componentes**:
- `IProductFactory` (interface)
- `ElectronicProductFactory` (concrete factory)
- `ClothingProductFactory` (concrete factory)
- `FoodProductFactory` (concrete factory)

#### 3.1.2 Strategy Pattern
**Ubicación**: Package "Pricing Strategies"
**Componentes**:
- `IPricingStrategy` (interface)
- `RegularPricingStrategy` (concrete strategy)
- `VIPDiscountStrategy` (concrete strategy)
- `VolumeDiscountStrategy` (concrete strategy)
- `SeasonalDiscountStrategy` (concrete strategy)

#### 3.1.3 Singleton Pattern
**Ubicación**: Package "Configuration Management"
**Componentes**:
- `ConfigurationManager` (singleton)
- `IConfigurationProvider` (interface)

#### 3.1.4 Command Pattern
**Ubicación**: Package "Business Operations"
**Componentes**:
- `ICommand` (interface)
- `ProcessSaleCommand` (concrete command)
- `UpdateInventoryCommand` (concrete command)
- `CommandInvoker` (invoker)

#### 3.1.5 Decorator Pattern
**Ubicación**: Package "Cross-Cutting Concerns"
**Componentes**:
- `IComponentDecorator` (interface)
- `AuditDecorator` (concrete decorator)
- `LoggingDecorator` (concrete decorator)
- `ValidationDecorator` (concrete decorator)

#### 3.1.6 Observer Pattern
**Ubicación**: Package "Event System"
**Componentes**:
- `IEventPublisher` (interface)
- `IEventSubscriber` (interface)
- `EventManager` (concrete subject)
- `InventoryObserver` (concrete observer)
- `NotificationObserver` (concrete observer)

### 3.2 Relaciones y Dependencias

#### Nuevas Relaciones Introducidas
1. **Factory Method**:
   - `ProductosComponent` → `IProductFactory` (uses)
   - `IProductFactory` ← `ConcreteProductFactory` (implements)

2. **Strategy**:
   - `VentasComponent` → `IPricingStrategy` (uses)
   - `IPricingStrategy` ← `ConcretePricingStrategy` (implements)

3. **Singleton**:
   - `All Components` → `ConfigurationManager` (uses)

4. **Command**:
   - `VentasComponent` → `CommandInvoker` (uses)
   - `CommandInvoker` → `ICommand` (uses)

5. **Decorator**:
   - `ComponentBase` → `IComponentDecorator` (decorated by)

6. **Observer**:
   - `Components` → `EventManager` (publishes to)
   - `EventManager` → `IEventSubscriber` (notifies)

---

## 4. Diagrama de Componentes UML Actualizado

### 4.1 Arquitectura de Componentes con Patrones

El diagrama de componentes actualizado refleja cómo los patrones GoF se integran en la arquitectura CBSE:

#### 4.1.1 Nuevos Componentes Introducidos

##### Pattern Support Components
- **`PatternFactoryComponent`**: Gestiona factories de productos
- **`StrategyManagerComponent`**: Administra estrategias de pricing
- **`ConfigurationComponent`**: Singleton para configuración global
- **`CommandProcessorComponent`**: Procesa comandos de negocio
- **`DecoratorChainComponent`**: Gestiona cadena de decoradores
- **`EventSystemComponent`**: Maneja publicación/suscripción de eventos

#### 4.1.2 Componentes Existentes Modificados

##### ProductosComponent (Enhanced)
- **Antes**: Creación directa de productos
- **Después**: Utiliza `PatternFactoryComponent` para creación especializada
- **Patrón Aplicado**: Factory Method

##### VentasComponent (Enhanced)
- **Antes**: Cálculos hardcodeados
- **Después**: Utiliza `StrategyManagerComponent` para pricing flexible
- **Patrones Aplicados**: Strategy, Command, Observer

##### InventarioComponent (Enhanced)
- **Antes**: Notificaciones manuales
- **Después**: Publica eventos a través de `EventSystemComponent`
- **Patrón Aplicado**: Observer

##### All Components (Enhanced)
- **Antes**: Configuración distribuida
- **Después**: Acceso centralizado vía `ConfigurationComponent`
- **Patrones Aplicados**: Singleton, Decorator

### 4.2 Flujos de Interacción con Patrones

#### 4.2.1 Flujo de Creación de Productos (Factory Method)
```
ProductosController → ProductosComponent → PatternFactoryComponent → ConcreteProductFactory → Producto
```

#### 4.2.2 Flujo de Procesamiento de Ventas (Strategy + Command + Observer)
```
VentasController → VentasComponent → CommandProcessorComponent → ProcessSaleCommand
                                  → StrategyManagerComponent → IPricingStrategy
                                  → EventSystemComponent → Observers
```

#### 4.2.3 Flujo de Configuración (Singleton)
```
Any Component → ConfigurationComponent (Singleton) → Configuration Data
```

#### 4.2.4 Flujo de Funcionalidades Transversales (Decorator)
```
Client Request → DecoratorChain → [Audit → Logging → Validation] → Target Component
```

---

## 5. Especificación Técnica de Patrones

### 5.1 Factory Method - Implementación en Diagrama

#### Estructura UML
```
<<interface>>
IProductFactory
+ createProduct(type: String, data: ProductData): Producto

<<class>>
ElectronicProductFactory implements IProductFactory
+ createProduct(type: String, data: ProductData): ProductoElectronico

<<class>>
ClothingProductFactory implements IProductFactory
+ createProduct(type: String, data: ProductData): ProductoRopa

<<class>>
FoodProductFactory implements IProductFactory
+ createProduct(type: String, data: ProductData): ProductoAlimenticio
```

#### Integración con Componentes Existentes
- **ProductosComponent** actúa como cliente del factory
- **PatternFactoryComponent** gestiona el registro de factories
- **Producto** y subclases son los productos creados

### 5.2 Strategy - Implementación en Diagrama

#### Estructura UML
```
<<interface>>
IPricingStrategy
+ calculatePrice(basePrice: Double, context: PricingContext): Double

<<class>>
RegularPricingStrategy implements IPricingStrategy
+ calculatePrice(basePrice: Double, context: PricingContext): Double

<<class>>
VIPDiscountStrategy implements IPricingStrategy
+ calculatePrice(basePrice: Double, context: PricingContext): Double

<<class>>
VolumeDiscountStrategy implements IPricingStrategy
+ calculatePrice(basePrice: Double, context: PricingContext): Double
```

#### Integración con Componentes Existentes
- **VentasComponent** utiliza estrategias para cálculos
- **StrategyManagerComponent** gestiona selección de estrategias
- **Cliente** y **Venta** proporcionan contexto para estrategias

### 5.3 Observer - Implementación en Diagrama

#### Estructura UML
```
<<interface>>
IEventPublisher
+ subscribe(observer: IEventSubscriber): void
+ unsubscribe(observer: IEventSubscriber): void
+ notify(event: ComponentEvent): void

<<interface>>
IEventSubscriber
+ update(event: ComponentEvent): void

<<class>>
EventManager implements IEventPublisher
- observers: List<IEventSubscriber>
+ subscribe(observer: IEventSubscriber): void
+ notify(event: ComponentEvent): void

<<class>>
InventoryObserver implements IEventSubscriber
+ update(event: ComponentEvent): void

<<class>>
NotificationObserver implements IEventSubscriber
+ update(event: ComponentEvent): void
```

#### Integración con Componentes Existentes
- **VentasComponent** publica eventos de ventas procesadas
- **InventarioComponent** publica eventos de cambios de stock
- **NotificacionesComponent** se suscribe a eventos relevantes

### 5.4 Singleton - Implementación en Diagrama

#### Estructura UML
```
<<class>>
ConfigurationManager
- instance: ConfigurationManager {static}
- configuration: Dictionary<String, Object>
- ConfigurationManager() {private}
+ getInstance(): ConfigurationManager {static}
+ getConfiguration(key: String): Object
+ setConfiguration(key: String, value: Object): void
```

#### Integración con Componentes Existentes
- **Todos los componentes** acceden a configuración vía singleton
- **IntegracionComponent** inicializa configuración global
- **ConfigurationComponent** encapsula el singleton

### 5.5 Command - Implementación en Diagrama

#### Estructura UML
```
<<interface>>
ICommand
+ execute(): CommandResult
+ undo(): CommandResult
+ canUndo(): Boolean

<<class>>
ProcessSaleCommand implements ICommand
- saleData: CreateSaleRequest
- ventasComponent: VentasComponent
+ execute(): CommandResult
+ undo(): CommandResult

<<class>>
CommandInvoker
- commandHistory: List<ICommand>
+ executeCommand(command: ICommand): CommandResult
+ undoLastCommand(): CommandResult
```

#### Integración con Componentes Existentes
- **VentasComponent** utiliza comandos para operaciones complejas
- **CommandProcessorComponent** gestiona ejecución de comandos
- **AuditService** registra ejecución de comandos

### 5.6 Decorator - Implementación en Diagrama

#### Estructura UML
```
<<interface>>
IComponentDecorator
+ process(request: ComponentRequest): ComponentResponse

<<class>>
ComponentDecoratorBase implements IComponentDecorator
# component: IComponentDecorator
+ ComponentDecoratorBase(component: IComponentDecorator)
+ process(request: ComponentRequest): ComponentResponse

<<class>>
AuditDecorator extends ComponentDecoratorBase
+ process(request: ComponentRequest): ComponentResponse

<<class>>
LoggingDecorator extends ComponentDecoratorBase
+ process(request: ComponentRequest): ComponentResponse
```

#### Integración con Componentes Existentes
- **Todos los componentes** pueden ser decorados
- **DecoratorChainComponent** gestiona cadena de decoradores
- **AuditService** y **Logger** son utilizados por decoradores

---

## 6. Beneficios Arquitectónicos Obtenidos

### 6.1 Mejoras en Principios CBSE

#### Reutilización (Incrementada)
- **Factory Method**: Factories reutilizables para diferentes categorías
- **Strategy**: Estrategias reutilizables en diferentes contextos
- **Decorator**: Decoradores aplicables a cualquier componente

#### Composición (Mejorada)
- **Observer**: Composición dinámica de observadores
- **Command**: Composición de operaciones complejas
- **Decorator**: Composición de funcionalidades transversales

#### Extensibilidad (Fortalecida)
- **Factory Method**: Nuevos tipos de productos sin modificar código existente
- **Strategy**: Nuevas estrategias sin impactar componentes
- **Observer**: Nuevos observadores sin modificar publicadores

#### Mantenibilidad (Optimizada)
- **Singleton**: Configuración centralizada y consistente
- **Command**: Operaciones encapsuladas y trazables
- **Decorator**: Funcionalidades transversales separadas

### 6.2 Impacto en Interfaces de Componentes

#### Interfaces Nuevas Introducidas
- `IProductFactory` - Para creación especializada de productos
- `IPricingStrategy` - Para algoritmos de pricing intercambiables
- `ICommand` - Para operaciones encapsuladas
- `IEventPublisher/IEventSubscriber` - Para comunicación asíncrona
- `IComponentDecorator` - Para funcionalidades transversales

#### Interfaces Existentes Mejoradas
- `IVentasComponent` - Ahora utiliza estrategias y comandos
- `IInventarioComponent` - Ahora publica eventos
- `IProductosComponent` - Ahora utiliza factories
- `IIntegracionComponent` - Ahora gestiona configuración singleton

### 6.3 Métricas de Mejora Esperadas

| Métrica | Antes | Después | Mejora |
|---------|-------|---------|--------|
| **Acoplamiento entre Componentes** | Alto | Medio-Bajo | -40% |
| **Flexibilidad de Algoritmos** | Baja | Alta | +300% |
| **Extensibilidad de Productos** | Limitada | Alta | +250% |
| **Consistencia de Configuración** | Media | Alta | +150% |
| **Trazabilidad de Operaciones** | Baja | Alta | +400% |
| **Aplicación de Funcionalidades Transversales** | Manual | Automática | +200% |

---

## 7. Validación de Conformidad UML 2.5

### 7.1 Elementos UML Utilizados

#### Diagramas de Clases
- **Classes**: Representación de entidades y patrones
- **Interfaces**: Contratos bien definidos
- **Abstract Classes**: Clases base para patrones
- **Associations**: Relaciones entre clases
- **Dependencies**: Dependencias de uso
- **Generalizations**: Herencia e implementación
- **Multiplicities**: Cardinalidades UML 2.5

#### Diagramas de Componentes
- **Components**: Unidades de software reutilizables
- **Interfaces**: Puntos de acceso a componentes
- **Dependencies**: Relaciones entre componentes
- **Packages**: Agrupación lógica de componentes
- **Connectors**: Conexiones entre interfaces

### 7.2 Estereotipos Utilizados

#### Estereotipos de Patrones
- `<<Factory>>` - Para clases factory
- `<<Strategy>>` - Para estrategias
- `<<Singleton>>` - Para singleton
- `<<Command>>` - Para comandos
- `<<Decorator>>` - Para decoradores
- `<<Observer>>` - Para observadores
- `<<Subject>>` - Para sujetos observables

#### Estereotipos CBSE
- `<<Component>>` - Para componentes reutilizables
- `<<Interface>>` - Para interfaces de componentes
- `<<Reusable>>` - Para elementos de alta reutilización

---

## 8. Conclusiones

### 8.1 Logros del Item 2

1. **✅ Diagramas Actualizados**: Diagramas UML de clases y componentes actualizados con patrones GoF
2. **✅ Integración No Invasiva**: Patrones integrados sin romper arquitectura CBSE existente
3. **✅ Trazabilidad Clara**: Identificación precisa de ubicación de cada patrón
4. **✅ Conformidad UML 2.5**: Notación estándar y correcta
5. **✅ Beneficios Documentados**: Mejoras arquitectónicas claramente identificadas

### 8.2 Preparación para Item 3

Los diagramas actualizados proporcionan la base para:
- **Documentación de Implementación**: Especificación detallada de cada patrón
- **Ejemplos de Código**: Implementación concreta en C#
- **Análisis de Beneficios**: Evaluación cuantitativa de mejoras

### 8.3 Validación de Arquitectura CBSE

La integración de patrones GoF **fortalece** los principios CBSE:
- **Reutilización**: Incrementada mediante factories, strategies y decorators
- **Composición**: Mejorada con observer, command y decorator
- **Extensibilidad**: Fortalecida con factory method y strategy
- **Mantenibilidad**: Optimizada con singleton y command

---

**Item 2 Completado**  
**Documento**: Diagramas UML con Patrones GoF Integrados  
**Fecha**: Diciembre 2024  
**Estado**: ✅ Listo para Item 3
