

## Opción 1: UNA sola base de datos (monolito disfrazado)


```
Microservicio A ─┐
Microservicio B ─┼──> Base de datos única
Microservicio C ─┘
```

## Problemas:

- Todos dependen de la misma BD
- Cambios afectan a todos
- Se rompe el desacoplamiento
- Difícil escalar

## Opción 2: UNA base de datos por microservicio (correcto)


```
Microservicio Usuarios ───> BD Usuarios
Microservicio Citas ──────> BD Citas
Microservicio Pagos ──────> BD Pagos
```

## Ventajas:

- Independencia total
- Cada servicio escala solo
- Puedes usar diferentes tecnologías
- Menos errores en cascada

# ¿Por qué esto es importante?

Porque en microservicios existe este principio clave:

 **“Cada servicio es dueño de sus datos”**

Esto se conoce como:

- Database per Service Pattern


---

# Entonces… ¿cómo se comunican?

Si cada uno tiene su BD, no pueden hacer:

❌ Esto:

```
SELECT * FROM usuarios JOIN citas ...
```

## 🔹 Comunicación correcta: API o eventos

### 1. Por API REST

```
Servicio Citas → pide datos → Servicio Usuarios
```

---

## Análisis de mi base de datos 

De acuerdo con un extenso análisis de mi base de datos, llegue a la conclusión, que de acuerdo al tipo de arquitectura que se posea, se debe construir la base de datos, una estructura monolito, no puede servir de nada. Es decir al momento de escalar, se puede romper de acuerdo a las exigencias de peticines 

```
Microservicio A ─┐
Microservicio B ─┼──> Base de datos única
Microservicio C ─┘
```

![[Pasted image 20260328020935.png]]

Lo que estoy construyendo esta directamente relacionado a un monolito

[[Data Base/Beginnings|Beginnings]]