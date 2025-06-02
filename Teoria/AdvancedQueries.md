## Cláusula SQL HAVING

Una cláusula `HAVING` especifica que una sentencia `SELECT` de SQL solo debe devolver filas donde los valores agregados cumplan con las condiciones especificadas.
Se añadió al lenguaje SQL porque la palabra clave `WHERE` **no puede usarse con funciones de agregación**.
Recuerda: `WHERE` para condiciones **antes del agrupamiento**, `HAVING` para condiciones **después del agrupamiento**.

**Sintaxis:**

```sql
SELECT nombre_columna, funcion_agregada(nombre_columna)
FROM nombre_tabla
WHERE nombre_columna operador valor
GROUP BY nombre_columna
HAVING funcion_agregada(nombre_columna) operador valor;
```

### Ejemplo de cláusula HAVING

```sql
-- ¿Qué clientes nunca han hecho un pedido?
SELECT  C.CustomerID,
        C.CompanyName,
        COUNT(O.OrderID) [Total Count of Orders]
FROM Customers C
    LEFT JOIN Orders O ON C.CustomerID = O.CustomerID
GROUP BY C.CustomerID, C.CompanyName
HAVING COUNT(O.OrderID) = 0;
```

---

## Operador SQL LIKE y comodines

El operador `LIKE` se utiliza para buscar un patrón específico en una columna.
Se usa junto con caracteres comodín.

**Comodines principales:**

1. `%` → Sustituye cero o más caracteres
2. `_` → Sustituye un solo carácter

### Ejemplos

```sql
-- Clientes de Bern, Berlin y Bergamo
SELECT * FROM Customers
WHERE City LIKE 'ber%';

-- Clientes de Bruxelles, Resende, Buenos Aires, etc.
SELECT * FROM Customers
WHERE City LIKE '%es%';  

-- Clientes con región CA o WA
SELECT * FROM Customers
WHERE Region LIKE '_A';
```

---

## Función SQL ROUND

La función `ROUND()` se usa para redondear un número con la cantidad de decimales especificada.

**Sintaxis:**

```sql
SELECT ROUND(nombre_columna, decimales) FROM nombre_tabla;
```

**Ejemplo:**

```sql
-- Precio total del pedido con OrderID = 10266 y ProductID = 12
SELECT ROUND((UnitPrice * Quantity * (1 - Discount)), 2), *
FROM [Order Details]
WHERE OrderID = 10266;
```

---

## Cláusula SQL SELECT TOP

La cláusula `SELECT TOP` se usa para especificar el número de registros que se desean retornar.

**Sintaxis:**

```sql
SELECT TOP número|porcentaje nombre_columna(s)
FROM nombre_tabla;
```

### Equivalente en MySQL

```sql
SELECT nombre_columna(s)
FROM nombre_tabla
LIMIT número;
```

---

## Función SQL ISNULL

Reemplaza valores `NULL` por un valor de reemplazo especificado.

**Sintaxis:**

```sql
ISNULL ( expresión , valor_reemplazo )  
```

**Ejemplos:**

```sql
SELECT ISNULL(NULL, 'thebridge.tech'); -- Resultado: 'thebridge.tech'
SELECT ISNULL('Kaggle.com', 'thebridge.com'); -- Resultado: 'Kaggle.com'

SELECT ISNULL(NULL, 45); -- Resultado: 45
SELECT ISNULL(12, 45); -- Resultado: 12

SELECT ISNULL(NULL, '2014-05-01'); -- Resultado: '2014-05-01'
SELECT ISNULL('2014-04-30', '2014-05-01'); -- Resultado: '2014-04-30'
```

---

## Variables en SQL

Un objeto que contiene un único valor de un tipo de dato específico.

**Usos:**

* Almacenar datos para su uso posterior
* Contadores
* Entradas/salidas para procedimientos almacenados o funciones

**Declaración:**

```sql
DECLARE @nombre_variable tipo_de_dato;
```

**Ejemplos:**

```sql
DECLARE @i INT;
DECLARE @stringVar VARCHAR(200);
DECLARE @myDate DATETIME;
```

---

## Asignación de valores a variables

### Usando `SET`

```sql
DECLARE @i INT;
SET @i = 10;
SET @stringVar = 'Coding Bootcamp';
```

### Usando `SELECT`

```sql
SELECT @i = 1,
       @stringVar = 'Coding Bootcamp',
       @myDate = '2016-10-24';
```

---

## SET vs SELECT para asignación de variables

* `SET` es estándar ANSI; `SELECT` no.
* `SET` asigna solo una variable a la vez; `SELECT` puede asignar múltiples.
* Con consultas:

  * `SET` lanza error si la consulta devuelve más de un valor
  * `SELECT` toma uno de los valores y no lanza error
* Si no hay valores:

  * `SET` asigna `NULL`
  * `SELECT` no cambia el valor anterior

---

## Tablas temporales

**Tipos:**

* Locales: `#miTabla`
* Globales: `##miTabla`
* Variables de tabla: `@miTabla`

**Ventajas:**

* Espacio de trabajo temporal
* Se pueden alterar tras su creación
* Pueden tener múltiples índices
* Soportan SQL dinámico

### Tabla Variable:

```sql
DECLARE @myStudents TABLE (
    ID INT,
    LastName VARCHAR(50),
    FirstName VARCHAR(50)
);
```

### Tabla Temporal Local:

```sql
CREATE TABLE #myStudents (
    ID INT,
    LastName VARCHAR(50),
    FirstName VARCHAR(50)
);
```

---

## SELECT INTO

Permite insertar los resultados de una consulta en una **nueva tabla temporal**.

**Sintaxis:**

```sql
-- Crear una tabla temporal con los IDs de pedidos y sus ingresos totales
SELECT O.OrderID,
       SUM(ROUND((UnitPrice * Quantity * (1 - Discount)), 2)) AS [Final Price]
INTO #tempFinalPrices
FROM Orders O
    INNER JOIN [Order Details] OD ON O.OrderID = OD.OrderID
GROUP BY O.OrderID;

SELECT * FROM #tempFinalPrices;

DROP TABLE #tempFinalPrices;
```

---

## Operador SQL IN

Permite especificar múltiples valores en una cláusula `WHERE`.

**Sintaxis:**

```sql
SELECT nombre_columna(s)
FROM nombre_tabla
WHERE nombre_columna IN (valor1, valor2, ...);
```

**Ejemplo:**

```sql
-- ¿Cuántos objetos han pedido los clientes de Canadá, UK y USA por año?
SELECT C.CompanyName, C.Country, YEAR(O.OrderDate) AS [Year],
       SUM(OD.Quantity) AS [Total Quantity]
FROM Customers C
    INNER JOIN Orders O ON C.CustomerID = O.CustomerID
    INNER JOIN [Order Details] OD ON O.OrderID = OD.OrderID
WHERE C.Country IN ('UK', 'USA', 'Canada')
GROUP BY C.CompanyName, YEAR(O.OrderDate), C.Country
ORDER BY C.CompanyName, YEAR(O.OrderDate);
```

---

## Subconsultas (Subqueries)

Consultas anidadas dentro de otras.

**Ejemplo simple:**

```sql
-- Nombre de la empresa que hizo el pedido 10290
SELECT CompanyName
FROM Customers
WHERE CustomerID = (SELECT CustomerID
                    FROM Orders
                    WHERE OrderID = 10290);
```

**Ejemplo más complejo:**

```sql
-- Empresas que hicieron pedidos en 1997
SELECT CompanyName
FROM Customers
WHERE CustomerID IN (SELECT CustomerID
                     FROM Orders
                     WHERE OrderDate BETWEEN '1997-01-01' AND '1997-12-31');
```

---

## Vistas (VIEWS)

Una **vista** es una tabla virtual basada en una consulta SQL predefinida y almacenada con un nombre.

**Sintaxis:**

```sql
CREATE VIEW nombre_vista AS
SELECT columna1, columna2...
FROM tabla
WHERE condición;
```

**Ejemplo:**

```sql
CREATE VIEW [Current Product List] AS
SELECT ProductID, ProductName
FROM Products
WHERE Discontinued = 0;

SELECT * FROM [Current Product List];
```

---

## Consejos y Trucos para los ejercicios

* Usa alias para las tablas (ej.: `SELECT C.CompanyName FROM Customers AS C`)
* Usa `DISTINCT` para resultados únicos (útil para verificaciones)
* Usa `BEGIN TRANSACTION ... ROLLBACK` en comandos `INSERT`, `UPDATE`, `DELETE`
* Usa `@@ROWCOUNT` para contar filas afectadas
* Siempre usa cláusulas `WHERE` en `DELETE` y `UPDATE`
* Evita usar `CURSORS` y bucles `WHILE`
* Prefiere variables de tabla a tablas temporales
