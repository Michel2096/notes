
### Piensa en esto...

Imagina que tienes un **formulario de registro** en una app. Una de las preguntas es:

**"¿Cuál es tu estado civil?"**

Las opciones son:

- Soltero
    
- Casado
    
- Divorciado
    
- Viudo
    
No puedes poner "Pepito" o "feliz" o "en duda". **Solo esas 4 opciones**.

---

**Un ENUM es exactamente eso**: una lista de opciones fijas que no cambian. Es como un menú de restaurante: solo puedes pedir lo que está en el menú.


### ¿Dónde se usa?

**En las tablas de tu base de datos**. Cuando creas una columna, puedes decirle: "esta columna solo acepta estos valores específicos".


```
-- Creo mi lista de opciones (el menú)
CREATE TYPE estado_usuario AS ENUM ('activo', 'suspendido', 'eliminado');

-- Creo una tabla que usa esa lista
CREATE TABLE usuarios (
    id SERIAL PRIMARY KEY,
    nombre TEXT,
    estado estado_usuario  -- 👈 Esta columna SOLO acepta esos 3 valores
);

```
### ¿Cuando se usa?

**Cuando tienes algo que solo puede ser UNA de varias opciones limitadas.**


![[Pasted image 20260327011901.png]]




[[SQL]]
