GROW SHOP
Evualacion2


# 🌿 Sativamente - E-commerce de Productos de Cultivo

---

## 🧪 Testing

### Estrategia de Testing

El proyecto implementa una estrategia de testing multinivel para garantizar la calidad del código:

```
┌─────────────────────────────────────────────────────────────┐
│                    PIRÁMIDE DE TESTING                      │
│                                                              │
│                         /\                                   │
│                        /  \      E2E Tests                   │
│                       /    \     (Manual/Cypress)            │
│                      /──────\                                │
│                     /        \   Integration Tests           │
│                    /          \  (Spring Boot Test)          │
│                   /────────────\                             │
│                  /              \ Unit Tests                 │
│                 /                \ (JUnit 5, Jest)           │
│                /──────────────────\                          │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

### Testing Backend (Spring Boot)

#### Configuración Actual

El proyecto incluye **Spring Boot Starter Test** que proporciona:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

**Librerías incluidas:**
- **JUnit 5** - Framework de testing
- **Mockito** - Mocking framework
- **AssertJ** - Assertions fluidas
- **Hamcrest** - Matchers
- **Spring Test** - Utilidades de testing para Spring
- **JSONPath** - Manipulación de JSON

---

#### 1. Unit Tests - Capa de Servicio

**Ubicación:** `src/test/java/.../services/`

**ProductoServicesImplTest.java**
```java
package com.vivitasol.projectbackend.services;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.*;
import static org.mockito.Mockito.*;

import java.util.Arrays;
import java.util.List;
import java.util.Optional;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import com.vivitasol.projectbackend.entities.Categoria;
import com.vivitasol.projectbackend.entities.Producto;
import com.vivitasol.projectbackend.repositories.ProductoRepositories;

@ExtendWith(MockitoExtension.class)
@DisplayName("Tests para ProductoServicesImpl")
class ProductoServicesImplTest {

    @Mock
    private ProductoRepositories productoRepositories;

    @InjectMocks
    private ProductoServicesImpl productoServices;

    private Producto producto;
    private Categoria categoria;

    @BeforeEach
    void setUp() {
        categoria = new Categoria();
        categoria.setId(1L);
        categoria.setNombre("Fumadores");

        producto = new Producto();
        producto.setId(1L);
        producto.setNombre("Bong");
        producto.setDescripcion("Bong de vidrio");
        producto.setPrecio(7000L);
        producto.setStock(10);
        producto.setActivo(true);
        producto.setCategoria(categoria);
    }

    @Test
    @DisplayName("Crear producto exitosamente")
    void testCrearProducto() {
        // Arrange
        when(productoRepositories.save(any(Producto.class))).thenReturn(producto);

        // Act
        Producto resultado = productoServices.crear(producto);

        // Assert
        assertNotNull(resultado);
        assertEquals("Bong", resultado.getNombre());
        assertEquals(7000L, resultado.getPrecio());
        verify(productoRepositories, times(1)).save(producto);
    }

    @Test
    @DisplayName("Obtener producto por ID existente")
    void testObtenerProductoPorId() {
        // Arrange
        when(productoRepositories.findById(1L)).thenReturn(Optional.of(producto));

        // Act
        Producto resultado = productoServices.obtenerId(1L);

        // Assert
        assertNotNull(resultado);
        assertEquals(1L, resultado.getId());
        assertEquals("Bong", resultado.getNombre());
    }

    @Test
    @DisplayName("Lanzar excepción al obtener producto inexistente")
    void testObtenerProductoInexistente() {
        // Arrange
        when(productoRepositories.findById(999L)).thenReturn(Optional.empty());

        // Act & Assert
        RuntimeException exception = assertThrows(
            RuntimeException.class,
            () -> productoServices.obtenerId(999L)
        );
        assertEquals("Producto no encontrado", exception.getMessage());
    }

    @Test
    @DisplayName("Listar todos los productos")
    void testListarProductos() {
        // Arrange
        Producto producto2 = new Producto();
        producto2.setId(2L);
        producto2.setNombre("Máscara");
        
        List<Producto> productos = Arrays.asList(producto, producto2);
        when(productoRepositories.findAll()).thenReturn(productos);

        // Act
        List<Producto> resultado = productoServices.listarTodas();

        // Assert
        assertNotNull(resultado);
        assertEquals(2, resultado.size());
        verify(productoRepositories, times(1)).findAll();
    }

    @Test
    @DisplayName("Actualizar producto existente")
    void testActualizarProducto() {
        // Arrange
        Producto productoActualizado = new Producto();
        productoActualizado.setNombre("Bong Mejorado");
        productoActualizado.setDescripcion("Nueva descripción");
        productoActualizado.setPrecio(9000L);
        productoActualizado.setStock(15);
        productoActualizado.setActivo(true);
        productoActualizado.setCategoria(categoria);

        when(productoRepositories.findById(1L)).thenReturn(Optional.of(producto));
        when(productoRepositories.save(any(Producto.class))).thenReturn(productoActualizado);

        // Act
        Producto resultado = productoServices.actualizar(1L, productoActualizado);

        // Assert
        assertNotNull(resultado);
        assertEquals("Bong Mejorado", resultado.getNombre());
        assertEquals(9000L, resultado.getPrecio());
    }

    @Test
    @DisplayName("Desactivar producto")
    void testDesactivarProducto() {
        // Arrange
        when(productoRepositories.findById(1L)).thenReturn(Optional.of(producto));
        when(productoRepositories.save(any(Producto.class))).thenAnswer(i -> i.getArguments()[0]);

        // Act
        Producto resultado = productoServices.desactivar(1L);

        // Assert
        assertNotNull(resultado);
        assertFalse(resultado.getActivo());
        verify(productoRepositories, times(1)).save(producto);
    }

    @Test
    @DisplayName("Eliminar producto existente")
    void testEliminarProducto() {
        // Arrange
        when(productoRepositories.existsById(1L)).thenReturn(true);
        doNothing().when(productoRepositories).deleteById(1L);

        // Act
        productoServices.eliminar(1L);

        // Assert
        verify(productoRepositories, times(1)).existsById(1L);
        verify(productoRepositories, times(1)).deleteById(1L);
    }

    @Test
    @DisplayName("Lanzar excepción al eliminar producto inexistente")
    void testEliminarProductoInexistente() {
        // Arrange
        when(productoRepositories.existsById(999L)).thenReturn(false);

        // Act & Assert
        RuntimeException exception = assertThrows(
            RuntimeException.class,
            () -> productoServices.eliminar(999L)
        );
        assertEquals("Producto no encontrado", exception.getMessage());
    }
}
```

**Ejecutar tests unitarios:**
```bash
./mvnw test
```

---

#### 2. Integration Tests - REST Controllers

**Ubicación:** `src/test/java/.../controllers/`

**ProductoRestControllersIntegrationTest.java**
```java
package com.vivitasol.projectbackend.controllers;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.hamcrest.Matchers.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.transaction.annotation.Transactional;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.vivitasol.projectbackend.entities.Categoria;
import com.vivitasol.projectbackend.entities.Producto;
import com.vivitasol.projectbackend.repositories.CategoriaRepositories;
import com.vivitasol.projectbackend.repositories.ProductoRepositories;

@SpringBootTest
@AutoConfigureMockMvc
@Transactional
@ActiveProfiles("test")
@DisplayName("Integration Tests para ProductoRestControllers")
class ProductoRestControllersIntegrationTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ProductoRepositories productoRepositories;

    @Autowired
    private CategoriaRepositories categoriaRepositories;

    @Autowired
    private ObjectMapper objectMapper;

    private Categoria categoria;
    private Producto producto;

    @BeforeEach
    void setUp() {
        productoRepositories.deleteAll();
        categoriaRepositories.deleteAll();

        categoria = new Categoria();
        categoria.setNombre("Fumadores");
        categoria = categoriaRepositories.save(categoria);

        producto = new Producto();
        producto.setNombre("Bong");
        producto.setDescripcion("Bong de vidrio");
        producto.setPrecio(7000L);
        producto.setStock(10);
        producto.setActivo(true);
        producto.setCategoria(categoria);
    }

    @Test
    @DisplayName("GET /api/productos - Listar todos los productos")
    void testListarProductos() throws Exception {
        // Arrange
        productoRepositories.save(producto);

        // Act & Assert
        mockMvc.perform(get("/api/productos"))
                .andExpect(status().isOk())
                .andExpect(content().contentType(MediaType.APPLICATION_JSON))
                .andExpect(jsonPath("$", hasSize(greaterThanOrEqualTo(1))))
                .andExpect(jsonPath("$[0].nombre", is("Bong")))
                .andExpect(jsonPath("$[0].precio", is(7000)));
    }

    @Test
    @DisplayName("GET /api/productos/{id} - Obtener producto por ID")
    void testObtenerProductoPorId() throws Exception {
        // Arrange
        Producto savedProducto = productoRepositories.save(producto);

        // Act & Assert
        mockMvc.perform(get("/api/productos/{id}", savedProducto.getId()))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.nombre", is("Bong")))
                .andExpect(jsonPath("$.descripcion", is("Bong de vidrio")))
                .andExpect(jsonPath("$.precio", is(7000)))
                .andExpect(jsonPath("$.activo", is(true)));
    }

    @Test
    @DisplayName("POST /api/productos - Crear nuevo producto")
    void testCrearProducto() throws Exception {
        // Arrange
        String productoJson = objectMapper.writeValueAsString(producto);

        // Act & Assert
        mockMvc.perform(post("/api/productos")
                .contentType(MediaType.APPLICATION_JSON)
                .content(productoJson))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.nombre", is("Bong")))
                .andExpect(jsonPath("$.precio", is(7000)));
    }

    @Test
    @DisplayName("PUT /api/productos/{id} - Actualizar producto")
    void testActualizarProducto() throws Exception {
        // Arrange
        Producto savedProducto = productoRepositories.save(producto);
        savedProducto.setNombre("Bong Premium");
        savedProducto.setPrecio(9000L);
        
        String productoJson = objectMapper.writeValueAsString(savedProducto);

        // Act & Assert
        mockMvc.perform(put("/api/productos/{id}", savedProducto.getId())
                .contentType(MediaType.APPLICATION_JSON)
                .content(productoJson))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.nombre", is("Bong Premium")))
                .andExpect(jsonPath("$.precio", is(9000)));
    }

    @Test
    @DisplayName("DELETE /api/productos/{id} - Eliminar producto")
    void testEliminarProducto() throws Exception {
        // Arrange
        Producto savedProducto = productoRepositories.save(producto);

        // Act & Assert
        mockMvc.perform(delete("/api/productos/{id}", savedProducto.getId()))
                .andExpect(status().isNoContent());

        // Verificar que fue eliminado
        mockMvc.perform(get("/api/productos/{id}", savedProducto.getId()))
                .andExpect(status().is4xxClientError());
    }

    @Test
    @DisplayName("PATCH /api/productos/{id}/desactivar - Desactivar producto")
    void testDesactivarProducto() throws Exception {
        // Arrange
        Producto savedProducto = productoRepositories.save(producto);

        // Act & Assert
        mockMvc.perform(patch("/api/productos/{id}/desactivar", savedProducto.getId()))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.activo", is(false)));
    }

    @Test
    @DisplayName("GET /api/productos/{id} - Producto no encontrado")
    void testProductoNoEncontrado() throws Exception {
        // Act & Assert
        mockMvc.perform(get("/api/productos/999"))
                .andExpect(status().is4xxClientError());
    }
}
```

**Ejecutar integration tests:**
```bash
./mvnw integration-test
```

---

#### 3. Configuración de Perfil de Test

**application-test.properties** (crear en `src/test/resources/`):
```properties
# Base de datos en memoria H2 para tests
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# JPA/Hibernate
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true

# Desactivar logs innecesarios
logging.level.org.springframework=WARN
logging.level.org.hibernate=WARN
```

**Agregar dependencia H2 al pom.xml:**
```xml
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>test</scope>
</dependency>
```

---

### Testing Frontend (React)

#### Configuración con Vitest y React Testing Library

**Instalar dependencias:**
```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom 
npm install -D @testing-library/user-event jsdom
```

**vite.config.js** - Agregar configuración de test:
```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/test/setup.js',
  },
})
```

**src/test/setup.js:**
```javascript
import { expect, afterEach } from 'vitest';
import { cleanup } from '@testing-library/react';
import * as matchers from '@testing-library/jest-dom/matchers';

expect.extend(matchers);

afterEach(() => {
  cleanup();
});
```

---

#### 1. Unit Tests - Componentes

**src/components/__tests__/ProductCard.test.jsx:**
```javascript
import { describe, it, expect } from 'vitest';
import { render, screen } from '@testing-library/react';
import { BrowserRouter } from 'react-router-dom';
import ProductCard from '../ProductCard';

describe('ProductCard', () => {
  const mockProducto = {
    id: 1,
    nombre: 'Bong',
    descripcion: 'Bong de vidrio',
    precio: 7000,
    activo: true
  };

  it('renderiza correctamente el producto', () => {
    render(
      <BrowserRouter>
        <ProductCard producto={mockProducto} />
      </BrowserRouter>
    );

    expect(screen.getByText('Bong')).toBeInTheDocument();
    expect(screen.getByText('Bong de vidrio')).toBeInTheDocument();
    expect(screen.getByText('$7.000')).toBeInTheDocument();
  });

  it('muestra badge de disponible cuando activo es true', () => {
    render(
      <BrowserRouter>
        <ProductCard producto={mockProducto} />
      </BrowserRouter>
    );

    expect(screen.getByText('Disponible')).toBeInTheDocument();
  });

  it('muestra badge de no disponible cuando activo es false', () => {
    const productoInactivo = { ...mockProducto, activo: false };
    
    render(
      <BrowserRouter>
        <ProductCard producto={productoInactivo} />
      </BrowserRouter>
    );

    expect(screen.getByText('No disponible')).toBeInTheDocument();
  });

  it('tiene enlace correcto al detalle del producto', () => {
    render(
      <BrowserRouter>
        <ProductCard producto={mockProducto} />
      </BrowserRouter>
    );

    const link = screen.getByText('Ver detalle').closest('a');
    expect(link).toHaveAttribute('href', '/productos/1');
  });
});
```

---

#### 2. Integration Tests - API Service

**src/api/__tests__/apiService.test.js:**
```javascript
import { describe, it, expect, beforeEach, vi } from 'vitest';
import { apiService } from '../apiService';

describe('apiService', () => {
  beforeEach(() => {
    global.fetch = vi.fn();
  });

  describe('getProductos', () => {
    it('obtiene productos exitosamente', async () => {
      const mockProductos = [
        { id: 1, nombre: 'Bong', precio: 7000 },
        { id: 2, nombre: 'Máscara', precio: 5000 }
      ];

      global.fetch.mockResolvedValueOnce({
        ok: true,
        json: async () => mockProductos
      });

      const result = await apiService.getProductos();

      expect(result).toEqual(mockProductos);
      expect(fetch).toHaveBeenCalledWith('http://localhost:8080/api/productos');
    });

    it('maneja errores correctamente', async () => {
      global.fetch.mockResolvedValueOnce({
        ok: false,
        json: async () => ({ error: 'Error del servidor' })
      });

      await expect(apiService.getProductos()).rejects.toThrow();
    });
  });

  describe('crearProducto', () => {
    it('crea un producto exitosamente', async () => {
      const nuevoProducto = {
        nombre: 'Bong',
        descripcion: 'Bong de vidrio',
        precio: 7000,
        stock: 10,
        categoriaId: 7,
        activo: true
      };

      const mockResponse = { id: 1, ...nuevoProducto };

      global.fetch.mockResolvedValueOnce({
        ok: true,
        json: async () => mockResponse
      });

      const result = await apiService.crearProducto(nuevoProducto);

      expect(result).toEqual(mockResponse);
      expect(fetch).toHaveBeenCalledWith(
        'http://localhost:8080/api/productos',
        expect.objectContaining({
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
            'Accept': 'application/json'
          }
        })
      );
    });
  });
});
```

---

#### 3. E2E Tests - Páginas completas

**src/pages/__tests__/Productos.test.jsx:**
```javascript
import { describe, it, expect, beforeEach, vi } from 'vitest';
import { render, screen, waitFor } from '@testing-library/react';
import { BrowserRouter } from 'react-router-dom';
import userEvent from '@testing-library/user-event';
import Productos from '../Productos';
import * as apiService from '../../api/apiService';

vi.mock('../../api/apiService');

describe('Productos Page', () => {
  const mockProductos = [
    { id: 1, nombre: 'Bong', precio: 7000, activo: true, categoriaId: 7 },
    { id: 2, nombre: 'Máscara', precio: 5000, activo: true, categoriaId: 7 }
  ];

  const mockCategorias = [
    { id: 7, nombre: 'Fumadores' },
    { id: 8, nombre: 'Cultivo' }
  ];

  beforeEach(() => {
    apiService.getProductos.mockResolvedValue(mockProductos);
    apiSer# 🌿 Sativamente - E-commerce de Productos de Cultivo

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
11. [Testing](#-testing)
12. [Funcionalidades](#-funcionalidades)

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
