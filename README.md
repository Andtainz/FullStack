GROW SHOP
Evualacion2

# 🌿 Sativamente - E-commerce de Productos de Cultivo

## 📋 Tabla de Contenidos

1. [Características](#-características)
2. [Tecnologías Utilizadas](#-tecnologías-utilizadas)
3. [Requisitos Previos](#-requisitos-previos)
4. [Instalación](#-instalación)
5. [Configuración](#-configuración)
6. [Uso](#-uso)
7. [Estructura del Proyecto](#-estructura-del-proyecto)
8. [API Endpoints](#-api-endpoints)
9. [Base de Datos](#-base-de-datos)
10. [Funcionalidades](#-funcionalidades)

---

## ✨ Características

### Usuario Final
- 🏠 **Página de inicio** con productos destacados
- 🛍️ **Catálogo de productos** con filtros por categoría y búsqueda
- 🛒 **Carrito de compras** con gestión de cantidades
- 📱 **Diseño responsive** compatible con móviles
- 📝 **Formulario de contacto** con validaciones
- 📰 **Blog/Noticias** sobre cannabis y cultivo
- 👤 **Sistema de registro y login** de usuarios

### Panel de Administración
- ➕ **Crear, editar y eliminar** productos
- 🏷️ **Gestión de categorías**
- 🔄 **Activar/Desactivar** productos
- 📊 **Visualización de inventario**
- 🔐 **Acceso protegido** con contraseña

---

## 🛠️ Tecnologías Utilizadas

### Frontend
- **React 18** - Biblioteca de JavaScript
- **React Router DOM** - Navegación entre páginas
- **Vite** - Herramienta de construcción rápida
- **Bootstrap 5** - Framework CSS
- **Fetch API** - Comunicación con el backend

### Backend
- **Spring Boot 3.5.6** - Framework de Java
- **Spring Data JPA** - Persistencia de datos
- **Spring Web** - API REST
- **Lombok** - Reducción de código boilerplate
- **Maven** - Gestión de dependencias

### Base de Datos
- **MySQL 8.0** / **MariaDB 10.4**
- **JPA/Hibernate** - ORM

---

## 📦 Requisitos Previos

Antes de comenzar, asegúrate de tener instalado:

- **Node.js** (v16 o superior) - [Descargar](https://nodejs.org/)
- **Java 21** - [Descargar](https://www.oracle.com/java/technologies/downloads/)
- **MySQL** o **MariaDB** - [Descargar MySQL](https://dev.mysql.com/downloads/)
- **Maven** (opcional, viene incluido en el proyecto)
- **Git** - [Descargar](https://git-scm.com/)

---

## 🚀 Instalación

### 1. Clonar el repositorio
```bash
git clone https://github.com/tu-usuario/sativamente.git
cd sativamente
```

### 2. Configurar la Base de Datos

**Crear la base de datos:**
```sql
CREATE DATABASE base CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
USE base;
```

**Importar el esquema inicial:**
```bash
mysql -u root -p base < basesativamente2.0.sql
```

O ejecuta el script SQL manualmente desde MySQL Workbench o phpMyAdmin.

### 3. Configurar el Backend

**Navegar a la carpeta del backend:**
```bash
cd backend
```

**Crear archivo `application.properties` en `src/main/resources/`:**
```properties
spring.application.name=projectbackend

# Configuración de MySQL
spring.datasource.url=jdbc:mysql://localhost:3306/base?useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=tu_password_aqui
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA/Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect

# Puerto del servidor
server.port=8080
```

**Compilar y ejecutar:**
```bash
# Con Maven Wrapper (recomendado)
./mvnw spring-boot:run

# O con Maven instalado globalmente
mvn spring-boot:run
```

El backend estará disponible en: `http://localhost:8080`

### 4. Configurar el Frontend

**Navegar a la carpeta del frontend:**
```bash
cd ../frontend
```

**Instalar dependencias:**
```bash
npm install
```

**Ejecutar en modo desarrollo:**
```bash
npm run dev
```

El frontend estará disponible en: `http://localhost:5173`

---

## ⚙️ Configuración

### Variables de Entorno (Opcional)

**Frontend - crear archivo `.env`:**
```env
VITE_API_URL=http://localhost:8080/api
```

**Backend - `application.properties`:**
```properties
# Configuración personalizada
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/base
spring.datasource.username=root
spring.datasource.password=tu_password
```

---

## 📖 Uso

### Acceso a la Aplicación

1. **Página Principal**: `http://localhost:5173/`
2. **Catálogo de Productos**: `http://localhost:5173/productos`
3. **Carrito**: `http://localhost:5173/carrito`
4. **Panel Admin**: `http://localhost:5173/admin` (contraseña: `admin123`)

### Credenciales de Prueba

**Administrador:**
- Contraseña: `admin123`

**Usuario de Base de Datos:**
- Usuario: `Jean`
- Contraseña: `duoc`

---

## 📁 Estructura del Proyecto

```
sativamente/
│
├── backend/                          # Spring Boot Backend
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/vivitasol/projectbackend/
│   │   │   │   ├── controllers/      # REST Controllers
│   │   │   │   │   ├── ProductoRestControllers.java
│   │   │   │   │   └── CategoriaRestControllers.java
│   │   │   │   ├── entities/         # Entidades JPA
│   │   │   │   │   ├── Producto.java
│   │   │   │   │   └── Categoria.java
│   │   │   │   ├── repositories/     # Interfaces de Repositorio
│   │   │   │   │   ├── ProductoRepositories.java
│   │   │   │   │   └── CategoriaRepositories.java
│   │   │   │   ├── services/         # Lógica de Negocio
│   │   │   │   │   ├── ProductoServices.java
│   │   │   │   │   ├── ProductoServicesImpl.java
│   │   │   │   │   ├── CategoriaServices.java
│   │   │   │   │   └── CategoriaServicesImpl.java
│   │   │   │   └── ProjectbackendApplication.java
│   │   │   └── resources/
│   │   │       └── application.properties
│   │   └── test/
│   └── pom.xml
│
└── frontend/                         # React Frontend
    ├── src/
    │   ├── api/
    │   │   └── apiService.js         # Servicio de API
    │   ├── components/               # Componentes reutilizables
    │   │   ├── Navbar.jsx
    │   │   ├── Footer.jsx
    │   │   └── ProductCard.jsx
    │   ├── pages/                    # Páginas de la aplicación
    │   │   ├── Home.jsx
    │   │   ├── Productos.jsx
    │   │   ├── DetalleProducto.jsx
    │   │   ├── Carrito.jsx
    │   │   ├── Admin.jsx
    │   │   ├── Login.jsx
    │   │   ├── Registro.jsx
    │   │   ├── Nosotros.jsx
    │   │   ├── Blog.jsx
    │   │   └── Contacto.jsx
    │   ├── App.jsx                   # Componente principal
    │   ├── main.jsx                  # Punto de entrada
    │   ├── index.css                 # Estilos globales
    │   └── App.css
    ├── package.json
    └── vite.config.js
```

---

## 🔌 API Endpoints

### Productos

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| `GET` | `/api/productos` | Listar todos los productos |
| `GET` | `/api/productos/{id}` | Obtener producto por ID |
| `POST` | `/api/productos` | Crear nuevo producto |
| `PUT` | `/api/productos/{id}` | Actualizar producto |
| `DELETE` | `/api/productos/{id}` | Eliminar producto |
| `PATCH` | `/api/productos/{id}/desactivar` | Desactivar producto |

### Categorías

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| `GET` | `/api/categorias` | Listar todas las categorías |
| `GET` | `/api/categorias/{id}` | Obtener categoría por ID |
| `POST` | `/api/categorias` | Crear nueva categoría |
| `PUT` | `/api/categorias/{id}` | Actualizar categoría |
| `DELETE` | `/api/categorias/{id}` | Eliminar categoría |

### Ejemplos de Uso

**Crear un producto:**
```bash
curl -X POST http://localhost:8080/api/productos \
  -H "Content-Type: application/json" \
  -d '{
    "nombre": "Bong de vidrio",
    "descripcion": "Bong de alta calidad",
    "precio": 15000,
    "stock": 10,
    "activo": true,
    "categoria": { "id": 7 }
  }'
```

**Obtener todos los productos:**
```bash
curl http://localhost:8080/api/productos
```

---

## 🗄️ Base de Datos

### Modelo de Datos

```
┌─────────────┐         ┌─────────────┐
│  categoria  │         │  producto   │
├─────────────┤         ├─────────────┤
│ id (PK)     │◄────────│ id (PK)     │
│ nombre      │   1:N   │ nombre      │
└─────────────┘         │ descripcion │
                        │ precio      │
                        │ stock       │
                        │ activo      │
                        │ categoria_id│
                        └─────────────┘

┌─────────────┐
│    user     │
├─────────────┤
│ id (PK)     │
│ nombre      │
│ password    │
└─────────────┘
```

### Categorías Disponibles

- **Fumadores** (ID: 7) - Bongs, máscaras, papeles
- **Cultivo** (ID: 8) - Indoor, macetas, tierra
- **Accesorios** (ID: 9) - Chisperos, encendedores
- **Protección** (ID: 10) - Termohigrómetros, máscaras
- **Iluminación** (ID: 11) - Extractores, reflectores LED

---

## 💡 Funcionalidades

### 1. Gestión de Productos

**Como administrador puedes:**
- ✅ Crear productos con nombre, descripción, precio y categoría
- ✅ Editar información de productos existentes
- ✅ Desactivar productos (soft delete)
- ✅ Eliminar productos permanentemente
- ✅ Ver inventario completo con filtros

### 2. Carrito de Compras

**Como usuario puedes:**
- ✅ Agregar productos al carrito
- ✅ Modificar cantidades
- ✅ Eliminar productos del carrito
- ✅ Ver subtotal y total de compra
- ✅ Envío gratis en compras sobre $30.000
- ✅ Persistencia en `localStorage`

### 3. Sistema de Usuarios

**Registro con validaciones:**
- RUN sin puntos ni guion
- Nombre máx. 50 caracteres
- Apellidos máx. 100 caracteres
- Email: `@duoc.cl`, `@profesor.duoc.cl`, `@gmail.com`
- Contraseña: 4-10 caracteres
- Dirección máx. 300 caracteres

**Login:**
- Validación de credenciales
- Sesión con `sessionStorage`

### 4. Formulario de Contacto

- Validación de dominios de email
- Nombre máx. 100 caracteres
- Mensaje máx. 500 caracteres
- Guardado en `localStorage`

---

## 🐛 Solución de Problemas

### Error: Puerto 8080 ya está en uso
```bash
# Windows
netstat -ano | findstr :8080
taskkill /PID <PID> /F

# Linux/Mac
lsof -ti:8080 | xargs kill -9
```

### Error: Cannot connect to MySQL
- Verifica que MySQL esté corriendo
- Revisa usuario y contraseña en `application.properties`
- Confirma que la base de datos `base` exista

### Error: CORS policy blocked
- Verifica que `@CrossOrigin(origins = "http://localhost:5173")` esté presente
- Confirma que el frontend corra en el puerto 5173

### Frontend no carga productos
- Verifica que el backend esté corriendo en puerto 8080
- Revisa la consola del navegador para errores
- Confirma la URL de la API en `apiService.js`

---

## 📝 Notas Importantes

1. **Contraseña de Admin**: `admin123` (solo en frontend, no hay autenticación real en backend)
2. **Base de datos**: El script SQL incluye datos de prueba
3. **CORS**: Configurado solo para `localhost:5173`
4. **localStorage**: Se usa para carrito y mensajes de contacto
5. **sessionStorage**: Se usa para sesión de usuario

---

## 🚧 Mejoras Futuras

- [ ] Implementar JWT para autenticación real
- [ ] Agregar paginación en listado de productos
- [ ] Sistema de roles (Admin, Usuario)
- [ ] Integración con pasarelas de pago
- [ ] Envío de correos de confirmación
- [ ] Historial de pedidos
- [ ] Valoraciones y comentarios de productos
- [ ] Integración con sistema de envío
- [ ] Panel de estadísticas para admin

---

## 👥 Equipo de Desarrollo

- **Jean Flores** - Product Owner
- **Maria Ortis** - Especialista en Cultivo / Dueña Growshop
- **Joshua Mardones** - Scrum Master

---

## 📄 Licencia

Este proyecto es de código abierto y está disponible bajo la licencia MIT.

---

## 📞 Contacto

- **Email**: info@sativamente.com
- **Teléfono**: +56 9 1234 5678
- **Horario**: Lunes a Viernes de 9:00 a 18:00 hrs

---

## 🎯 Comandos Rápidos

```bash
# Backend
cd backend
./mvnw clean install
./mvnw spring-boot:run

# Frontend
cd frontend
npm install
npm run dev

# Base de Datos
mysql -u root -p base < basesativamente2.0.sql

# Compilar para producción
npm run build
```

---

**¡ Sativamente! 🌿**
