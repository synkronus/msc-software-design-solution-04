# Especificaciones Detalladas de Interfaces - PoliMarket CBSE

## 1. Interface IAutorizacion

### Descripción
Interface principal para servicios de autenticación y autorización en el sistema PoliMarket.

### Definición OpenAPI/Swagger

```yaml
openapi: 3.0.3
info:
  title: IAutorizacion Interface
  version: 1.0.0
  description: Interface para servicios de autorización y autenticación

paths:
  /autorizacion/autenticar:
    post:
      summary: Autentica un usuario en el sistema
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                email:
                  type: string
                  format: email
                  example: "vendedor@polimarket.com"
                password:
                  type: string
                  format: password
                  minLength: 8
                  example: "SecurePass123"
              required:
                - email
                - password
      responses:
        '200':
          description: Autenticación exitosa
          content:
            application/json:
              schema:
                type: object
                properties:
                  token:
                    type: string
                    example: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
                  usuario:
                    $ref: '#/components/schemas/Usuario'
                  expiracion:
                    type: string
                    format: date-time
        '401':
          description: Credenciales inválidas
        '429':
          description: Demasiados intentos de autenticación

  /autorizacion/validar-permiso:
    post:
      summary: Valida si un usuario tiene un permiso específico
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                usuarioId:
                  type: string
                  example: "usr_123456"
                permiso:
                  type: string
                  example: "ventas.crear"
                recurso:
                  type: string
                  example: "producto_001"
              required:
                - usuarioId
                - permiso
      responses:
        '200':
          description: Validación completada
          content:
            application/json:
              schema:
                type: object
                properties:
                  autorizado:
                    type: boolean
                  razon:
                    type: string
                    example: "Usuario tiene rol de vendedor autorizado"

components:
  schemas:
    Usuario:
      type: object
      properties:
        id:
          type: string
        nombre:
          type: string
        email:
          type: string
        roles:
          type: array
          items:
            type: string
```

### Métodos Principales

#### 1. autenticar(credenciales: CredencialesDTO): AuthResultDTO
- **Propósito**: Autentica un usuario en el sistema
- **Parámetros**:
  - `credenciales`: Objeto con email y password
- **Retorna**: Token JWT y datos del usuario
- **Excepciones**:
  - `AuthenticationException`: Credenciales inválidas
  - `AccountLockedException`: Cuenta bloqueada
  - `RateLimitException`: Demasiados intentos

#### 2. validarPermiso(usuarioId: string, permiso: string, recurso?: string): boolean
- **Propósito**: Verifica si un usuario tiene un permiso específico
- **Parámetros**:
  - `usuarioId`: ID único del usuario
  - `permiso`: Nombre del permiso a validar
  - `recurso`: Recurso específico (opcional)
- **Retorna**: true si está autorizado, false en caso contrario
- **Excepciones**:
  - `UserNotFoundException`: Usuario no encontrado
  - `InvalidPermissionException`: Permiso inválido

#### 3. obtenerRoles(usuarioId: string): List<RolDTO>
- **Propósito**: Obtiene los roles asignados a un usuario
- **Parámetros**:
  - `usuarioId`: ID único del usuario
- **Retorna**: Lista de roles del usuario
- **Excepciones**:
  - `UserNotFoundException`: Usuario no encontrado

### Ejemplo de Uso

```csharp
// Implementación en C# (.NET)
public class AutorizacionService : IAutorizacion
{
    public async Task<AuthResultDTO> AutenticarAsync(CredencialesDTO credenciales)
    {
        try
        {
            var usuario = await _userRepository.GetByEmailAsync(credenciales.Email);
            if (usuario == null || !_passwordService.Verify(credenciales.Password, usuario.PasswordHash))
            {
                throw new AuthenticationException("Credenciales inválidas");
            }

            var token = _jwtService.GenerateToken(usuario);
            return new AuthResultDTO
            {
                Token = token,
                Usuario = _mapper.Map<UsuarioDTO>(usuario),
                Expiracion = DateTime.UtcNow.AddHours(8)
            };
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error en autenticación para {Email}", credenciales.Email);
            throw;
        }
    }

    public async Task<bool> ValidarPermisoAsync(string usuarioId, string permiso, string recurso = null)
    {
        var usuario = await _userRepository.GetByIdAsync(usuarioId);
        if (usuario == null) return false;

        return await _permissionService.HasPermissionAsync(usuario, permiso, recurso);
    }
}
```

## 2. Interface IVentas

### Descripción
Interface para gestión de procesos de venta y transacciones comerciales.

### Métodos Principales

#### 1. procesarVenta(ventaDTO: VentaDTO): VentaResultDTO
- **Propósito**: Procesa una nueva venta en el sistema
- **Parámetros**:
  - `ventaDTO`: Datos completos de la venta
- **Retorna**: Resultado del procesamiento con ID de venta
- **Excepciones**:
  - `InsufficientStockException`: Stock insuficiente
  - `InvalidCustomerException`: Cliente inválido
  - `PaymentProcessingException`: Error en procesamiento de pago

#### 2. calcularTotal(detalles: List<DetalleVentaDTO>): TotalVentaDTO
- **Propósito**: Calcula el total de una venta incluyendo impuestos y descuentos
- **Parámetros**:
  - `detalles`: Lista de productos y cantidades
- **Retorna**: Desglose completo del total
- **Excepciones**:
  - `InvalidProductException`: Producto no válido
  - `PricingException`: Error en cálculo de precios

#### 3. aplicarDescuento(ventaId: string, descuentoDTO: DescuentoDTO): VentaDTO
- **Propósito**: Aplica un descuento a una venta existente
- **Parámetros**:
  - `ventaId`: ID de la venta
  - `descuentoDTO`: Datos del descuento a aplicar
- **Retorna**: Venta actualizada con descuento
- **Excepciones**:
  - `SaleNotFoundException`: Venta no encontrada
  - `InvalidDiscountException`: Descuento no válido

### Ejemplo de Implementación

```csharp
public class VentasService : IVentas
{
    public async Task<VentaResultDTO> ProcesarVentaAsync(VentaDTO ventaDTO)
    {
        using var transaction = await _dbContext.Database.BeginTransactionAsync();
        try
        {
            // Validar stock
            await _inventarioService.ValidarDisponibilidadAsync(ventaDTO.Detalles);
            
            // Crear venta
            var venta = _mapper.Map<Venta>(ventaDTO);
            venta.Total = await CalcularTotalAsync(ventaDTO.Detalles);
            
            await _ventaRepository.AddAsync(venta);
            
            // Actualizar inventario
            await _inventarioService.ActualizarStockAsync(ventaDTO.Detalles);
            
            // Generar entrega
            await _entregasService.ProgramarEntregaAsync(venta.Id);
            
            await transaction.CommitAsync();
            
            return new VentaResultDTO
            {
                VentaId = venta.Id,
                Estado = "Procesada",
                Total = venta.Total
            };
        }
        catch (Exception)
        {
            await transaction.RollbackAsync();
            throw;
        }
    }
}
```

## 3. Interface IInventario

### Descripción
Interface para gestión de inventario y control de stock.

### Métodos Principales

#### 1. consultarDisponibilidad(productoId: string): DisponibilidadDTO
- **Propósito**: Consulta la disponibilidad actual de un producto
- **Parámetros**:
  - `productoId`: ID único del producto
- **Retorna**: Información de disponibilidad y stock
- **Excepciones**:
  - `ProductNotFoundException`: Producto no encontrado

#### 2. actualizarStock(movimientos: List<MovimientoStockDTO>): ResultadoActualizacionDTO
- **Propósito**: Actualiza el stock de múltiples productos
- **Parámetros**:
  - `movimientos`: Lista de movimientos de stock
- **Retorna**: Resultado de la actualización
- **Excepciones**:
  - `InsufficientStockException`: Stock insuficiente
  - `InvalidMovementException`: Movimiento inválido

#### 3. generarAlerta(criterios: CriteriosAlertaDTO): List<AlertaStockDTO>
- **Propósito**: Genera alertas basadas en criterios de stock
- **Parámetros**:
  - `criterios`: Criterios para generar alertas
- **Retorna**: Lista de alertas generadas
- **Excepciones**:
  - `InvalidCriteriaException`: Criterios inválidos

## 4. Patrones de Manejo de Errores

### Jerarquía de Excepciones

```csharp
public abstract class PoliMarketException : Exception
{
    public string ErrorCode { get; }
    public DateTime Timestamp { get; }
    
    protected PoliMarketException(string errorCode, string message) : base(message)
    {
        ErrorCode = errorCode;
        Timestamp = DateTime.UtcNow;
    }
}

public class BusinessException : PoliMarketException
{
    public BusinessException(string errorCode, string message) : base(errorCode, message) { }
}

public class TechnicalException : PoliMarketException
{
    public TechnicalException(string errorCode, string message) : base(errorCode, message) { }
}
```

### Códigos de Error Estándar

| Código | Descripción | Categoría |
|--------|-------------|-----------|
| AUTH_001 | Credenciales inválidas | Autenticación |
| AUTH_002 | Token expirado | Autenticación |
| AUTH_003 | Permisos insuficientes | Autorización |
| SALES_001 | Stock insuficiente | Ventas |
| SALES_002 | Cliente inválido | Ventas |
| INV_001 | Producto no encontrado | Inventario |
| INV_002 | Movimiento inválido | Inventario |

## 5. Versionado de Interfaces

### Estrategia de Versionado

- **Semantic Versioning**: MAJOR.MINOR.PATCH
- **Backward Compatibility**: Mantenida en versiones MINOR
- **Breaking Changes**: Solo en versiones MAJOR
- **Deprecation Policy**: 6 meses de aviso previo

### Ejemplo de Evolución

```csharp
// Versión 1.0.0
public interface IVentas_V1
{
    Task<VentaResultDTO> ProcesarVentaAsync(VentaDTO venta);
}

// Versión 1.1.0 - Backward compatible
public interface IVentas_V1_1 : IVentas_V1
{
    Task<VentaResultDTO> ProcesarVentaConDescuentoAsync(VentaDTO venta, DescuentoDTO descuento);
}

// Versión 2.0.0 - Breaking changes
public interface IVentas_V2
{
    Task<VentaResultDTO> ProcesarVentaAsync(VentaRequestDTO request); // Parámetro cambiado
}
```

Esta especificación proporciona una base sólida para la implementación de interfaces siguiendo estándares industriales y mejores prácticas de CBSE.
