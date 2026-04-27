# QuickBite - Microservicio de Pedidos

Microservicio Spring Boot para la gestión completa de pedidos del sistema QuickBite. Permite crear, gestionar y seguir el estado de los pedidos de clientes.

## 🚀 Características Principales

- **Gestión Completa de Pedidos**: CRUD completo con validaciones
- **Estados de Pedido**: Seguimiento del ciclo de vida del pedido
- **Items por Pedido**: Soporte para múltiples productos por pedido
- **Cálculos Automáticos**: Subtotal, impuestos (16%), total y costo de envío
- **Validaciones**: Bean Validation con mensajes en español
- **Manejo de Errores**: Excepciones personalizadas y manejo global
- **Base de Datos**: MySQL con Hibernate/JPA
- **API REST**: Endpoints completos con paginación
- **Logging**: Configurado para desarrollo

## 📋 Estados del Pedido

| Estado | Descripción |
|--------|-------------|
| `PENDIENTE` | Pedido creado, esperando confirmación |
| `CONFIRMADO` | Pedido confirmado, en preparación |
| `EN_PREPARACION` | Pedido siendo preparado en cocina |
| `LISTO` | Pedido listo para entrega |
| `EN_CAMINO` | Pedido en ruta de entrega |
| `ENTREGADO` | Pedido entregado exitosamente |
| `CANCELADO` | Pedido cancelado por cliente/restaurante |

## 💳 Métodos de Pago

- `EFECTIVO`
- `TARJETA_CREDITO`
- `TARJETA_DEBITO`
- `TRANSFERENCIA`
- `PAYPAL`
- `MERCADO_PAGO`

## 🛠️ Requisitos

- **Java 21**
- **Maven 3.8+**
- **MySQL 8.0+**
- **Spring Boot 4.0.5**

## ⚙️ Configuración

### Base de Datos

1. Crear base de datos en MySQL:
```sql
CREATE DATABASE quickbite_pedidos;
```

2. Configurar `application.properties`:
```properties
# Configuración de Base de Datos
spring.datasource.url=jdbc:mysql://localhost:3306/quickbite_pedidos?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# Configuración JPA
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect
```

### Puerto de Aplicación

- **Puerto por defecto**: `8082`
- **URL base**: `http://localhost:8082`

## 🚀 Ejecución

### Desarrollo

```bash
# Compilar y ejecutar
./mvnw spring-boot:run

# O compilar primero
./mvnw clean compile
./mvnw spring-boot:run
```

### Producción

```bash
# Build JAR
./mvnw clean package

# Ejecutar JAR
java -jar target/pedidos-0.0.1-SNAPSHOT.jar
```

## 📚 API Documentation

### Endpoints Principales

#### 📝 Crear Pedido
```http
POST /api/v1/pedidos
Content-Type: application/json

{
  "clienteId": 1,
  "nombreCliente": "Juan Perez",
  "emailCliente": "juan@quickbite.com",
  "telefonoCliente": "123456789",
  "direccionEntrega": "Calle Principal 123",
  "metodoPago": "EFECTIVO",
  "costoEnvio": 50.00,
  "notasCliente": "Sin cebolla por favor",
  "items": [
    {
      "productoId": 1,
      "nombreProducto": "Hamburguesa Clasica",
      "descripcionProducto": "Hamburguesa con carne, lechuga, tomate",
      "cantidad": 2,
      "precioUnitario": 15000,
      "notasItem": "Sin cebolla"
    }
  ]
}
```

**Respuesta (201 Created)**:
```json
{
  "id": 1,
  "numeroPedido": "PED-0186B64F",
  "clienteId": 1,
  "nombreCliente": "Juan Perez",
  "emailCliente": "juan@quickbite.com",
  "telefonoCliente": "123456789",
  "direccionEntrega": "Calle Principal 123",
  "estado": "PENDIENTE",
  "metodoPago": "EFECTIVO",
  "subtotal": 30000.00,
  "impuesto": 4800.00,
  "total": 35450.00,
  "costoEnvio": 50.00,
  "notasCliente": "Sin cebolla por favor",
  "notasRestaurante": null,
  "tiempoEstimadoMinutos": 30,
  "fechaCreacion": "2026-04-27T15:24:44",
  "fechaActualizacion": "2026-04-27T15:24:44",
  "fechaEntrega": null,
  "items": [
    {
      "id": 1,
      "pedidoId": 1,
      "productoId": 1,
      "nombreProducto": "Hamburguesa Clasica",
      "descripcionProducto": "Hamburguesa con carne, lechuga, tomate",
      "cantidad": 2,
      "precioUnitario": 15000.00,
      "precioTotal": 30000.00,
      "notasItem": "Sin cebolla"
    }
  ]
}
```

#### 🔍 Consultar Pedido por ID
```http
GET /api/v1/pedidos/{id}
```

#### 🔍 Consultar Pedido por Número
```http
GET /api/v1/pedidos/numero/{numeroPedido}
```

#### 👥 Pedidos por Cliente
```http
GET /api/v1/pedidos/cliente/{clienteId}
```

#### 📊 Pedidos por Estado
```http
GET /api/v1/pedidos/estado/{estado}
```

#### 📋 Listar Todos los Pedidos (Paginado)
```http
GET /api/v1/pedidos?page=0&size=10&sortBy=fechaCreacion&sortDir=desc
```

### Endpoints de Gestión

#### 🔄 Actualizar Estado del Pedido
```http
PUT /api/v1/pedidos/{id}/estado
Content-Type: application/json

{
  "estado": "CONFIRMADO"
}
```

#### 📝 Actualizar Notas del Restaurante
```http
PUT /api/v1/pedidos/{id}/notas
Content-Type: application/json

{
  "notas": "Cliente solicita entrega urgente"
}
```

#### ❌ Cancelar Pedido
```http
DELETE /api/v1/pedidos/{id}/cancelar
```

### Endpoints de Catálogos

#### 📋 Lista de Estados
```http
GET /api/v1/pedidos/estados
```

**Respuesta**:
```json
["PENDIENTE", "CONFIRMADO", "EN_PREPARACION", "LISTO", "EN_CAMINO", "ENTREGADO", "CANCELADO"]
```

#### 💳 Lista de Métodos de Pago
```http
GET /api/v1/pedidos/metodos-pago
```

**Respuesta**:
```json
["EFECTIVO", "TARJETA_CREDITO", "TARJETA_DEBITO", "TRANSFERENCIA", "PAYPAL", "MERCADO_PAGO"]
```

#### 📈 Estadísticas de Pedidos
```http
GET /api/v1/pedidos/estadisticas
```

## 🧪 Ejemplos con cURL

### Crear Pedido
```bash
curl -X POST http://localhost:8082/api/v1/pedidos \
  -H "Content-Type: application/json" \
  -d '{
    "clienteId": 1,
    "nombreCliente": "Juan Perez",
    "emailCliente": "juan@quickbite.com",
    "telefonoCliente": "123456789",
    "direccionEntrega": "Calle Principal 123",
    "metodoPago": "EFECTIVO",
    "costoEnvio": 50,
    "notasCliente": "Sin cebolla por favor",
    "items": [
      {
        "productoId": 1,
        "nombreProducto": "Hamburguesa Clasica",
        "descripcionProducto": "Hamburguesa con carne, lechuga, tomate",
        "cantidad": 2,
        "precioUnitario": 15000,
        "notasItem": "Sin cebolla"
      }
    ]
  }'
```

### Consultar Pedido
```bash
curl -X GET http://localhost:8082/api/v1/pedidos/1
```

### Actualizar Estado
```bash
curl -X PUT http://localhost:8082/api/v1/pedidos/1/estado \
  -H "Content-Type: application/json" \
  -d '{"estado": "CONFIRMADO"}'
```

### PowerShell (Windows)
```powershell
curl -Method POST -Uri "http://localhost:8082/api/v1/pedidos" `
  -Headers @{"Content-Type"="application/json"} `
  -Body '{"clienteId": 1, "nombreCliente": "Juan Perez", "emailCliente": "juan@quickbite.com", "telefonoCliente": "123456789", "direccionEntrega": "Calle Principal 123", "metodoPago": "EFECTIVO", "costoEnvio": 50, "notasCliente": "Sin cebolla por favor", "items": [{"productoId": 1, "nombreProducto": "Hamburguesa Clasica", "descripcionProducto": "Hamburguesa con carne, lechuga, tomate", "cantidad": 2, "precioUnitario": 15000, "notasItem": "Sin cebolla"}]}' `
  -UseBasicParsing
```

## 🏗️ Arquitectura

```
src/main/java/com/quickbite/pedidos/
├── controller/
│   └── PedidoController.java          # Endpoints REST
├── service/
│   └── PedidoService.java            # Lógica de negocio
├── repository/
│   ├── PedidoRepository.java         # Consultas JPA
│   └── ItemPedidoRepository.java     # Items de pedido
├── entity/
│   ├── Pedido.java                   # Entidad principal
│   └── ItemPedido.java               # Items del pedido
├── dto/
│   ├── PedidoRequest.java            # DTO de entrada
│   ├── PedidoResponse.java           # DTO de salida
│   ├── ItemPedidoRequest.java        # DTO item entrada
│   └── ItemPedidoResponse.java       # DTO item salida
├── exception/
│   ├── PedidoNotFoundException.java  # Excepción personalizada
│   ├── PedidoValidationException.java # Validación
│   └── GlobalExceptionHandler.java    # Manejo global
└── PedidosApplication.java           # Clase principal
```

## 📊 Base de Datos

### Tablas Generadas

#### `pedidos`
| Columna | Tipo | Descripción |
|---------|------|-------------|
| id | BIGINT | Primary Key |
| numero_pedido | VARCHAR(255) | Número único del pedido |
| cliente_id | BIGINT | ID del cliente |
| nombre_cliente | VARCHAR(255) | Nombre del cliente |
| email_cliente | VARCHAR(255) | Email del cliente |
| telefono_cliente | VARCHAR(255) | Teléfono del cliente |
| direccion_entrega | TEXT | Dirección de entrega |
| estado | VARCHAR(255) | Estado del pedido |
| metodo_pago | VARCHAR(255) | Método de pago |
| subtotal | DECIMAL(10,2) | Subtotal de items |
| impuesto | DECIMAL(10,2) | Impuesto (16%) |
| total | DECIMAL(10,2) | Total del pedido |
| costo_envio | DECIMAL(10,2) | Costo de envío |
| notas_cliente | TEXT | Notas del cliente |
| notas_restaurante | TEXT | Notas del restaurante |
| tiempo_estimado_minutos | INT | Tiempo estimado |
| fecha_creacion | TIMESTAMP | Fecha de creación |
| fecha_actualizacion | TIMESTAMP | Fecha de actualización |
| fecha_entrega | TIMESTAMP | Fecha de entrega |

#### `items_pedido`
| Columna | Tipo | Descripción |
|---------|------|-------------|
| id | BIGINT | Primary Key |
| pedido_id | BIGINT | FK a pedidos |
| producto_id | BIGINT | ID del producto |
| nombre_producto | VARCHAR(255) | Nombre del producto |
| descripcion_producto | TEXT | Descripción del producto |
| cantidad | INT | Cantidad solicitada |
| precio_unitario | DECIMAL(10,2) | Precio unitario |
| precio_total | DECIMAL(10,2) | Precio total |
| notas_item | TEXT | Notas del item |

## 🔒 Validaciones

### Validaciones de Pedido
- **clienteId**: Requerido, positivo
- **nombreCliente**: Requerido, 2-100 caracteres
- **emailCliente**: Email válido, máximo 100 caracteres
- **telefonoCliente**: Máximo 20 caracteres
- **direccionEntrega**: Requerida, 5-200 caracteres
- **metodoPago**: Requerido
- **costoEnvio**: No negativo
- **notasCliente**: Máximo 500 caracteres
- **items**: Requerido, al menos 1 item

### Validaciones de Item
- **productoId**: Requerido, positivo
- **nombreProducto**: Requerido, 2-100 caracteres
- **cantidad**: Requerida, 1-100
- **precioUnitario**: Requerido, mayor a 0
- **notasItem**: Máximo 200 caracteres

## 🚨 Manejo de Errores

### Formato de Respuesta de Error
```json
{
  "timestamp": "2026-04-27T15:24:36.642340700",
  "status": 404,
  "error": "Pedido no encontrado",
  "message": "Pedido no encontrado con ID: 999",
  "validationErrors": {},
  "path": ""
}
```

### Códigos de Error
- **400**: Error de validación
- **404**: Pedido no encontrado
- **500**: Error interno del servidor

## 📝 Logs

La aplicación genera logs detallados en consola:

```
2026-04-27 15:24:44 - Solicitud para crear nuevo pedido para cliente: Juan Perez
2026-04-27 15:24:44 - Pedido creado exitosamente con número: PED-0186B64F
2026-04-27 15:24:52 - Obteniendo pedido: PED-0186B64F
```

## 🔄 Integración

### Microservicios Relacionados
- **Autenticación** (puerto 8081): Gestión de usuarios y autenticación JWT
- **Pedidos** (puerto 8082): Gestión de pedidos (este servicio)

### Comunicación entre Servicios
```properties
# URLs de otros microservicios (si es necesario)
quickbite.auth.url=http://localhost:8081
quickbite.auth.validate-token=/api/v1/auth/validate
```

## 🐳 Docker (Opcional)

```dockerfile
FROM openjdk:21-jdk-slim
COPY target/pedidos-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8082
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

## 🧪 Testing

### Ejecutar Tests
```bash
./mvnw test
```

### Tests de Integración
```bash
./mvnw test -P integration-test
```

## 📈 Monitoreo

### Endpoints de Actuator (si se habilitan)
- `/actuator/health` - Estado del servicio
- `/actuator/info` - Información del servicio
- `/actuator/metrics` - Métricas

## 🤝 Contribución

1. Fork del repositorio
2. Crear feature branch: `git checkout -b feature/nueva-funcionalidad`
3. Commit cambios: `git commit -m 'Agregar nueva funcionalidad'`
4. Push: `git push origin feature/nueva-funcionalidad`
5. Pull Request

## 📄 Licencia

Este proyecto es parte del sistema QuickBite y está licenciado bajo los términos de la empresa.

## 📞 Soporte

Para soporte técnico o preguntas:
- **Repositorio**: https://github.com/Eslayer/Quickbite-pedidos
- **Issues**: Reportar en GitHub
- **Documentación**: Este README

---

**QuickBite Pedidos** - Gestión eficiente de pedidos para restaurantes modernos 🍔🚀
