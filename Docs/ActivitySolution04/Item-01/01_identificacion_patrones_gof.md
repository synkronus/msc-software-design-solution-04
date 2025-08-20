# Actividad 04 - Unidad 4: Patrones de Diseño Gang of Four
## Item 1: Identificación y Nominación de Patrones GoF para Implementación

### Información del Proyecto
- **Proyecto**: PoliMarket - Sistema de Comercio Electrónico Basado en Componentes (CBSE)
- **Unidad**: 4 - Patrones de Diseño de Software
- **Actividad**: Evaluativa Sumativa U4
- **Fecha**: Diciembre 2024
- **Versión**: 1.0

---

## 1. Introducción

El presente documento corresponde al **Item 1** de la Actividad Evaluativa Sumativa de la Unidad 4, que requiere la identificación y nominación de un mínimo de 5 patrones Gang of Four (GoF) para implementar en el sistema PoliMarket desarrollado en la Unidad 2.

### 1.1 Contexto del Sistema

El sistema PoliMarket implementa una arquitectura basada en componentes reutilizables (CBSE) con los siguientes elementos principales:

- **Backend**: API REST desarrollada en .NET 8 con arquitectura de componentes
- **Frontend**: Dos clientes (Angular y React) que consumen diferentes componentes
- **Componentes Funcionales**: 5 requerimientos funcionales (RF1-RF5) implementados como componentes independientes
- **Infraestructura**: Componentes transversales para integración, auditoría y configuración

### 1.2 Objetivos del Análisis

1. Identificar oportunidades de mejora en la arquitectura actual mediante patrones GoF
2. Seleccionar patrones que complementen y fortalezcan la arquitectura CBSE existente
3. Justificar la selección basada en problemas reales identificados en el código
4. Preparar la base para la implementación de patrones en los siguientes items de la actividad

---

## 2. Metodología de Análisis

### 2.1 Criterios de Evaluación

Para la selección de patrones GoF se establecieron los siguientes criterios:

1. **Compatibilidad con CBSE**: El patrón debe complementar la arquitectura basada en componentes
2. **Valor Agregado**: Debe resolver problemas reales identificados en el código actual
3. **Extensibilidad**: Debe facilitar futuras expansiones del sistema
4. **Mantenibilidad**: Debe mejorar la organización y claridad del código
5. **Reutilización**: Debe aumentar la reutilización de componentes

### 2.2 Proceso de Análisis

1. **Revisión de Código**: Análisis exhaustivo de la implementación actual
2. **Identificación de Problemas**: Detección de áreas de mejora en el diseño
3. **Mapeo de Patrones**: Correlación entre problemas identificados y patrones GoF
4. **Evaluación de Impacto**: Análisis del beneficio esperado de cada patrón
5. **Selección Final**: Elección de los patrones más beneficiosos

---

## 3. Análisis del Sistema Actual

### 3.1 Arquitectura Existente

El sistema PoliMarket presenta las siguientes características arquitectónicas relevantes para la aplicación de patrones:

#### 3.1.1 Gestión de Componentes
- **Dependency Injection**: Registro manual de servicios en `Program.cs`
- **Interfaces Bien Definidas**: Contratos claros entre componentes
- **Separación de Responsabilidades**: Cada componente maneja un dominio específico

#### 3.1.2 Creación de Objetos
- **Instanciación Directa**: Creación de productos y entidades sin abstracción
- **Configuración Hardcodeada**: Valores de configuración distribuidos en el código
- **Falta de Flexibilidad**: Dificultad para crear variantes de objetos

#### 3.1.3 Comunicación Entre Componentes
- **Acoplamiento Directo**: Componentes se comunican directamente
- **Eventos Limitados**: Sistema de notificaciones básico sin patrón Observer
- **Sincronización Manual**: Coordinación manual entre operaciones

#### 3.1.4 Algoritmos de Negocio
- **Lógica Hardcodeada**: Cálculos de precios y descuentos fijos en el código
- **Falta de Flexibilidad**: Dificultad para cambiar algoritmos de pricing
- **Extensibilidad Limitada**: Agregar nuevas estrategias requiere modificar código existente

### 3.2 Problemas Identificados

#### 3.2.1 Problema 1: Creación Compleja de Productos
**Ubicación**: `PoliMarket.Components.Products.ProductosComponent`
**Descripción**: La creación de productos se realiza de forma directa sin considerar las diferencias entre categorías de productos.

```csharp
// Código actual - creación directa sin abstracción
if (string.IsNullOrEmpty(producto.Id))
{
    producto.Id = GenerateProductId();
}
producto.FechaCreacion = DateTime.UtcNow;
producto.Estado = true;
```

**Limitaciones**:
- No hay diferenciación en la creación según tipo de producto
- Validaciones específicas por categoría están mezcladas
- Dificultad para agregar nuevos tipos de productos

#### 3.2.2 Problema 2: Comunicación Asíncrona Limitada
**Ubicación**: Sistema de notificaciones entre componentes
**Descripción**: Los componentes no tienen un mecanismo robusto para notificar cambios a otros componentes interesados.

**Limitaciones**:
- Notificaciones manuales entre componentes
- Falta de desacoplamiento en la comunicación
- Dificultad para agregar nuevos observadores

#### 3.2.3 Problema 3: Algoritmos de Cálculo Rígidos
**Ubicación**: `PoliMarket.Components.Sales.VentasComponent`
**Descripción**: Los algoritmos de cálculo de precios, descuentos e impuestos están hardcodeados.

```csharp
// Lógica hardcodeada actual
var subtotal = (detalle.Cantidad * detalle.Precio) - detalle.Descuento;
total += subtotal;
// Apply taxes (example: 19% IVA)
var tax = total * 0.19;
var finalTotal = total + tax;
```

**Limitaciones**:
- Imposibilidad de cambiar algoritmos en tiempo de ejecución
- Dificultad para agregar nuevas estrategias de pricing
- Código duplicado para diferentes tipos de cálculos

#### 3.2.4 Problema 4: Gestión de Configuración Distribuida
**Ubicación**: Múltiples archivos de configuración
**Descripción**: La configuración del sistema está distribuida sin un punto central de acceso.

**Limitaciones**:
- Configuración duplicada en múltiples lugares
- Falta de consistencia en el acceso a configuración
- Dificultad para gestionar configuración global

#### 3.2.5 Problema 5: Operaciones Complejas Sin Encapsulación
**Ubicación**: `PoliMarket.Components.Sales.VentasComponent.ProcessSaleAsync`
**Descripción**: Las operaciones de venta involucran múltiples pasos sin encapsulación adecuada.

**Limitaciones**:
- Operaciones complejas mezcladas en un solo método
- Dificultad para implementar undo/redo
- Falta de logging granular de operaciones

#### 3.2.6 Problema 6: Funcionalidades Transversales Aplicadas Manualmente
**Ubicación**: `PoliMarket.Components.Infrastructure.Services.AuditService`
**Descripción**: Funcionalidades como auditoría y logging se aplican manualmente en cada componente.

**Limitaciones**:
- Código repetitivo para auditoría
- Inconsistencia en la aplicación de funcionalidades transversales
- Dificultad para agregar nuevas funcionalidades de forma transparente

---

## 4. Patrones GoF Seleccionados

### 4.1 Patrón 1: Factory Method (Creational)

#### 4.1.1 Identificación
- **Nombre**: Factory Method
- **Categoría**: Creational Pattern
- **Ubicación de Implementación**: `PoliMarket.Components.Products`

#### 4.1.2 Problema que Resuelve
El patrón Factory Method aborda la **creación compleja y variada de productos según categoría** identificada en el sistema actual.

#### 4.1.3 Justificación de Selección
1. **Problema Actual**: Creación directa de productos sin diferenciación por tipo
2. **Beneficio Esperado**: Flexibilidad para crear diferentes tipos de productos
3. **Extensibilidad**: Facilita agregar nuevas categorías sin modificar código existente
4. **Mantenibilidad**: Separa la lógica de creación del uso de objetos

#### 4.1.4 Aplicación en PoliMarket
- **Contexto**: Creación de productos con validaciones específicas por categoría
- **Variantes**: ProductoElectronico, ProductoRopa, ProductoAlimenticio, etc.
- **Beneficio**: Cada factory maneja las reglas específicas de su categoría

### 4.2 Patrón 2: Observer (Behavioral)

#### 4.2.1 Identificación
- **Nombre**: Observer
- **Categoría**: Behavioral Pattern
- **Ubicación de Implementación**: `PoliMarket.Components.Infrastructure` + Todos los componentes

#### 4.2.2 Problema que Resuelve
El patrón Observer aborda la **comunicación asíncrona limitada entre componentes** para notificaciones del sistema.

#### 4.2.3 Justificación de Selección
1. **Problema Actual**: Comunicación directa y acoplada entre componentes
2. **Beneficio Esperado**: Desacoplamiento en la comunicación de eventos
3. **Extensibilidad**: Facilita agregar nuevos observadores sin modificar publicadores
4. **Reutilización**: Permite reutilizar el sistema de eventos en diferentes contextos

#### 4.2.4 Aplicación en PoliMarket
- **Contexto**: Notificaciones de cambios de stock, ventas procesadas, alertas
- **Observadores**: Componentes de inventario, notificaciones, reportes
- **Beneficio**: Comunicación asíncrona y desacoplada entre componentes

### 4.3 Patrón 3: Strategy (Behavioral)

#### 4.3.1 Identificación
- **Nombre**: Strategy
- **Categoría**: Behavioral Pattern
- **Ubicación de Implementación**: `PoliMarket.Components.Sales`

#### 4.3.2 Problema que Resuelve
El patrón Strategy aborda los **algoritmos de cálculo rígidos** para precios, descuentos e impuestos.

#### 4.3.3 Justificación de Selección
1. **Problema Actual**: Lógica de cálculo hardcodeada en el componente de ventas
2. **Beneficio Esperado**: Intercambiabilidad de algoritmos en tiempo de ejecución
3. **Extensibilidad**: Facilita agregar nuevas estrategias de pricing
4. **Flexibilidad**: Permite diferentes estrategias para diferentes tipos de clientes

#### 4.3.4 Aplicación en PoliMarket
- **Contexto**: Cálculo de precios, aplicación de descuentos, cálculo de impuestos
- **Estrategias**: DescuentoVIP, DescuentoVolumen, DescuentoTemporada, etc.
- **Beneficio**: Flexibilidad total en algoritmos de pricing

### 4.4 Patrón 4: Singleton (Creational)

#### 4.4.1 Identificación
- **Nombre**: Singleton
- **Categoría**: Creational Pattern
- **Ubicación de Implementación**: `PoliMarket.Components.Infrastructure`

#### 4.4.2 Problema que Resuelve
El patrón Singleton aborda la **gestión de configuración distribuida** del sistema.

#### 4.4.3 Justificación de Selección
1. **Problema Actual**: Configuración distribuida sin punto central de acceso
2. **Beneficio Esperado**: Instancia única para gestión global de configuración
3. **Consistencia**: Garantiza acceso uniforme a configuración del sistema
4. **Control**: Gestión centralizada de recursos compartidos

#### 4.4.4 Aplicación en PoliMarket
- **Contexto**: Configuración global del sistema, logging centralizado
- **Instancia Única**: ConfigurationManager, LoggerManager
- **Beneficio**: Acceso consistente y controlado a recursos globales

### 4.5 Patrón 5: Command (Behavioral)

#### 4.5.1 Identificación
- **Nombre**: Command
- **Categoría**: Behavioral Pattern
- **Ubicación de Implementación**: `PoliMarket.Components.Sales` + `PoliMarket.Components.Inventory`

#### 4.5.2 Problema que Resuelve
El patrón Command aborda las **operaciones complejas sin encapsulación** adecuada.

#### 4.5.3 Justificación de Selección
1. **Problema Actual**: Operaciones de venta con múltiples pasos mezclados
2. **Beneficio Esperado**: Encapsulación de operaciones complejas
3. **Funcionalidad**: Soporte para undo/redo de operaciones críticas
4. **Auditoría**: Logging granular y trazabilidad de operaciones

#### 4.5.4 Aplicación en PoliMarket
- **Contexto**: Procesamiento de ventas, actualización de inventario
- **Comandos**: ProcessSaleCommand, UpdateInventoryCommand, ApplyDiscountCommand
- **Beneficio**: Operaciones encapsuladas, reversibles y auditables

### 4.6 Patrón 6: Decorator (Structural)

#### 4.6.1 Identificación
- **Nombre**: Decorator
- **Categoría**: Structural Pattern
- **Ubicación de Implementación**: `PoliMarket.Components.Authorization` + `PoliMarket.Components.Infrastructure`

#### 4.6.2 Problema que Resuelve
El patrón Decorator aborda las **funcionalidades transversales aplicadas manualmente**.

#### 4.6.3 Justificación de Selección
1. **Problema Actual**: Auditoría y logging aplicados manualmente
2. **Beneficio Esperado**: Funcionalidades dinámicas y transparentes
3. **Flexibilidad**: Combinación de funcionalidades según necesidades
4. **Mantenibilidad**: Separación de responsabilidades transversales

#### 4.6.4 Aplicación en PoliMarket
- **Contexto**: Auditoría, logging, validación, autorización
- **Decoradores**: AuditDecorator, LoggingDecorator, ValidationDecorator
- **Beneficio**: Funcionalidades transversales aplicadas transparentemente

---

## 5. Resumen y Conclusiones

### 5.1 Tabla Resumen de Patrones Seleccionados

| # | Patrón | Tipo | Componente Principal | Problema que Resuelve | Beneficio Clave |
|---|--------|------|---------------------|----------------------|-----------------|
| 1 | Factory Method | Creational | Products | Creación compleja de productos | Flexibilidad en creación por categoría |
| 2 | Observer | Behavioral | Infrastructure | Comunicación asíncrona limitada | Desacoplamiento en notificaciones |
| 3 | Strategy | Behavioral | Sales | Algoritmos de cálculo rígidos | Intercambiabilidad de algoritmos |
| 4 | Singleton | Creational | Infrastructure | Configuración distribuida | Gestión centralizada de recursos |
| 5 | Command | Behavioral | Sales/Inventory | Operaciones complejas sin encapsulación | Operaciones reversibles y auditables |
| 6 | Decorator | Structural | Authorization/Infrastructure | Funcionalidades transversales manuales | Funcionalidades dinámicas y transparentes |

### 5.2 Impacto Esperado en la Arquitectura CBSE

#### 5.2.1 Fortalecimiento de Principios CBSE
- **Separación de Responsabilidades**: Los patrones refuerzan la modularidad
- **Reutilización**: Aumenta la reutilización de componentes y funcionalidades
- **Extensibilidad**: Facilita la extensión del sistema sin modificar código existente
- **Mantenibilidad**: Mejora la organización y claridad del código

#### 5.2.2 Mejoras en Interfaces
- **Flexibilidad**: Interfaces más flexibles entre componentes
- **Desacoplamiento**: Reducción del acoplamiento directo
- **Abstracción**: Mayor nivel de abstracción en las interacciones

#### 5.2.3 Beneficios para Testing
- **Aislamiento**: Mejor aislamiento de componentes para testing
- **Mocking**: Facilita la creación de mocks y stubs
- **Cobertura**: Permite mejor cobertura de casos de prueba

### 5.3 Conclusiones

1. **Selección Justificada**: Los 6 patrones seleccionados abordan problemas reales identificados en el código actual del sistema PoliMarket.

2. **Compatibilidad CBSE**: Todos los patrones son compatibles y complementan la arquitectura basada en componentes existente.

3. **Valor Agregado**: Cada patrón aporta valor específico sin comprometer la integridad del diseño actual.

4. **Preparación para Implementación**: La selección proporciona una base sólida para los siguientes items de la actividad.

5. **Escalabilidad**: Los patrones preparan el sistema para futuras expansiones y modificaciones.

### 5.4 Próximos Pasos

1. **Item 2**: Actualización de diagramas UML para mostrar la integración de patrones
2. **Item 3**: Documentación detallada de implementación de cada patrón
3. **Item 4**: Evaluación de madurez del equipo según modelo seleccionado
4. **Item 5**: Estrategias de mejora para alcanzar el siguiente nivel de madurez

---

## Referencias

*Nota: Las referencias completas en formato APA se incluirán en el documento final consolidado de la actividad.*

- Gamma, E., Helm, R., Johnson, R., & Vlissides, J. (1994). *Design Patterns: Elements of Reusable Object-Oriented Software*
- Documentación técnica del sistema PoliMarket CBSE (Unidad 2)
- Transcripciones de sesiones 6 y 7 del curso
- Lectura fundamental Unidad 4: Modelos de Madurez de Ingeniería de Software

---

**Documento generado para**: Actividad Evaluativa Sumativa U4 - Item 1  
**Fecha**: Diciembre 2024  
**Versión**: 1.0  
**Estado**: Completado
