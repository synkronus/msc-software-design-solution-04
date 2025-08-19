# PoliMarket - Sistema de Gestión Empresarial

## Actividad Evaluativa Sumativa U2 - Reutilización de Software

### Descripción del Proyecto

Este proyecto implementa un sistema de gestión empresarial para PoliMarket utilizando principios de reutilización de software y desarrollo basado en componentes (CBSE). El sistema incluye:

- **Backend**: API REST en .NET Core
- **Cliente 1**: Aplicación web Angular
- **Cliente 2**: Aplicación web React
- **Diagramas UML**: Clases y Componentes en formato BMP

### Estructura de la Documentación (Orden de Lectura)

#### Documentos Principales (Numerados para Orden Secuencial)

- **`01_componentes_polimarket.md`** - Análisis detallado de componentes reutilizables
- **`02_RF4_Estrategia_Reutilizacion.md`** - Estrategia de reutilización de software
- **`03_RF5_Plan_Implementacion_Integracion.md`** - Plan de implementación e integración
- **`04_especificaciones_interfaces.md`** - Especificaciones detalladas de interfaces
- **`05_matriz_dependencias_componentes.md`** - Matriz de dependencias entre componentes
- **`06_protocolos_interaccion_componentes.md`** - Protocolos de comunicación
- **`07_Conclusiones_Academicas.md`** - Conclusiones académicas en formato APA
- **`08_resumen_mejoras_aplicadas.md`** - Resumen de mejoras y correcciones aplicadas
- **`09_README_Entrega_Completa.md`** - Guía completa de entrega

#### Diagramas UML (Formato BMP - UML 2.5) - ACTUALIZADOS

- **`BPMs/PoliMarket_Class_Diagram_CBSE.bmp`** - Diagrama de clases UML 2.5 ✅
- **`BPMs/PoliMarket_Components_Diagram_CBSE.bmp`** - Diagrama de componentes UML 2.5 ✅
- **`BPMs/PoliMarket_Deployment_Architecture.bmp`** - Arquitectura de despliegue ✅

### Arquitectura del Sistema

#### Backend (.NET Core)

- **RF1**: Autorización de vendedores por RRHH
- **RF2**: Procesamiento de ventas
- **RF3**: Consulta de disponibilidad de productos
- **RF4**: Gestión de entregas
- **RF5**: Gestión de proveedores

#### Clientes

- **Angular**: Consume RF1 (Autorización) y RF3 (Inventario)
- **React**: Consume RF4 (Entregas) y RF5 (Proveedores)

### Estructura del Proyecto

```
solution-01/
├── Backend/
│   └── PoliMarket.API/
│       ├── Controllers/
│       ├── Models/
│       ├── Services/
│       ├── Program.cs
│       └── PoliMarket.API.csproj
├── Client1-Angular/
│   ├── src/
│   │   └── app/
│   │       └── app.component.ts
│   └── package.json
├── Client2-React/
│   ├── src/
│   │   ├── App.tsx
│   │   └── App.css
│   └── package.json
├── PoliMarket_Class_Diagram_CBSE.bmp
├── PoliMarket_Components_Diagram_CBSE.bmp
├── componentes_polimarket.md
└── Conclusiones_Academicas.md
```

### Requisitos Previos

- .NET 8.0 SDK
- Node.js 18+
- Angular CLI 17+
- Visual Studio Code o Visual Studio 2022

### Instalación y Ejecución

#### 1. Backend (.NET API)

```bash
cd Backend/PoliMarket.API
dotnet restore
dotnet run
```

La API estará disponible en: `https://localhost:7000`

#### 2. Cliente Angular

```bash
cd Client1-Angular
npm install
ng serve
```

La aplicación estará disponible en: `http://localhost:4200`

#### 3. Cliente React

```bash
cd Client2-React
npm install
npm start
```

La aplicación estará disponible en: `http://localhost:3001`

### Funcionalidades Implementadas

#### RF1: Autorización de Vendedores (Angular)

- Formulario para autorizar nuevos vendedores
- Validación de empleados de RRHH
- Lista de vendedores autorizados

#### RF2: Procesamiento de Ventas (Backend)

- API endpoints para crear y consultar ventas
- Cálculo automático de totales
- Gestión de detalles de venta

#### RF3: Consulta de Inventario (Angular)

- Consulta de productos disponibles
- Verificación de stock individual
- Alertas de stock bajo

#### RF4: Gestión de Entregas (React)

- Programación de entregas
- Confirmación de entregas
- Seguimiento de estados

#### RF5: Gestión de Proveedores (React)

- Lista de proveedores activos
- Creación de órdenes de compra
- Gestión de detalles de órdenes

### Diagramas UML

#### Diagrama de Clases (BMP)

- Modela las entidades del dominio
- Relaciones con multiplicidad
- Sintaxis UML 2.5

#### Diagrama de Componentes (BMP)

- Arquitectura por capas
- Interfaces entre componentes
- Separación de responsabilidades

### Componentes Identificados

| Área de Negocio | Componente            | Funcionalidades                          |
| ---------------- | --------------------- | ---------------------------------------- |
| RRHH             | AutorizacionComponent | Autorizar vendedores, gestionar permisos |
| Ventas           | VentasComponent       | Procesar ventas, calcular totales        |
| Bodega           | InventarioComponent   | Consultar disponibilidad, alertas        |
| Proveedores      | ProveedoresComponent  | Gestionar proveedores, órdenes          |
| Entregas         | EntregasComponent     | Programar entregas, confirmar            |

### Principios de Reutilización Aplicados

1. **Reutilización de Código**: Componentes .NET reutilizables
2. **Reutilización de Diseño**: Patrones arquitectónicos consistentes
3. **Reutilización de Análisis**: Modelos de dominio abstraídos

### Tecnologías Utilizadas

- **Backend**: .NET 8.0, ASP.NET Core Web API
- **Cliente 1**: Angular 17, TypeScript
- **Cliente 2**: React 18, TypeScript
- **Diagramas**: PlantUML convertido a BMP
- **Comunicación**: HTTP REST API

### Pruebas

#### Probar la API

1. Ejecutar el backend
2. Navegar a `https://localhost:7000/swagger`
3. Probar los endpoints disponibles

#### Probar Clientes

1. Ejecutar backend y cliente
2. Verificar funcionalidades específicas de cada cliente
3. Confirmar comunicación con la API

### Conclusiones Académicas

Las conclusiones detalladas del proyecto se encuentran en `Conclusiones_Academicas.md`, donde se analiza:

- Aplicación de principios teóricos
- Beneficios observados
- Desafíos enfrentados
- Contribuciones al conocimiento
- Recomendaciones para futuros proyectos

### Entregables Completados

✅ **Diagrama de Clases UML 2.5** (BMP)
✅ **Tabla de Componentes** (Markdown)
✅ **Diagrama de Componentes UML** (BMP)
✅ **Sistema Backend** (.NET - 5 RF implementados)
✅ **Cliente Angular** (RF1 + RF3)
✅ **Cliente React** (RF4 + RF5)
✅ **Conclusiones Académicas** (APA)

### Autor

Estudiante del Politécnico Grancolombiano
Módulo: Temas Avanzados en Diseño de Software
Unidad 2: Reutilización de Software

### Fecha de Entrega

Julio 2025
