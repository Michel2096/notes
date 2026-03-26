
# 1. ¿Qué es una _query_?

Una **query** es simplemente una consulta a la base de datos.

Ejemplo en PostgreSQL:

```
SELECT nombre, email FROM usuarios WHERE activo = true;
```

# 2. ¿Qué es una _query reutilizable_?

Es una consulta que:  
 **no quieres escribir cada vez**

Entonces la guardas para usarla cuando quieras.


---

# 3. ¿Qué es una _vista (VIEW)_?

👉 Una **vista** es como una “tabla virtual”

NO guarda datos reales  
SOLO guarda la consulta

```
CREATE VIEW usuarios_activos AS
SELECT nombre, email
FROM usuarios
WHERE activo = true;
```

## ¿Cómo se usa?

Luego haces:

```
SELECT * FROM usuarios_activos;
```

# 5. ¿Y en MongoDB?

También existen vistas 👇
```
db.createView(  
  "usuarios_activos",  
  "usuarios",  
  [  
    { $match: { activo: true } }  
  ]  
  }
```

## Diferencia clave Vista/Funciones

| **Concepto** |               **Qué hace**                |
| :----------: | :---------------------------------------: |
|    Vista     |            Guarda una consulta            |
|   Función    | Hace lógica (cálculos, condiciones, etc.) |

👉 **Query** = consulta  
👉 **Query reutilizable** = consulta que guardas  
👉 **Vista (view)** = consulta guardada como “tabla virtual”

[[SQL]]