
SQL no se aprende por lista… se aprende por **grupos funcionales**:

| **Tipo** | **Para qué sirve** |
| :------: | :----------------: |
|   DDL    |  Crear estructura  |
|   DML    |  Manipular datos   |
|   DQL    |     Consultar      |
|   DCL    |      Permisos      |
|   TCL    |   Transacciones    |

---

# 2. DDL (Crear estructura)

## 🔑 Palabras clave:

`CREATE`, `ALTER`, `DROP`, `TRUNCATE`

## Ejemplo completo:

CREATE TABLE alumnos (  
    id INT PRIMARY KEY,  
    nombre VARCHAR(50),  
    edad INT  
);  
  
ALTER TABLE alumnos ADD correo VARCHAR(100);  
  
TRUNCATE TABLE alumnos;  
  
DROP TABLE alumnos;

---

# ✏️ 3. DML (Manipular datos)

## 🔑 Palabras clave:

`INSERT`, `UPDATE`, `DELETE`

INSERT INTO alumnos (id, nombre, edad)  
VALUES (1, 'Juan', 20);  
  
UPDATE alumnos  
SET edad = 21  
WHERE id = 1;  
  
DELETE FROM alumnos  
WHERE id = 1;

---

# 🔎 4. DQL (Consultas - SELECT)

## 🔑 Palabras clave importantes:

`SELECT`, `FROM`, `WHERE`, `DISTINCT`, `ORDER BY`, `LIMIT`

SELECT DISTINCT nombre  
FROM alumnos  
WHERE edad > 18  
ORDER BY edad DESC  
LIMIT 5;

---

# 🔗 5. JOINs (relaciones)

## 🔑 Palabras clave:

`JOIN`, `INNER`, `LEFT`, `RIGHT`, `ON`

SELECT a.nombre, c.curso  
FROM alumnos a  
INNER JOIN cursos c  
ON a.id = c.alumno_id;

---

# 📊 6. Agrupación

## 🔑 Palabras clave:

`GROUP BY`, `HAVING`, `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`

SELECT edad, COUNT(*) AS total  
FROM alumnos  
GROUP BY edad  
HAVING COUNT(*) > 1;

---

# 🔁 7. Subconsultas

## 🔑 Palabras clave:

`IN`, `EXISTS`, `ANY`, `ALL`

SELECT nombre  
FROM alumnos  
WHERE edad IN (SELECT edad FROM alumnos WHERE edad > 20);

---

# 🧱 8. CTE (WITH)

WITH mayores AS (  
    SELECT * FROM alumnos WHERE edad > 18  
)  
SELECT * FROM mayores;

---

# 🪟 9. Window Functions

## 🔑 Palabras clave:

`OVER`, `PARTITION BY`, `ROW_NUMBER`, `RANK`

SELECT nombre, edad,  
ROW_NUMBER() OVER (ORDER BY edad) AS fila  
FROM alumnos;

---

# 🔐 10. DCL (Permisos)

GRANT SELECT, INSERT ON alumnos TO usuario;  
  
REVOKE INSERT ON alumnos FROM usuario;

---

# 🔄 11. TCL (Transacciones)

## 🔑 Palabras clave:

`BEGIN`, `COMMIT`, `ROLLBACK`

BEGIN;  
  
UPDATE alumnos SET edad = 25 WHERE id = 1;  
  
ROLLBACK;  
-- o  
COMMIT;



---

## Lista de palabras reservadas


```
SELECT, FROM, WHERE, INSERT, UPDATE, DELETE,
CREATE, ALTER, DROP,
JOIN, INNER, LEFT, RIGHT, ON,
GROUP BY, HAVING, ORDER BY,
DISTINCT, LIMIT,
IN, EXISTS, BETWEEN, LIKE,
AND, OR, NOT,
COUNT, SUM, AVG, MIN, MAX,
WITH, OVER, PARTITION BY,
GRANT, REVOKE,
COMMIT, ROLLBACK
```


[[SQL]]


