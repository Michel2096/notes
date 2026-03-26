
Un índice es como el índice de un libro 📚

 Sirve para buscar más rápido

### 🔹 Sin índice:

- PostgreSQL revisa TODA la tabla 

### 🔹 Con índice:

- Va directo al dato 


```
CREATE INDEX idx_email ON usuarios(email);
```


### ¿Qué pasa?

Cuando haces:

```
SELECT * FROM usuarios WHERE email = 'test@gmail.com';
```

 PostgreSQL usa el índice automáticamente


# CUÁNDO USAR CADA COSA

### ✅ Funciones en BD:

- Cálculos complejos
- Reportes
- lógica repetitiva

---

### ✅ Índices:

- Búsquedas frecuentes
- Campos como:
    - email
    - id
    - fechas


Otra cosas son los índices, pero en otro tipo de bases de datos. 


[[Indices en Mongo]]