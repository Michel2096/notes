

Las **llaves (keys)** son **atributos que permiten identificar registros y relacionar tablas** dentro de una base de datos.

 Sirven para:

- Identificar datos de forma única
- Conectar tablas entre sí
- Mantener el orden y la integridad

![[Pasted image 20260326123351.png]]

# Ejemplo 

```
Alumno
- id_alumno (PK)
- nombre
- edad
```

Aquí:

- `id_alumno` identifica a cada alumno de forma única

---

# Llave foránea (Foreign Key - FK)


Es un campo que **hace referencia a la llave primaria de otra tabla**.

## ¿Para qué sirve?

- Relacionar tablas
- Mantener integridad referencial
- Evitar datos inválidos


#  Ejemplo 


```
Alumno
- id_alumno (PK)
- nombre

Curso
- id_curso (PK)
- nombre

Inscripcion
- id_inscripcion
- id_alumno (FK)
- id_curso (FK)
```

Aquí:

- `id_alumno` en Inscripción apunta a Alumno
- `id_curso` en Inscripción apunta a Curso

---

## Tabla de diferencias 

|  Tipo de llave   |       **F**unción        |
| :--------------: | :------------------: |
| 🔑 Primaria (PK) | Identifica registros |
| 🔗 Foránea (FK)  |    Conecta tablas    |



[[Data Base/Beginnings|Beginnings]]