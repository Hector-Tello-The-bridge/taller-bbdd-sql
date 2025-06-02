# Ejemplo Base de datos relacional.
---

## 🧠 Conceptos Clave

| Concepto         | Definición                                                                 |
|------------------|---------------------------------------------------------------------------|
| Base de Datos    | Colección organizada de datos relacionados                                |
| Tabla            | Conjunto de datos organizados en filas y columnas                         |
| Fila (Registro)  | Una entrada individual en la tabla                                        |
| Columna (Campo)  | Un tipo específico de dato dentro de una tabla                           |
| Clave Primaria   | Identificador único de cada fila de una tabla                             |
| Clave Foránea    | Campo que establece relación con la clave primaria de otra tabla          |
| Relación         | Asociación entre dos tablas a través de claves                            |

---


## ✅ Buenas Prácticas

- Usa claves primarias como identificadores únicos
- Evita duplicar información: usa claves foráneas
- Normaliza tus tablas para evitar redundancia

---


## 📘 Caso Práctico: Biblioteca

### 1. Requisitos.
Queremos registrar:
- Libros disponibles
- Autores de cada libro
- Préstamos de libros por parte de los usuarios

---

## 📊 Diseño de Tablas

### Tabla: `Autor`

| id_autor (PK) | nombre       | nacionalidad  |
|---------------|--------------|----------------|
| 1             | Gabriel García Márquez | Colombiana |
| 2             | J.K. Rowling | Británica     |

---

### Tabla: `Libro`

| id_libro (PK) | titulo                      | id_autor (FK) |
|---------------|-----------------------------|---------------|
| 1             | Cien Años de Soledad        | 1             |
| 2             | El Amor en los Tiempos del Cólera | 1       |
| 3             | Harry Potter y la Piedra Filosofal | 2       |

---

### Tabla: `Usuario`

| id_usuario (PK) | nombre       | email               |
|------------------|--------------|---------------------|
| 1                | Ana Pérez    | ana@email.com       |
| 2                | Juan Gómez   | juan@email.com      |

---

### Tabla: `Prestamo`

| id_prestamo (PK) | id_libro (FK) | id_usuario (FK) | fecha_prestamo |
|------------------|---------------|------------------|----------------|
| 1                | 1             | 1                | 2024-05-01     |
| 2                | 3             | 2                | 2024-05-03     |

---

## 🔄 Relaciones entre Tablas

- `Libro` → `Autor`: muchos a uno (muchos libros pueden tener el mismo autor)
- `Prestamo` → `Libro`: muchos a uno (un préstamo corresponde a un libro)
- `Prestamo` → `Usuario`: muchos a uno (un préstamo lo hace un usuario)

---
## 🏗️ 1. Creación de Tablas en SQL

```sql
-- Tabla de autores
CREATE TABLE Autor (
  id_autor INT PRIMARY KEY,
  nombre VARCHAR(100),
  nacionalidad VARCHAR(50)
);

-- Tabla de libros
CREATE TABLE Libro (
  id_libro INT PRIMARY KEY,
  titulo VARCHAR(150),
  id_autor INT,
  FOREIGN KEY (id_autor) REFERENCES Autor(id_autor)
);

-- Tabla de usuarios
CREATE TABLE Usuario (
  id_usuario INT PRIMARY KEY,
  nombre VARCHAR(100),
  email VARCHAR(100)
);

-- Tabla de préstamos
CREATE TABLE Prestamo (
  id_prestamo INT PRIMARY KEY,
  id_libro INT,
  id_usuario INT,
  fecha_prestamo DATE,
  FOREIGN KEY (id_libro) REFERENCES Libro(id_libro),
  FOREIGN KEY (id_usuario) REFERENCES Usuario(id_usuario)
);
```

---

## 📝 2. Inserción de Datos

```sql
-- Autores
INSERT INTO Autor VALUES (1, 'Gabriel García Márquez', 'Colombiana');
INSERT INTO Autor VALUES (2, 'J.K. Rowling', 'Británica');

-- Libros
INSERT INTO Libro VALUES (1, 'Cien Años de Soledad', 1);
INSERT INTO Libro VALUES (2, 'El Amor en los Tiempos del Cólera', 1);
INSERT INTO Libro VALUES (3, 'Harry Potter y la Piedra Filosofal', 2);

-- Usuarios
INSERT INTO Usuario VALUES (1, 'Ana Pérez', 'ana@email.com');
INSERT INTO Usuario VALUES (2, 'Juan Gómez', 'juan@email.com');

-- Préstamos
INSERT INTO Prestamo VALUES (1, 1, 1, '2024-05-01');
INSERT INTO Prestamo VALUES (2, 3, 2, '2024-05-03');
```

## 🧪 Consultas SQL Básicas

### 1. Ver todos los libros y sus autores

```sql
SELECT libro.titulo, autor.nombre
FROM libro
JOIN autor ON libro.id_autor = autor.id_autor;
```

### 2. Ver qué libros ha prestado un usuario

```sql
SELECT usuario.nombre AS usuario, libro.titulo, prestamo.fecha_prestamo
FROM prestamo
JOIN usuario ON prestamo.id_usuario = usuario.id_usuario
JOIN libro ON prestamo.id_libro = libro.id_libro;
```

### 3. Insertar un nuevo libro

```sql
INSERT INTO libro (id_libro, titulo, id_autor)
VALUES (4, 'Harry Potter y la Cámara Secreta', 2);
```



## 🧩 Ejercicio para Practicar

1. Agrega un nuevo autor y un libro suyo.
2. Registra un nuevo préstamo.
3. Haz una consulta para ver todos los préstamos ordenados por fecha.

---

## 🧠 Resumen

| Concepto              | Ejemplo del caso                       |
|------------------------|----------------------------------------|
| Clave primaria         | `id_libro` en la tabla `Libro`         |
| Clave foránea          | `id_autor` en la tabla `Libro`         |
| Relación muchos-a-uno | Muchos libros pueden tener 1 autor      |

---

¿Listo para diseñar tu propia base de datos? 🌱
