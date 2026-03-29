
Es ver **cómo la base de datos ejecuta tu query**

---

## 🔹 Ejemplo

EXPLAIN SELECT * FROM users WHERE email = 'test@gmail.com';

---

##  Te dice:

- si usa índice
- si hace full scan (malo)
- cuánto cuesta la consulta

---

## Tipos importantes

- **Seq Scan** → escanea toda la tabla (malo)
- **Index Scan** → usa índice (bueno)