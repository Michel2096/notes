
**JSON (JavaScript Object Notation)** es un formato para almacenar y transportar datos.


```
{
  "nombre": "Juan",
  "edad": 20,
  "activo": true
}
```


En bases de datos, **JSON es un tipo de dato** que permite guardar información **semi-estructurada** dentro de una tabla.

 ==En lugar de tener muchas columnas, puedes guardar datos complejos en una sola.==

# Cómo consultar JSON en SQL

Depende del motor (MySQL, PostgreSQL), pero ejemplo general:

```
SELECT datos->>'nombre' AS nombre
FROM alumnos;
```

## Ejemplos

```
INSERT INTO alumnos VALUES (
    2,
    '{
        "nombre": "Ana",
        "edad": 22,
        "cursos": ["SQL", "Python"]
    }'
);
```

---
## Estructura 

## 1. Objeto (Object)

 Es la estructura principal  
👉 Se define con `{ }`

```
{
  "nombre": "Juan",
  "edad": 20
}
```

## 2. Par clave-valor (key-value)

Dentro del objeto:

```
{
  "nombre": "Juan"
}
```

- `"nombre"` → **clave (key)**
- `"Juan"` → **valor (value)**

## 3. Tipos de valores

Un valor puede ser:

{  
  "texto": "Hola",  
  "numero": 10,  
  "booleano": true,  
  "nulo": null  
}

---

## 🔹 4. Arreglos (Array)

👉 Se definen con `[ ]`

```
{  
  "cursos": ["SQL", "Python", "AWS"]  
}
```

✔ Esto es un **array (lista)**

---

## 🔹 5. Objetos dentro de objetos

{  
  "alumno": {  
    "nombre": "Juan",  
    "edad": 20  
  }  
}

👉 Aquí:

- `"alumno"` → clave
- `{...}` → valor que es **otro objeto**

---

## 🔹 6. Array de objetos (muy común)

```
{  
  "alumnos": [  
    {  
      "nombre": "Juan",  
      "edad": 20  
    },  
    {  
      "nombre": "Ana",  
      "edad": 22  
    }  
  ]  
}
```

👉 Esto es clave para bases de datos y APIs

---

# 🧠 Cómo identificar cada cosa (tu duda principal)

## 📌 Regla rápida:

- `{ }` → **Objeto**
- `[ ]` → **Array (lista)**
- `"clave": valor` → **Propiedad**
Siempre van así:  
**"clave": valor**


---

## Ejemplo completo explicado

```
{
  "escuela": "UTP",
  "activo": true,
  "direccion": {
    "ciudad": "CDMX",
    "cp": 12345
  },
  "alumnos": [
    {
      "nombre": "Juan",
      "edad": 20
    },
    {
      "nombre": "Ana",
      "edad": 22
    }
  ]
}
```

- `{}` → objeto principal
- `"escuela"` → clave
- `"UTP"` → valor
- `"direccion"` → objeto anidado
- `"alumnos"` → array
- `[ {...}, {...} ]` → lista de objetos

----

## Resumen 

|     **Elemento**     |  **Símbolo**  | **Nombre** |
| :--------------: | :-------: | :--------: |
|       `{}`       |  Objeto   |   Object   |
|       `[]`       |   Lista   |   Array    |
| `"clave": valor` | Propiedad | Key-Value  |


[[Data Base/Beginnings|Beginnings]]