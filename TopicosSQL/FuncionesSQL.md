
# Actividad de Evaluación: Funciones SQL

**Materia:** Bases de Datos (SQL Server)  
**Alumno:** José Oswaldo Ayala Jiménez  
**Fecha:** 9 de octubre de 2025

**Matricula** 22300082


---

## 1. Funciones de Cadenas

### 1.1 Teoría 
Las funciones de cadena permiten transformar y analizar texto: cambiar mayúsculas/minúsculas, extraer partes, buscar posiciones, reemplazar, eliminar espacios o unir textos.

### 1.2 Sintaxis típica
```sql
-- Mayúsculas / minúsculas
SELECT UPPER(cadena), LOWER(cadena);

-- Longitud y recortes
SELECT LEN(cadena), LTRIM(cadena), RTRIM(cadena), TRIM(cadena);

-- Extracción y posiciones
SELECT LEFT(cadena, n), RIGHT(cadena, n), SUBSTRING(cadena, inicio, largo), CHARINDEX('buscado', cadena);

-- Concatenación y reemplazo
SELECT cadena1 + cadena2, CONCAT(cadena1, cadena2, ...), REPLACE(cadena, 'viejo', 'nuevo');
```

### 1.3 Ejemplos y resultados

#### Ejemplo A: Formateo simple de nombre y usuario
```sql
SELECT 
  UPPER('Ana Torres')   AS Nombre_Mayus,
  LOWER('ADMIN@EMPRESA.COM') AS Correo_Minus,
  LEN(' Ana ')          AS Longitud_Incluyendo_Espacios,
  TRIM(' Ana ')         AS Sin_Espacios_Extremos;
```
**Resultado:**

| Nombre_Mayus | Correo_Minus         | Longitud_Incluyendo_Espacios | Sin_Espacios_Extremos |
|---|---|---:|---|
| ANA TORRES | admin@empresa.com | 5 | Ana |

#### Ejemplo B: Extracción y búsqueda
```sql
SELECT
  LEFT('NORTE-ROUTER-01', 5)              AS Prefijo,
  RIGHT('NORTE-ROUTER-01', 2)             AS Sufijo,
  SUBSTRING('NORTE-ROUTER-01', 7, 6)      AS Tipo,
  CHARINDEX('-', 'NORTE-ROUTER-01')       AS PosPrimerGuion;
```
**Resultado:**

| Prefijo | Sufijo | Tipo   | PosPrimerGuion |
|---|---|---|---:|
| NORTE | 01 | ROUTER | 6 |

#### Ejemplo C: Reemplazo y concatenación segura
```sql
SELECT
  REPLACE('192.168.0.10', '.', '-')             AS IP_Formato,
  CONCAT('srv-', 'web', '-', '01')              AS Hostname,
  'srv-' + 'db' + '-02'                         AS Hostname_Alt;
```
**Resultado:**

| IP_Formato    | Hostname     | Hostname_Alt |
|---|---|---|
| 192-168-0-10 | srv-web-01 | srv-db-02 |

---

## 2. Funciones de Fechas

### 2.1 Teoría 
Las funciones de fecha obtienen la fecha/hora actual, extraen componentes (año, mes, día), suman o restan periodos y formatean fechas.

### 2.2 Sintaxis típica
```sql
SELECT GETDATE() AS FechaHoraActual, SYSDATETIME() AS FechaHoraAltaPrec;

SELECT YEAR(fecha), MONTH(fecha), DAY(fecha);

SELECT DATEADD(DAY, 7, fecha), DATEDIFF(DAY, fecha_ini, fecha_fin);

SELECT EOMONTH(fecha) AS UltimoDiaMes, FORMAT(fecha, 'yyyy-MM') AS Formateada;
```

### 2.3 Ejemplos y resultados

> Nota: Los valores actuales varían por día. Se muestran resultados de ejemplo.

#### Ejemplo A: Componentes de fecha
```sql
DECLARE @f DATE = '2025-10-09';

SELECT 
  @f                           AS Fecha_Base,
  YEAR(@f)                     AS Anio,
  MONTH(@f)                    AS Mes,
  DAY(@f)                      AS Dia,
  EOMONTH(@f)                  AS Ultimo_Dia_Mes;
```
**Resultado:**

| Fecha_Base | Anio | Mes | Dia | Ultimo_Dia_Mes |
|---|---:|---:|---:|---|
| 2025-10-09 | 2025 | 10 | 9 | 2025-10-31 |

#### Ejemplo B: Sumas, diferencias y formato
```sql
DECLARE @ini DATE = '2025-10-01';
DECLARE @fin DATE = '2025-10-09';

SELECT 
  @ini AS Inicio,
  @fin AS Fin,
  DATEDIFF(DAY, @ini, @fin)                AS Dias_Entre,
  DATEADD(DAY, 7, @ini)                    AS Inicio_Mas_7,
  FORMAT(@fin, 'yyyy/MM/dd')               AS Fin_Formateado;
```
**Resultado:**

| Inicio     | Fin        | Dias_Entre | Inicio_Mas_7 | Fin_Formateado |
|---|---|---:|---|---|
| 2025-10-01 | 2025-10-09 | 8 | 2025-10-08 | 2025/10/09 |

---

## 3. Control de Valores Nulos

### 3.1 Teoría 
SQL Server usa `NULL` para indicar valor desconocido o no aplicable. Para controlarlo se usan funciones que sustituyen, comparan o detectan nulos.

### 3.2 Sintaxis típica
```sql
SELECT ISNULL(col, 'Sin dato');

SELECT COALESCE(col1, col2, 'Defecto');

SELECT NULLIF(expr1, expr2);  -- Devuelve NULL si expr1 = expr2, si no, expr1
```

### 3.3 Ejemplos y resultados

#### Ejemplo A: ISNULL y COALESCE
```sql
SELECT 
  ISNULL(NULL, 'Desconocido')                          AS Valor_ISNULL,
  COALESCE(NULL, NULL, 'Primero_NoNulo')               AS Valor_COALESCE;
```
**Resultado:**

| Valor_ISNULL | Valor_COALESCE |
|---|---|
| Desconocido | Primero_NoNulo |

#### Ejemplo B: NULLIF para evitar división por cero
```sql
DECLARE @numerador INT = 100, @den INT = 0;

SELECT 
  @numerador / NULLIF(@den, 0) AS Resultado_Seguro; -- Devuelve NULL en vez de error
```
**Resultado:**

| Resultado_Seguro |
|---|
| NULL |

---

## 4. Uso de CASE

### 4.1 Teoría 
`CASE` permite crear columnas calculadas con lógica condicional, similar a un IF sencillo dentro del `SELECT`, `UPDATE` o `ORDER BY`.

### 4.2 Sintaxis típica
```sql
SELECT 
  CASE 
    WHEN condicion1 THEN valor1
    WHEN condicion2 THEN valor2
    ELSE valor_por_defecto
  END AS Columna;
```

### 4.3 Ejemplos y resultados

#### Ejemplo A: Clasificación por edad
```sql
-- Tabla de ejemplo en memoria
WITH Datos(Cliente, Edad) AS (
  SELECT 'Ana', 19 UNION ALL
  SELECT 'Luis', 30 UNION ALL
  SELECT 'María', 45
)
SELECT 
  Cliente, Edad,
  CASE 
    WHEN Edad < 21 THEN 'Joven'
    WHEN Edad BETWEEN 21 AND 40 THEN 'Adulto'
    ELSE 'Mayor'
  END AS Grupo_Edad;
```
**Resultado:**

| Cliente | Edad | Grupo_Edad |
|---|---:|---|
| Ana | 19 | Joven |
| Luis | 30 | Adulto |
| María | 45 | Mayor |

#### Ejemplo B: Estado de inventario simple
```sql
WITH Inv(Producto, Unidades) AS (
  SELECT 'Teclado', 0 UNION ALL
  SELECT 'Mouse', 5 UNION ALL
  SELECT 'Monitor', 15
)
SELECT 
  Producto, Unidades,
  CASE 
    WHEN Unidades = 0 THEN 'Sin stock'
    WHEN Unidades < 10 THEN 'Bajo'
    ELSE 'OK'
  END AS Estado;
```
**Resultado:**

| Producto | Unidades | Estado    |
|---|---:|---|
| Teclado | 0  | Sin stock |
| Mouse   | 5  | Bajo      |
| Monitor | 15 | OK        |

---

## 5. Uso de MERGE

### 5.1 Teoría 
`MERGE` permite combinar operaciones de **INSERT**, **UPDATE** y **DELETE** en una sola instrucción para sincronizar una tabla destino con una fuente. Es útil para “upserts”.

### 5.2 Sintaxis base
```sql
MERGE dbo.Destino AS D
USING dbo.Fuente  AS F
ON D.Clave = F.Clave
WHEN MATCHED THEN 
  UPDATE SET D.Col = F.Col
WHEN NOT MATCHED BY TARGET THEN
  INSERT (Clave, Col) VALUES (F.Clave, F.Col)
WHEN NOT MATCHED BY SOURCE THEN
  DELETE;
```

### 5.3 Ejemplo sencillo y resultado

#### Preparación de datos
```sql
-- Tablas de práctica
IF OBJECT_ID('dbo.Destino','U') IS NOT NULL DROP TABLE dbo.Destino;
IF OBJECT_ID('dbo.Fuente','U')  IS NOT NULL DROP TABLE dbo.Fuente;

CREATE TABLE dbo.Destino (Id INT PRIMARY KEY, Nombre NVARCHAR(50));
CREATE TABLE dbo.Fuente  (Id INT PRIMARY KEY, Nombre NVARCHAR(50));

INSERT INTO dbo.Destino VALUES (1,'Ana'), (2,'Luis');
INSERT INTO dbo.Fuente  VALUES (2,'Luis Actualizado'), (3,'María');
```

#### MERGE
```sql
MERGE dbo.Destino AS D
USING dbo.Fuente  AS F
ON D.Id = F.Id
WHEN MATCHED THEN 
  UPDATE SET D.Nombre = F.Nombre
WHEN NOT MATCHED BY TARGET THEN
  INSERT (Id, Nombre) VALUES (F.Id, F.Nombre)
WHEN NOT MATCHED BY SOURCE THEN
  DELETE;  
```

#### Consulta final
```sql
SELECT * FROM dbo.Destino ORDER BY Id;
```
**Resultado esperado:**

| Id | Nombre          |
|---:|---|
| 2  | Luis Actualizado |
| 3  | María            |

> Explicación:  
> - El Id=1 de Destino no existe en Fuente ⇒ se elimina.  
> - El Id=2 coincide ⇒ se actualiza el nombre.  
> - El Id=3 existe solo en Fuente ⇒ se inserta.

---

## 6. Notas de ejecución y compatibilidad
1. Todos los ejemplos usan sintaxis estándar de SQL Server 2019+ y funcionan en SSMS 20.  
2. Para evitar usar tus tablas reales, se emplean constantes, CTEs y tablas de práctica.  
3. Si se desea, los ejemplos pueden adaptarse a tablas reales como `clientes` o `productos`.  
4. `FORMAT()` está disponible; en escenarios de alto rendimiento se prefieren funciones clásicas y conversión explícita.

---

## 7. Referencias rápidas
- Documentación oficial de SQL Server (Transact-SQL): funciones de cadena, fecha y CASE.  
- Mejores prácticas: usar `CONCAT` sobre `+` cuando hay posibilidad de `NULL`; controlar `NULL` con `ISNULL` o `COALESCE`; probar `MERGE` primero en un entorno de práctica.

