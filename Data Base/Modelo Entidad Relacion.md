

Antes de crear una base de datos, diseñamos cómo se relacionan las cosas del mundo real. En el modelo ER tenemos tres elementos clave:

- **Entidades**: cosas del mundo real que queremos modelar (Cliente, Producto, Pedido)

```
Entidad: Alumno
```

- **Atributos**: propiedades de esas entidades (nombre, precio, fecha)

```
	Alumno:
- id_alumno
- nombre
- edad
- correo
```

- **Relaciones**: cómo se conectan las entidades entre sí

```
	Alumno — se inscribe en — Curso
```

Y las relaciones tienen **cardinalidad**, es decir, cuántos registros de un lado se relacionan con cuántos del otro:


![[Pasted image 20260325164616.png]]


---

# Cardinalidad 

*Indica la cantidad de relaciones posibles entre entidades*

**Responde preguntas como:**

- ¿Un usuario puede tener varios pedidos?
- ¿Un alumno puede estar en varios cursos?
- ¿Un pedido pertenece a uno o varios usuarios?


### Ejemplos de cardinalidad

## Uno a uno (1:1)

Cada elemento se relaciona con solo uno del otro lado.

#Ejemplo:

Persona — tiene — Pasaporte

```
- Una persona → un pasaporte
- Un pasaporte → una persona
```

💡 Uso real: datos muy específicos o separados por seguridad


## Uno a muchos (1:N)

 Una entidad se relaciona con muchas de otra.

#Ejemplo:

Cliente — hace — Pedido

```
- Un cliente → muchos pedidos
- Un pedido → un solo cliente
```

💡 Es la **más común en bases de datos**


## Muchos a muchos (N:M)

 Muchas entidades se relacionan con muchas.

#Ejemplo:

Alumno — cursa — Materia

```
- Un alumno → muchas materias
- Una materia → muchos alumnos
```

💡 Esto **no se implementa directamente**, se usa una tabla intermedia.

---

# Cómo se lleva a la base de datos


#Caso 1:1

Se resuelve con una **clave foránea (FK)**

```
Cliente
- id_cliente

Pedido
- id_pedido
- id_cliente (FK)
```

#Caso N:M

Se crea una **tabla intermedia**

```
Alumno
Curso
Inscripcion
- id_alumno (FK)
- id_curso (FK)
```

# Ejemplo real


# Ejemplo real completo

**Sistema de ventas**

```
Usuario (1) —— (N) Pedido  
Pedido (N) —— (M) Producto
```

 ==Interpretación:==

- Un usuario puede tener muchos pedidos
- Un pedido puede tener muchos productos
- Un producto puede estar en muchos pedidos

	***N:M** → muchos con muchos*



[[Data Base/Beginnings|Beginnings]]
[[OLTP vs OLAP]]