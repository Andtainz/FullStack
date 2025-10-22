GROW SHOP
Evualacion2


# Backend API REST - Vivitasol Project

## 📋 Resumen de la Arquitectura

## 🗂️ Entidades Creadas

### 1. **Usuario.java**
- Gestión de usuarios registrados y administradores
- Campos: `id`, `nombre`, `correo`, `password`, `fechaRegistro`, `activo`, `esAdmin`
- Validaciones: correo único, usuarios activos/inactivos

### 2. **Carrito.java**
- Carrito de compras por usuario
- Relación OneToMany con `ItemCarrito`
- Métodos auxiliares: `calcularSubtotal()`, `calcularEnvio()`, `calcularTotal()`, `getTotalItems()`
- Lógica: envío gratis sobre $50.000

### 3. **ItemCarrito.java**
- Items individuales dentro del carrito
- Relación ManyToOne con `Carrito` y `Producto`
- Campos: `cantidad`, `precio` (al momento de agregar)
- Método: `getSubtotal()`

### 4. **Contacto.java**
- Mensajes de formulario de contacto
- Campos: `nombre`, `correo`, `telefono`, `mensaje`, `fechaEnvio`, `leido`, `respondido`
- Gestión de estado de mensajes

---

## 🔧 Servicios Implementados

### UsuarioServices
```java
- registrar(Usuario)              // Crear nuevo usuario
- login(correo, password)         // Autenticación
- obtenerId(Long)                 // Buscar por ID
- obtenerPorCorreo(String)        // Buscar por email
- listarTodos()                   // Listar usuarios
- actualizar(id, Usuario)         // Actualizar datos
- eliminar(Long)                  // Eliminar usuario
- existeCorreo(String)            // Validar email único
- cambiarEstado(id, Boolean)      // Activar/Desactivar
```

### CarritoServices
```java
- obtenerCarritoUsuario(usuarioId)         // Obtener o crear carrito
- agregarProducto(usuarioId, productoId, cantidad)  // Agregar al carrito
- actualizarCantidad(usuarioId, itemId, cantidad)  // Cambiar cantidad
- eliminarItem(usuarioId, itemId)          // Eliminar producto
- vaciarCarrito(usuarioId)                 // Vaciar todo
- finalizarCompra(usuarioId)               // Checkout
```

### ContactoServices
```java
- crear(Contacto)              // Crear mensaje
- obtenerId(Long)              // Obtener mensaje
- listarTodos()                // Listar todos
- listarNoLeidos()             // Mensajes sin leer
- listarNoRespondidos()        // Mensajes sin responder
- marcarComoLeido(Long)        // Marcar leído
- marcarComoRespondido(Long)   // Marcar respondido
- eliminar(Long)               // Eliminar mensaje
```

---

## 🌐 Endpoints REST

### **Usuarios** - `/api/usuarios`

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| POST | `/registro` | Registrar nuevo usuario |
| POST | `/login` | Autenticar usuario |
| GET | `/existe-correo?correo={email}` | Verificar si existe email |
| GET | `/{id}` | Obtener usuario por ID |
| GET | `/correo/{correo}` | Obtener usuario por email |
| GET | `/` | Listar todos los usuarios |
| PUT | `/{id}` | Actualizar usuario |
| PATCH | `/{id}/estado` | Cambiar estado activo/inactivo |
| DELETE | `/{id}` | Eliminar usuario |

### **Carrito** - `/api/carrito`

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/usuario/{usuarioId}` | Obtener carrito del usuario |
| POST | `/usuario/{usuarioId}/agregar` | Agregar producto al carrito |
| PATCH | `/usuario/{usuarioId}/item/{itemId}` | Actualizar cantidad |
| DELETE | `/usuario/{usuarioId}/item/{itemId}` | Eliminar item |
| DELETE | `/usuario/{usuarioId}/vaciar` | Vaciar carrito |
| POST | `/usuario/{usuarioId}/checkout` | Finalizar compra |

### **Contacto** - `/api/contacto`

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| POST | `/` | Crear mensaje de contacto |
| GET | `/{id}` | Obtener mensaje por ID |
| GET | `/` | Listar todos los mensajes |
| GET | `/no-leidos` | Listar no leídos |
| GET | `/no-respondidos` | Listar no respondidos |
| PATCH | `/{id}/leido` | Marcar como leído |
| PATCH | `/{id}/respondido` | Marcar como respondido |
| DELETE | `/{id}` | Eliminar mensaje |

---

## 📦 Ejemplos de Uso

### Registrar Usuario
```json
POST /api/usuarios/registro
{
  "nombre": "Juan Pérez",
  "correo": "juan@example.com",
  "password": "123456"
}
```

### Login
```json
POST /api/usuarios/login
{
  "correo": "juan@example.com",
  "password": "123456"
}
```

### Agregar Producto al Carrito
```json
POST /api/carrito/usuario/1/agregar
{
  "productoId": 5,
  "cantidad": 2
}
```

### Actualizar Cantidad
```json
PATCH /api/carrito/usuario/1/item/3
{
  "cantidad": 5
}
```

### Crear Mensaje de Contacto
```json
POST /api/contacto
{
  "nombre": "María López",
  "correo": "maria@example.com",
  "telefono": "+56912345678",
  "mensaje": "Consulta sobre productos"
}
```

---

## 🔐 Características de Seguridad

- ✅ Validación de correo único en registro
- ✅ Verificación de contraseña en login
- ✅ Control de usuarios activos/inactivos
- ✅ Verificación de productos activos antes de agregar al carrito
- ✅ Validaciones de cantidad (debe ser > 0)
- ✅ Control de existencia de entidades (RuntimeException si no existen)

---

## 📊 Lógica de Negocio

### Carrito
- Envío gratis sobre $50.000
- Envío: $3.500 bajo $50.000
- Si un producto ya existe en el carrito, se suma la cantidad
- Al finalizar compra, el carrito se marca como inactivo

### Usuarios
- No se pueden registrar emails duplicados
- Los usuarios inactivos no pueden hacer login
- Admins tienen flag especial `esAdmin`

### Contacto
- Auto-marcado de fecha de envío
- Estados: leído/no leído, respondido/no respondido
- Filtros para gestión de mensajes

---

## 🚀 Próximos Pasos

1. **Agregar estas dependencias** al `pom.xml` si no las tienes:
   ```xml
   <!-- Ya las tienes todas en tu proyecto actual -->
   ```

2. **Crear el archivo `application.properties`**:
   ```properties
   spring.datasource.url=jdbc:mysql://localhost:3306/vivitasol_db
   spring.datasource.username=root
   spring.datasource.password=tu_password
   spring.jpa.hibernate.ddl-auto=update
   spring.jpa.show-sql=true
   ```

3. **Ejecutar el proyecto** y las tablas se crearán automáticamente

4. **Conectar tu frontend** cambiando localStorage por llamadas fetch a estos endpoints

---

## 📝 Notas Importantes

- Todos los controladores tienen `@CrossOrigin` para localhost:5173
- Todas las entidades usan Lombok (`@Data`, `@NoArgsConstructor`, `@AllArgsConstructor`)
- Seguí exactamente el mismo patrón de tu código existente
- Las contraseñas se guardan en texto plano (en producción usar bcrypt)
- Los cálculos del carrito están en la entidad misma (métodos auxiliares)

---

## 🎯 Ventajas de esta Arquitectura

✅ **Persistencia real** - Los datos se guardan en MySQL en lugar de localStorage  
✅ **Escalabilidad** - Múltiples usuarios pueden usar el sistema simultáneamente  
✅ **Seguridad** - Control centralizado de acceso y validaciones  
✅ **Mantenibilidad** - Código organizado en capas bien definidas  
✅ **Transaccionalidad** - Operaciones atómicas en el carrito  
✅ **RESTful** - API estándar fácil de consumir desde cualquier cliente
message.txt
message.txt (8 KB)
8 KB
