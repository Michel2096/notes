
**SQL (Structured Query Language)** es el lenguaje que se usa para:

- Crear bases de datos y tablas
- Insertar, modificar y eliminar datos
- Consultar información
- Controlar accesos

## 1. DDL (Data Definition Language)

Sirve para **definir la estructura** de la base de datos.

### Comandos principales:

- `CREATE` → crear tablas o BD
- `ALTER` → modificar tablas
- `DROP` → eliminar tablas
- `TRUNCATE` → borrar todos los datos

```
CREATE TABLE alumnos (
    id INT PRIMARY KEY,
    nombre VARCHAR(50),
    edad INT
);
```

---

## 2. DML (Data Manipulation Language)

Sirve para **manipular datos**.

### Comandos principales:

- `INSERT` → insertar datos
- `UPDATE` → actualizar
- `DELETE` → eliminar
- `SELECT` → consultar

```
INSERT INTO alumnos VALUES (1, 'Juan', 20);

UPDATE alumnos SET edad = 21 WHERE id = 1;

DELETE FROM alumnos WHERE id = 1;

SELECT * FROM alumnos;
```

---
## 3. DCL (Data Control Language)

Sirve para **controlar permisos**.

### Comandos:

- `GRANT` → dar permisos
- `REVOKE` → quitar permisos

```
GRANT SELECT ON alumnos TO usuario;
```

---

# SELECT (lo más importante de SQL)

Es el comando más usado.

## Básico:

```
SELECT nombre, edad FROM alumnos;
```

## Con condición:

```
SELECT * FROM alumnos WHERE edad > 18;
```

## Operadores:

- `=`, `>`, `<`, `>=`, `<=`
- `AND`, `OR`, `NOT`
- `LIKE` → búsqueda con patrones

```
SELECT * FROM alumnos WHERE nombre LIKE 'J%';
```

----

# GROUP BY y HAVING

Sirve para agrupar datos.

```
SELECT edad, COUNT(*)   
FROM alumnos  
GROUP BY edad;
```

👉 `HAVING` filtra grupos:

```
SELECT edad, COUNT(*)   
FROM alumnos  
GROUP BY edad  
HAVING COUNT(*) > 1;
```

---

# JOINs (MUY IMPORTANTE)

Se usan para unir tablas.

## Tipos:

### INNER JOIN

Solo lo que coincide

```
SELECT alumnos.nombre, cursos.nombre  
FROM alumnos  
INNER JOIN cursos ON alumnos.id = cursos.alumno_id;

```
### LEFT JOIN

Todo de la izquierda

```
SELECT *  
FROM alumnos  
LEFT JOIN cursos ON alumnos.id = cursos.alumno_id;
```


---

# Subqueries (subconsultas)

Consulta dentro de otra consulta:

```
SELECT nombre 
FROM alumnos
WHERE edad > (SELECT AVG(edad) FROM alumnos);
```


---

# CTE (Common Table Expressions)

Hace consultas más limpias:

```
WITH mayores AS (
    SELECT * FROM alumnos WHERE edad > 18
)
SELECT * FROM mayores;
```


---

## Palabras reservadas


[[Comandos sql]]

[[Data Base/Beginnings|Beginnings]]