# Topicos SQLSERVER (T-SQL)

```SQL
IF NOT EXISTS (SELECT name FROM sys.databases WHERE name = N'miniBD')
BEGIN
	CREATE DATABASE miniBD
	COLLATE Latin1_General_100_CI_AS_SC_UTF8;
END
GO

	--select name from sys.databases;

USE miniBD;
GO

DELETE FROM clientes

--Creacion de tablas

IF OBJECT_ID('clientes','u') is NOT NULL DROP TABLE clientes;

CREATE TABLE clientes(
	IdClientes INT not null,
	Nombre NVARCHAR(100),
	Edad INT,
	Ciudad NVARCHAR(100),
	CONSTRAINT pk_clientes
	PRIMARY KEY (idclientes)
);

IF OBJECT_ID('productos','u') is NOT NULL DROP TABLE productos;

CREATE TABLE productos(
	Idproducto INT PRIMARY KEY,
	NombreProductos NVARCHAR(200),
	Categoria NVARCHAR(200),
	Precio DECIMAL (12,2)
)

/*
			INSERCION DE REGISTROS EN LAS TABLAS
*/

INSERT INTO clientes
VALUES
(1,'Ana Torres',25,'Ciudad de Mexico');

INSERT INTO clientes(IdClientes,Nombre,Edad,Ciudad)
VALUES(2,'Luis Perez',32,'Guadalajara');

INSERT INTO clientes(IdClientes,Edad,Nombre,Ciudad)
VALUES(3,29,'Soy la vaca',NULL)

INSERT INTO clientes(IdClientes,Nombre,Edad)
VALUES(4,'Natacha',41)

INSERT INTO clientes(IdClientes,Nombre,Edad,Ciudad)
VALUES  (5,'Sofia Lopez',19,'Chapulhuacan'),
		(6,'Laura Bozo',38,Null),
		(7,'Victor Trujillo',25,'Zacualtiplan');


INSERT INTO clientes (IdClientes, Nombre, Edad, Ciudad)
VALUES
(1, 'Mar�a L�pez', 28, 'Ciudad de M�xico'),
(2, 'Luis P�rez', 32, 'Guadalajara'),
(3, 'Carlos Hern�ndez', 45, 'Monterrey'),
(4, 'Ana Torres', 22, 'Puebla'),
(5, 'Jorge Ram�rez', 38, 'Toluca'),
(6, 'Luc�a Morales', 30, 'Quer�taro'),
(7, 'Pedro Gonz�lez', 41, 'M�rida'),
(8, 'Fernanda Castillo', 27, 'Le�n'),
(9, 'Roberto D�az', 35, 'Tijuana'),
(10, 'Andrea Vargas', 24, 'Canc�n'),
(11, 'H�ctor Jim�nez', 29, 'Chihuahua'),
(12, 'Sof�a Herrera', 33, 'San Luis Potos�'),
(13, 'Ricardo Navarro', 26, 'Cuernavaca'),
(14, 'Gabriela Rojas', 40, 'Durango'),
(15, 'Miguel Salinas', 37, 'Aguascalientes'),
(16, 'Elena Cruz', 31, 'Morelia'),
(17, 'Arturo Mej�a', 23, 'Culiac�n'),
(18, 'Daniela Soto', 36, 'Veracruz'),
(19, 'Francisco Mendoza', 42, 'Zacatecas'),
(20, 'Patricia Ortega', 25, 'Oaxaca');



select * from clientes


GO
CREATE OR ALTER PROCEDURE sp_add_custumer
	@Id INT,
	@Nombre NVARCHAR(100),
	@Edad INT,
	@Ciudad NVARCHAR(100)
AS
BEGIN
	INSERT INTO clientes(IdClientes,Nombre,Edad,Ciudad)
	VALUES(@Id,@Nombre,@Edad,@Ciudad)
END;
GO

EXEC sp_add_custumer 9,'Carlos Ruiz',41,'Monterrey';
EXEC sp_add_custumer 10,'Brene Quintana Manzana',74,'Salte si puedes';

SELECT COUNT(*) AS [NumeroDeClientes] FROM clientes;

-- Mostrar todos los clientes ordenados por edad de menor a mayor
SELECT 
	UPPER (Nombre) AS [Cliente],
	Edad,
	UPPER (Ciudad) 
FROM clientes
ORDER BY Edad DESC;

--Listar lis clientes que viven en Guadalajara

SELECT 
	UPPER (Nombre) AS [Cliente],
	Edad,
	UPPER (Ciudad) 
FROM clientes
WHERE Ciudad = 'GUADALAJARA';

--Listar los clientes con una edad mayor o igual a 30

SELECT 
	UPPER (Nombre) AS [Cliente],
	Edad,
	UPPER (Ciudad) 
FROM clientes
WHERE Edad>=30;

--Listar los clientes cuya ciudad sea cul

SELECT 
	UPPER (Nombre) AS [Cliente],
	Edad,
	UPPER (Ciudad) AS [Ciudad]
FROM clientes
WHERE Ciudad IS NULL;

-- Remplazar en la consulta las ciudades nulas por la palabra DESCONOCIDA 

SELECT 
	UPPER (Nombre) AS [Cliente],
	Edad,
	ISNULL(Ciudad,'Desconocido') AS [Ciudad]
FROM clientes;

--Selecciona los clientes que tengan edad entre 20 y 35 y que vivan en puebla

SELECT 
	UPPER (Nombre) AS [Cliente],
	Edad,
	UPPER (Ciudad) AS [Ciudad]
FROM clientes
WHERE Edad between 20 AND 35
AND
(Ciudad IN ('Ciudad de Mexico','Monterrey'))


/*

======================================== Actualizacion de Datos ====================================================================

*/

select * from clientes


UPDATE clientes
SET Ciudad = 'Xochitlan'
WHERE IdClientes = 5;

UPDATE clientes
SET Ciudad = 'Sin ciudad'
WHERE Ciudad IS NULL;

UPDATE clientes
SET Edad = 30
WHERE IdClientes BETWEEN 3 AND 6;

UPDATE clientes
SET Ciudad = 'Metropoli'
WHERE Ciudad IN ('Ciudad de Mexico','Guadalajara','Monterrey')

UPDATE clientes
SET Nombre = 'Juan Perez',
	Edad   = 27,
	Ciudad = 'Ciudad Gotica'
WHERE IdClientes = 2

UPDATE clientes
SET Nombre = 'CLIENTE PREMIUM'
WHERE Nombre LIKE 'A%'

UPDATE clientes 
SET Nombre = 'Silver Custumer'
WHERE Nombre LIKE '%ER%'

UPDATE clientes
SET Edad = (Edad * 2)
WHERE Edad >= 30 AND Ciudad = 'Metropoli'
;

/*

========================================= ELIMINAR DATOS ===========================================================================

*/

SELECT * FROM clientes

DELETE FROM clientes
WHERE Edad BETWEEN 25 AND 30;

DELETE clientes
WHERE Nombre LIKE '%z'

TRUNCATE TABLE clientes

/*

STORE PROCEDURE Update, Delete, Select

*/

-- Modificar los datos por id
GO
CREATE OR ALTER PROC sp_update_custumer
	@id INT,
	@nombre NVARCHAR(100),
	@edad INT,
	@ciudad NVARCHAR(100)
AS
BEGIN
	UPDATE clientes
	SET 
		Nombre = @nombre,
		Edad   = @edad,
		Ciudad = @ciudad
	WHERE IdClientes = @id
END;
GO


SELECT * FROM clientes

EXEC sp_update_custumer 1,'Benito Camelo',50,'Lima'

EXEC sp_update_custumer 
	@ciudad = 'Ciudad de Mexico',
	@nombre = 'Trofero',
	@edad   = 50,
	@id     = 1


-- Ejercicio completo donde se pueda insertar datos en una tabla principal (encabezado) y en una tabla detalle utilizando un SP

USE miniBD
--TABLA PRINCIPAL
CREATE TABLE ventas(
	Idventa INT IDENTITY (1,1) PRIMARY KEY,
	FechaVenta DATETIME NOT NULL DEFAULT GETDATE(),
	Cliente NVARCHAR(100) NOT NULL,
	Total DECIMAL (10,2)NULL
);

--TABLA DETALLE
CREATE TABLE DetalleVenta(
	IdDetalle INT IDENTITY (1,1) PRIMARY KEY,
	IdVenta  INT NOT NULL,
	Producto  NVARCHAR(100) NOT NULL,
	Cantidad  INT NOT NULL,
	Precio    DECIMAL (10,2) NOT NULL
	CONSTRAINT pk_detalleVenta_venta
	FOREIGN KEY (Idventa)
	REFERENCES Ventas
);


-- Este tipo de tabla servira como estructura para enviar los detalles al SP

CREATE TYPE TipoDetalleVentas AS TABLE(
	Producto NVARCHAR(100),
	Cantidad INT,
	Precio DECIMAL(10,2)
);

-- CREAR EL CP
-- EL SP insertara el encabezado y luego todos los detalles utilizado el tipo de tabla
GO
CREATE OR ALTER PROCEDURE InsertarVentaConDetalle
	@cliente nvarchar(100),
	@detalles TipoDetalleVentas READONLY
AS
BEGIN
	SET NOCOUNT ON;

	DECLARE @Idventa INT;

		BEGIN TRY
		BEGIN TRANSACTION;

		--Insertar en la tabla
		INSERT INTO ventas(Cliente)
		VALUES(@cliente)

		--Obtener el ID recien generado
		SET @Idventa = SCOPE_IDENTITY();

		-- Insertar en la tabla detalles (Tabla detalles)
		INSERT INTO DetalleVenta(IdVenta,Producto,Cantidad,Precio)
		SELECT @Idventa, Producto,Cantidad,Precio
		FROM @detalles

		-- Calcular el total de venta
		UPDATE ventas
		SET Total = (SELECT SUM(Cantidad*Precio) FROM @detalles)
		WHERE Idventa = @Idventa

		COMMIT TRANSACTION 

	END TRY
	BEGIN CATCH
		ROLLBACK TRANSACTION;
		THROW
	END CATCH;
END
GO

-- Ejecutar el SP con datos de prueba

-- Declarar una variable tipo tabla

DECLARE @MisDetalles AS TipoDetalleVentas

-- Insertar productos en el Type Table

INSERT INTO @MisDetalles (Producto,Cantidad,Precio)
VALUES
('Laptop',1,1500),
('Mouse',2,300),
('Teclado',1,500),
('Pantalla',1,4500);

EXEC InsertarVentaConDetalle @Cliente ='Uriel Edgar', @Detalles=@MisDetalles

SELECT * FROM ventas

SELECT * FROM DetalleVenta


-- Funciones Integradas (Built-in Functions)


```

## Funciones Integradas ()
| Función                               | Descripción                                      | Ejemplo                                            |
| ------------------------------------- | ------------------------------------------------ | -------------------------------------------------- |
| `LEN(cadena)`                         | Longitud del texto (sin contar espacios finales) | `LEN('SQL Server ') → 10`                          |
| `LTRIM(cadena)`                       | Elimina espacios a la izquierda                  | `'  Hola' → 'Hola'`                                |
| `RTRIM(cadena)`                       | Elimina espacios a la derecha                    | `'Hola  ' → 'Hola'`                                |
| `LOWER(cadena)`                       | Convierte a minúsculas                           | `'HOLA' → 'hola'`                                  |
| `UPPER(cadena)`                       | Convierte a mayúsculas                           | `'hola' → 'HOLA'`                                  |
| `SUBSTRING(cadena, inicio, longitud)` | Extrae una parte del texto                       | `SUBSTRING('SQLServer', 4, 6) → 'Server'`          |
| `LEFT(cadena, n)`                     | Devuelve los primeros *n* caracteres             | `LEFT('SQLServer', 3) → 'SQL'`                     |
| `RIGHT(cadena, n)`                    | Devuelve los últimos *n* caracteres              | `RIGHT('SQLServer', 6) → 'Server'`                 |
| `CHARINDEX(subcadena, cadena)`        | Devuelve la posición de una subcadena            | `CHARINDEX('S', 'SQL Server') → 1`                 |
| `REPLACE(cadena, buscar, reemplazo)`  | Reemplaza texto                                  | `REPLACE('SQL 2022', '2022', '2025') → 'SQL 2025'` |
| `REVERSE(cadena)`                     | Invierte el texto                                | `REVERSE('SQL') → 'LQS'`                           |
| `CONCAT(val1, val2, ...)`             | Une varios valores en una sola cadena            | `CONCAT('Cliente ', Nombre)`                       |
| `CONCAT_WS(sep, val1, val2, ...)`     | Une valores con un separador                     | `CONCAT_WS('-', 'MX', '001') → 'MX-001'`           |

```SQL

-- Funciones Integradas (Built-in Functions)

-- Funciones de cadena

SELECT 
	Nombre,
	LTRIM(UPPER (Nombre)) AS [Mayusculas],
	LOWER (Nombre) AS [Minusculas],
	LEN   (Nombre) AS [Longitud],
	SUBSTRING(Nombre,1,3) AS [ParteTexto], 
	LTRIM(Nombre)  AS [SinEspaciosIzquierda],
	CONCAT(Nombre, '-' ,Edad) AS [NombreEdad],
	UPPER(TRIM(REPLACE (Ciudad,'Chapulhuacan','Chapu'))) AS [CiudadNormal]
FROM clientes;

INSERT INTO clientes(IdClientes,Nombre,Edad,Ciudad)
VALUES (21,'Luis Lopez',45,'Achichilco')

INSERT INTO clientes(IdClientes,Nombre,Edad,Ciudad)
VALUES (22,' German Galindo',45,'Achichilco2 ')

INSERT INTO clientes(IdClientes,Nombre,Edad,Ciudad)
VALUES (23,' Jael Porfiriio ',19,' Achichilco3 ')


INSERT INTO clientes(IdClientes,Nombre,Edad,Ciudad)
VALUES (24,' Roberto Estrada ',19,' Chapulhuacan ')





-- Crear una tabla a partir de una consulta

SELECT TOP 0
    IdClientes,
	Nombre AS [Nombre Fuente],
	LTRIM(UPPER (Nombre)) AS [Mayusculas],
	LOWER (Nombre) AS [Minusculas],
	LEN   (Nombre) AS [Longitud],
	SUBSTRING(Nombre,1,3) AS [ParteTexto], 
	LTRIM(Nombre)  AS [SinEspaciosIzquierda],
	CONCAT(Nombre, '-' ,Edad) AS [NombreEdad],
	UPPER(TRIM(REPLACE (Ciudad,'Chapulhuacan','Chapu'))) AS [CiudadNormal]
	INTO stage_clientes
FROM clientes;

-- Agrega un constraint a la tabla (PRIMARY KEY)
ALTER TABLE stage_clientes
ADD CONSTRAINT pk_stage_clientes
PRIMARY KEY(idCliente)

SELECT * FROM stage_clientes

-- Vamos a insertar datos a partit de una consulta
INSERT INTO stage_clientes(IdClientes,
	[Nombre Fuente],
	Mayusculas,
	Minusculas,
	Longitud,ParteTexto,
	SinEspaciosIzquierda,
	NombreEdad,CiudadNormal)
SELECT 
    IdClientes,
	Nombre AS [Nombre Fuente],
	LTRIM(UPPER (Nombre)) AS [Mayusculas],
	LOWER (Nombre) AS [Minusculas],
	LEN   (Nombre) AS [Longitud],
	SUBSTRING(Nombre,1,3) AS [ParteTexto], 
	LTRIM(Nombre)  AS [SinEspaciosIzquierda],
	CONCAT(Nombre, '-' ,Edad) AS [NombreEdad],
	UPPER(TRIM(REPLACE (Ciudad,'Chapulhuacan','Chapu'))) AS [CiudadNormal]
FROM clientes;


SELECT * FROM clientes


```

## -- Funciones de Fecha

| Función                               | Descripción                        |
| ------------------------------------- | ---------------------------------- |
| `GETDATE()`                           | Devuelve la fecha y hora actual    |
| `DATEADD(intervalo, cantidad, fecha)` | Suma o resta unidades de tiempo    |
| `DATEDIFF(intervalo, fecha1, fecha2)` | Calcula la diferencia entre fechas |

```SQL
SELECT * FROM VENTAS

-- Funciones de Fecha

USE NORTHWND

GO
SELECT 
	OrderDate, 
	GETDATE(),
	DATEADD(DAY,10,OrderDate) AS [FechaMas10Dias],
	DATEPART(QUARTER,OrderDate) AS [Trimestre],
	DATENAME(MONTH, OrderDate) AS [MesConNombre],
	DATEPART(MONTH, OrderDate) AS [MesConNumero],
	DATENAME(WEEKDAY,OrderDate) AS [NombreDIA],
	DATEDIFF(DAY,OrderDate,GETDATE()) AS [DiasTranscurridos],
	DATEDIFF(YEAR,OrderDate,GETDATE()) AS [A♫osTranscurridos],
	DATEDIFF(YEAR,'2004-06-17',GETDATE()) AS [EdadPEPE]

FROM Orders
```

## Manejo de valores Nulos (NULL)

| Función                                 | Descripción                                 | Ejemplo                                                     |
| --------------------------------------- | ------------------------------------------- | ----------------------------------------------------------- |
| `ISNULL(expresión, valor)`              | Reemplaza `NULL` por un valor definido.     | `SELECT ISNULL(phone, 'Sin teléfono');`                     |
| `COALESCE(expresión1, expresión2, ...)` | Devuelve el primer valor no nulo.           | `SELECT COALESCE(email, secondary_email, 'No disponible');` |
| `NULLIF(expresión1, expresión2)`        | Devuelve `NULL` si los valores son iguales. | `SELECT NULLIF(salary, 0);`                                 |


```SQL

-- Manejo de valores Nulos

USE miniBD

CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    Email NVARCHAR(100),
    SecondaryEmail NVARCHAR(100),
    Phone NVARCHAR(20),
    Salary DECIMAL(10,2),
    Bonus DECIMAL(10,2)
);

INSERT INTO Employees (EmployeeID, FirstName, LastName, Email, SecondaryEmail, Phone, Salary, Bonus)
VALUES (1, 'Ana', 'Lopez', 'ana.lopez@empresa.com',NULL,'555-2345', 12000, 100),
       (2, 'Carlos', 'Ramirez', NULL, 'c.ramirez@empresa.com', NULL, 9500, NULL),
       (3, 'Laura', 'Gomez', NULL, NULL, '555-8900', 0, 500),
       (4, 'Jorge', 'Diaz', 'jorge.diaz@empresa.com', NULL, NULL, 15000, 0);

-- Ejecucion 1 - ISNULL

--Mostrar el nombre completo del empleado junto con su numero de telefono.
--sino tiene telefono, mostrar el texto 'No disponible'

SELECT 
	CONCAT(FirstName,' ',LastName) AS [FullName],
	ISNULL(Phone,'No disponible')
FROM Employees;

-- Ejercicio 2 . Mostrar el nombre del empleado y su correo de contacto
SELECT * FROM Employees

SELECT
	CONCAT(FirstName,' ',LastName) AS [NombreCompleto],
	COALESCE(Email,SecondaryEmail, 'SinCorreo') AS [CorreoContacto] 
FROM Employees


-- Ejecicio 3. NULLIF

--Mostrar el nombre del empleado, su salario y el resultado de NULLIF (Salary,0) para detectar quien tiene salario 0

SELECT 
		CONCAT(FirstName,' ',LastName) AS [NombreCompleto],
		Salary,
		NULLIF(Salary,0) AS [SalarioEvaluable]
FROM Employees

-- Evita error de divicion por cero:

SELECT 
	Bonus,
	(Bonus/NULLIF(Salary,0)) AS [BonusSalary]
FROM Employees

```

## Expresiones Condicionales Case

Permite crear condiciones dentro de una consuta
```SQL 
CASE
	WHEN condicion THEN resultado1
	WHEN condicion THEN resuktado
	ELSE resultado_por_defecto

END
```
