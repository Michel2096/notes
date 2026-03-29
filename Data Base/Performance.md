
# 1. PERFORMANCE (rendimiento)

	 Es qué tan rápido responde tu base de datos.

```
SELECT * FROM users WHERE email = 'test@gmail.com';
```

Puede tardar:

- ⚡ 1 ms (bien optimizado)
- 🐢 3 segundos (mal optimizado)
## Fatores que afectan performance:

- índices
- diseño de tablas
- queries mal hechas
- cantidad de datos

---

# 2. USO CORRECTO DE ÍNDICES

 Un índice es como el índice de un libro 📖

---

## 🔹 Sin índice

Buscar "Miguel" → revisa TODA la tabla 😵

---

## 🔹 Con índice

Va directo a "Miguel" ⚡

---

##  Ejemplo en PostgreSQL

	CREATE INDEX idx_users_email ON users(email);


---

# 3. OPTIMIZACIÓN DE QUERIES

 Es escribir consultas eficientes

---

##  MAL

	SELECT * FROM users;

 trae todo 

---

## ✅ BIEN

	SELECT id, name FROM users WHERE id = 1;



[[Explain plans]]
[[Data Base/Beginnings|Beginnings]]