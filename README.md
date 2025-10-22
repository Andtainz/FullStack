GROW SHOP
Evualacion2


# 🌿 Sativamente - E-commerce de Productos de Cultivo

Aplicación web fullstack para la venta de productos relacionados con el cultivo de plantas. Incluye catálogo de productos, carrito de compras, panel de administración y gestión de usuarios.

---

## 📋 Tabla de Contenidos

1. [Características](#-características)
2. [Arquitectura](#-arquitectura)
3. [Tecnologías Utilizadas](#-tecnologías-utilizadas)
4. [Requisitos Previos](#-requisitos-previos)
5. [Instalación](#-instalación)
6. [Configuración](#-configuración)
7. [Uso](#-uso)
8. [Estructura del Proyecto](#-estructura-del-proyecto)
9. [API Endpoints](#-api-endpoints)
10. [Base de Datos](#-base-de-datos)
11. [Funcionalidades](#-funcionalidades)

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

## 🏗️ Arquitectura

### Arquitectura General del Sistema

El proyecto **Sativamente** utiliza una **arquitectura cliente-servidor de 3 capas** con comunicación mediante API REST:

```
┌─────────────────────────────────────────────────────────────────┐
│                         NAVEGADOR WEB                           │
│                     (Puerto 5173 - Dev)                         │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ HTTP/HTTPS
                             │ (Fetch API)
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                      FRONTEND - REACT                           │
│  ┌───────────────┐  ┌──────────────┐  ┌──────────────────┐    │
│  │   Components  │  │    Pages     │  │   API Service    │    │
│  │  - Navbar     │  │  - Home      │  │  - apiService.js │    │
│  │  - Footer     │  │  - Productos │  │  - fetch calls   │    │
│  │  - ProductCard│  │  - Admin     │  │  - handleResponse│    │
│  │               │  │  - Carrito   │  │                  │    │
│  └───────────────┘  └──────────────┘  └──────────────────┘    │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │          React Router (Navegación SPA)                   │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │    Estado Local (useState, useEffect)                    │  │
│  │    Almacenamiento (localStorage, sessionStorage)         │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ REST API
                             │ JSON (GET, POST, PUT, DELETE, PATCH)
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                   BACKEND - SPRING BOOT                         │
│                    (Puerto 8080)                                │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              CAPA DE PRESENTACIÓN                        │  │
│  │  @RestController (REST API Endpoints)                    │  │
│  │  ┌─────────────────────┐  ┌──────────────────────────┐  │  │
│  │  │ ProductoRestCtrl    │  │ CategoriaRestCtrl        │  │  │
│  │  │ - GET /productos    │  │ - GET /categorias        │  │  │
│  │  │ - POST /productos   │  │ - POST /categorias       │  │  │
│  │  │ - PUT /productos    │  │ - PUT /categorias        │  │  │
│  │  │ - DELETE /productos │  │ - DELETE /categorias     │  │  │
│  │  │ - PATCH /desactivar │  │                          │  │  │
│  │  └─────────────────────┘  └──────────────────────────┘  │  │
│  │                                                          │  │
│  │  ┌──────────────────────────────────────────────────┐   │  │
│  │  │      @CrossOrigin (CORS Configuration)           │   │  │
│  │  └──────────────────────────────────────────────────┘   │  │
│  └──────────────────────────────────────────────────────────┘  │
│                             │                                   │
│                             ▼                                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              CAPA DE LÓGICA DE NEGOCIO                   │  │
│  │  @Service (Business Logic)                               │  │
│  │  ┌──────────────────────┐  ┌───────────────────────┐    │  │
│  │  │ ProductoServices     │  │ CategoriaServices     │    │  │
│  │  │ ProductoServicesImpl │  │ CategoriaServicesImpl │    │  │
│  │  │ - crear()            │  │ - crear()             │    │  │
│  │  │ - obtenerId()        │  │ - obtenerId()         │    │  │
│  │  │ - listarTodas()      │  │ - listarTodas()       │    │  │
│  │  │ - actualizar()       │  │ - actualizar()        │    │  │
│  │  │ - eliminar()         │  │ - eliminar()          │    │  │
│  │  │ - desactivar()       │  │                       │    │  │
│  │  └──────────────────────┘  └───────────────────────┘    │  │
│  └──────────────────────────────────────────────────────────┘  │
│                             │                                   │
│                             ▼                                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              CAPA DE ACCESO A DATOS                      │  │
│  │  @Repository (Spring Data JPA)                           │  │
│  │  ┌────────────────────────┐  ┌──────────────────────┐   │  │
│  │  │ ProductoRepositories   │  │ CategoriaRepositories│   │  │
│  │  │ extends CrudRepository │  │ extends CrudRepository│  │  │
│  │  │ - save()               │  │ - save()             │   │  │
│  │  │ - findById()           │  │ - findById()         │   │  │
│  │  │ - findAll()            │  │ - findAll()          │   │  │
│  │  │ - deleteById()         │  │ - deleteById()       │   │  │
│  │  │ - existsById()         │  │ - existsById()       │   │  │
│  │  └────────────────────────┘  └──────────────────────┘   │  │
│  └──────────────────────────────────────────────────────────┘  │
│                             │                                   │
│                             ▼                                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                   CAPA DE ENTIDADES                      │  │
│  │  @Entity (JPA/Hibernate)                                 │  │
│  │  ┌───────────────┐         ┌──────────────┐             │  │
│  │  │  Producto     │         │  Categoria   │             │  │
│  │  │  - id         │         │  - id        │             │  │
│  │  │  - nombre     │         │  - nombre    │             │  │
│  │  │  - descripcion│         │  - productos │             │  │
│  │  │  - precio     │         └──────────────┘             │  │
│  │  │  - stock      │                │                     │  │
│  │  │  - activo     │                │                     │  │
│  │  │  - categoria ─┼────────────────┘                     │  │
│  │  └───────────────┘         @ManyToOne                   │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ JDBC
                             │ Hibernate/JPA
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                   BASE DE DATOS - MySQL                         │
│                    (Puerto 3306)                                │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Tablas:                                                 │  │
│  │  - producto (id, nombre, descripcion, precio,           │  │
│  │              stock, activo, categoria_id)               │  │
│  │  - categoria (id, nombre)                               │  │
│  │  - user (id, nombre, password)                          │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

### Patrón de Diseño: MVC + Capas

El backend utiliza una variante del patrón **MVC (Modelo-Vista-Controlador)** adaptado a APIs REST:

```
┌─────────────────────────────────────────────────────────┐
│                      FRONTEND                           │
│                   (Actúa como Vista)                    │
└────────────────────────┬────────────────────────────────┘
                         │
                         │ HTTP Requests
                         ▼
┌─────────────────────────────────────────────────────────┐
│                 CONTROLADOR (@RestController)           │
│  - Recibe peticiones HTTP                              │
│  - Valida datos de entrada                             │
│  - Llama a servicios                                   │
│  - Retorna ResponseEntity<T>                           │
└────────────────────────┬────────────────────────────────┘
                         │
                         │ Invoca métodos
                         ▼
┌─────────────────────────────────────────────────────────┐
│                 SERVICIO (@Service)                     │
│  - Implementa lógica de negocio                        │
│  - Maneja transacciones                                │
│  - Valida reglas de negocio                            │
│  - Coordina acceso a datos                             │
└────────────────────────┬────────────────────────────────┘
                         │
                         │ CRUD Operations
                         ▼
┌─────────────────────────────────────────────────────────┐
│              REPOSITORIO (@Repository)                  │
│  - Interfaz Spring Data JPA                            │
│  - Extiende CrudRepository                             │
│  - Métodos de acceso a BD                              │
└────────────────────────┬────────────────────────────────┘
                         │
                         │ ORM (Hibernate)
                         ▼
┌─────────────────────────────────────────────────────────┐
│                  MODELO (@Entity)                       │
│  - Representa tablas de BD                             │
│  - Mapeo objeto-relacional                             │
│  - Relaciones entre entidades                          │
└────────────────────────┬────────────────────────────────┘
                         │
                         │ SQL Queries
                         ▼
┌─────────────────────────────────────────────────────────┐
│                   BASE DE DATOS                         │
│  - MySQL / MariaDB                                     │
│  - Persistencia de datos                               │
└─────────────────────────────────────────────────────────┘
```

---

### Flujo de Datos Completo

#### Ejemplo: Agregar Producto al Carrito

```
┌──────────┐                                      ┌──────────┐
│ Usuario  │                                      │ Frontend │
└────┬─────┘                                      └────┬─────┘
     │                                                 │
     │ 1. Click "Añadir al carrito"                   │
     ├────────────────────────────────────────────────►
     │                                                 │
     │                        2. useState actualiza   │
     │                           cantidad local       │
     │                                                 │
     │                        3. Llamada a apiService │
     │                           getProductoById(id)  │
     │                                                 ▼
     │                                          ┌──────────────┐
     │                                          │   Backend    │
     │                           4. GET /api/productos/{id}    │
     │                                          └──────┬───────┘
     │                                                 │
     │                                   5. Controller recibe  │
     │                                      request             │
     │                                                 ▼
     │                                    ┌────────────────────┐
     │                                    │ ProductoServices   │
     │                                    │ obtenerId(id)      │
     │                                    └──────┬─────────────┘
     │                                           │
     │                                 6. Repository consulta  │
     │                                    BD                   │
     │                                           ▼
     │                                    ┌──────────────┐
     │                                    │   MySQL      │
     │                                    │ SELECT * FROM│
     │                                    │ producto...  │
     │                                    └──────┬───────┘
     │                                           │
     │                                 7. Retorna Entity      │
     │                                           ▼
     │                              ┌────────────────────────┐
     │                              │ Controller serializa   │
     │                              │ Producto a JSON        │
     │                              └──────┬─────────────────┘
     │                                     │
     │                        8. Response JSON                │
     │                           { id, nombre, precio... }    │
     ◄────────────────────────────────────┘                   │
     │                                                         │
     │                        9. Frontend guarda en carrito   │
     │                           localStorage.setItem(...)    │
     │                                                         │
     │                        10. useEffect actualiza Navbar  │
     │                            contador                     │
     │                                                         │
     │ 11. UI actualizada - badge muestra cantidad            │
     ◄─────────────────────────────────────────────────────────┘
```

---

### Arquitectura de Frontend (React)

```
┌─────────────────────────────────────────────────────────────┐
│                    APLICACIÓN REACT                         │
├─────────────────────────────────────────────────────────────┤
│  main.jsx (Entry Point)                                     │
│  └─► BrowserRouter                                          │
│      └─► App.jsx                                            │
│          ├─► Navbar (Componente Global)                     │
│          │   └─► useState para carrito                      │
│          │       └─► localStorage listener                  │
│          │                                                   │
│          ├─► Routes                                         │
│          │   ├─► / → Home                                   │
│          │   ├─► /productos → Productos                     │
│          │   ├─► /productos/:id → DetalleProducto          │
│          │   ├─► /carrito → Carrito                        │
│          │   ├─► /admin → Admin                            │
│          │   ├─► /login → Login                            │
│          │   ├─► /registro → Registro                      │
│          │   ├─► /nosotros → Nosotros                      │
│          │   ├─► /blog → Blog                              │
│          │   └─► /contacto → Contacto                      │
│          │                                                   │
│          └─► Footer (Componente Global)                     │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│  GESTIÓN DE ESTADO                                          │
├─────────────────────────────────────────────────────────────┤
│  React Hooks:                                               │
│  • useState - Estado local de componentes                   │
│  • useEffect - Side effects (fetch, listeners)              │
│  • useParams - Parámetros de ruta                          │
│  • useNavigate - Navegación programática                    │
│                                                              │
│  Almacenamiento del Navegador:                              │
│  • localStorage - Carrito, mensajes, usuarios               │
│  • sessionStorage - Sesión de usuario activo                │
│                                                              │
│  Event System:                                              │
│  • window.addEventListener('storage')                       │
│  • window.addEventListener('carritoActualizado')            │
│  • window.dispatchEvent(new Event(...))                     │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│  CAPA DE COMUNICACIÓN                                       │
├─────────────────────────────────────────────────────────────┤
│  apiService.js:                                             │
│  • Centraliza todas las peticiones HTTP                     │
│  • Usa Fetch API                                            │
│  • Manejo de errores centralizado                           │
│  • Formateo de datos para el backend                        │
│                                                              │
│  Métodos principales:                                       │
│  - getProductos()                                           │
│  - getProductoById(id)                                      │
│  - crearProducto(producto)                                  │
│  - actualizarProducto(id, producto)                         │
│  - eliminarProducto(id)                                     │
│  - desactivarProducto(id)                                   │
│  - getCategorias()                                          │
│  - crearCategoria(categoria)                                │
│  - actualizarCategoria(id, categoria)                       │
│  - eliminarCategoria(id)                                    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

### Arquitectura de Backend (Spring Boot)

```
┌─────────────────────────────────────────────────────────────┐
│              SPRING BOOT APPLICATION                        │
├─────────────────────────────────────────────────────────────┤
│  ProjectbackendApplication.java                             │
│  @SpringBootApplication                                     │
│  └─► SpringApplication.run(...)                            │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│  CAPA WEB - REST CONTROLLERS                                │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ProductoRestControllers.java                               │
│  @RestController                                            │
│  @RequestMapping("/api/productos")                          │
│  @CrossOrigin(origins = "http://localhost:5173")            │
│  └─► @Autowired ProductoServices                            │
│                                                              │
│  Endpoints:                                                 │
│  • POST   /api/productos                                    │
│  • GET    /api/productos/{id}                               │
│  • GET    /api/productos                                    │
│  • PUT    /api/productos/{id}                               │
│  • DELETE /api/productos/{id}                               │
│  • PATCH  /api/productos/{id}/desactivar                    │
│                                                              │
│  CategoriaRestControllers.java                              │
│  @RestController                                            │
│  @RequestMapping("/api/categorias")                         │
│  @CrossOrigin(origins = "http://localhost:5173")            │
│  └─► @Autowired CategoriaServices                           │
│                                                              │
│  Endpoints:                                                 │
│  • POST   /api/categorias                                   │
│  • GET    /api/categorias/{id}                              │
│  • GET    /api/categorias                                   │
│  • PUT    /api/categorias/{id}                              │
│  • DELETE /api/categorias/{id}                              │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│  CAPA DE SERVICIO - BUSINESS LOGIC                          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ProductoServices.java (Interface)                          │
│  └─► ProductoServicesImpl.java                              │
│       @Service                                              │
│       @Autowired ProductoRepositories                       │
│                                                              │
│       Métodos:                                              │
│       • crear(Producto)                                     │
│       • obtenerId(Long id)                                  │
│       • listarTodas()                                       │
│       • actualizar(Long id, Producto)                       │
│       • eliminar(Long id)                                   │
│       • desactivar(Long id)                                 │
│                                                              │
│  CategoriaServices.java (Interface)                         │
│  └─► CategoriaServicesImpl.java                             │
│       @Service                                              │
│       @Autowired CategoriaRepositories                      │
│                                                              │
│       Métodos:                                              │
│       • crear(Categoria)                                    │
│       • obtenerId(Long id)                                  │
│       • listarTodas()                                       │
│       • actualizar(Long id, Categoria)                      │
│       • eliminar(Long id)                                   │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│  CAPA DE PERSISTENCIA - REPOSITORIES                        │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ProductoRepositories.java                                  │
│  extends CrudRepository<Producto, Long>                     │
│                                                              │
│  Métodos heredados (Spring Data JPA):                       │
│  • save(S entity)                                           │
│  • findById(ID id)                                          │
│  • findAll()                                                │
│  • deleteById(ID id)                                        │
│  • existsById(ID id)                                        │
│  • count()                                                  │
│                                                              │
│  CategoriaRepositories.java                                 │
│  extends CrudRepository<Categoria, Long>                    │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│  CAPA DE DOMINIO - ENTITIES                                 │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Producto.java                                              │
│  @Entity                                                    │
│  @Data (Lombok)                                             │
│  @NoArgsConstructor                                         │
│  @AllArgsConstructor                                        │
│                                                              │
│  Atributos:                                                 │
│  • Long id (@Id @GeneratedValue)                            │
│  • String nombre                                            │
│  • String descripcion                                       │
│  • Long precio                                              │
│  • Integer stock                                            │
│  • Boolean activo                                           │
│  • Categoria categoria (@ManyToOne)                         │
│                                                              │
│  Categoria.java                                             │
│  @Entity                                                    │
│  @Data (Lombok)                                             │
│  @NoArgsConstructor                                         │
│  @AllArgsConstructor                                        │
│                                                              │
│  Atributos:                                                 │
│  • Long id (@Id @GeneratedValue)                            │
│  • String nombre                                            │
│  • List<Producto> productos (@OneToMany @JsonIgnore)        │
│                                                              │
├─────────────────────────────────────────────────────────────┤
│  CONFIGURACIÓN Y DEPENDENCIAS                               │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  pom.xml - Maven Dependencies:                              │
│  • spring-boot-starter-web                                  │
│  • spring-boot-starter-data-jpa                             │
│  • spring-boot-starter-actuator                             │
│  • spring-boot-devtools                                     │
│  • mysql-connector-j                                        │
│  • lombok                                                   │
│  • spring-boot-starter-test                                 │
│                                                              │
│  application.properties:                                    │
│  • spring.datasource.url                                    │
│  • spring.datasource.username                               │
│  • spring.datasource.password                               │
│  • spring.jpa.hibernate.ddl-auto                            │
│  • spring.jpa.show-sql                                      │
│  • server.port                                              │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

### Principios de Arquitectura Implementados

#### 1. **Separación de Responsabilidades (SoC)**
- Frontend maneja UI y experiencia de usuario
- Backend maneja lógica de negocio y datos
- Base de datos maneja persistencia

#### 2. **Inyección de Dependencias (DI)**
```java
@Autowired
private ProductoServices productoServices;
```
- Spring gestiona automáticamente las dependencias
- Facilita testing y mantenibilidad

#### 3. **API REST RESTful**
- Uso correcto de verbos HTTP (GET, POST, PUT, DELETE, PATCH)
- URIs descriptivas y consistentes
- Respuestas con códigos de estado apropiados

#### 4. **Single Responsibility Principle (SRP)**
- Cada clase tiene una única responsabilidad
- Controller → maneja HTTP
- Service → lógica de negocio
- Repository → acceso a datos
- Entity → modelo de datos

#### 5. **Open/Closed Principle**
- Uso de interfaces (ProductoServices, CategoriaServices)
- Fácil extensión sin modificar código existente

#### 6. **Dependency Inversion Principle**
- Controllers dependen de abstracciones (interfaces)
- No de implementaciones concretas

---

### Seguridad y CORS

```java
@CrossOrigin(origins = "http://localhost:5173")
```

**Configuración CORS:**
- Permite solicitudes solo desde `localhost:5173` (Frontend)
- Previene ataques CSRF desde otros orígenes
- En producción, debe configurarse con el dominio real

**Mejoras de seguridad recomendadas:**
- Implementar Spring Security
- Añadir JWT para autenticación
- Validación de datos en backend
- Sanitización de inputs
- Manejo de excepciones personalizado

---

### Performance y Optimización

**Frontend:**
- React lazy loading para rutas
- useMemo y useCallback para optimizar renders
- localStorage para cacheo de carrito

**Backend:**
- FetchType.EAGER en relaciones necesarias
- CrudRepository para operaciones optimizadas
- Connection pooling de Hibernate

**Base de Datos:**
- Índices en claves foráneas
- Constraints para integridad referencial

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

**¡Sativamente! 🌿**
