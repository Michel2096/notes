

Sirve para:

- Reducir duplicación de datos
- Evitar errores al actualizar (ej: cambiar un dato en muchos lugares)
- Mejorar la integridad de la información
- Hacer la base de datos más limpia y lógica


## Primera Forma Normal (1FN) — Atomicidad

**Regla:** cada celda debe contener un único valor atómico (indivisible). Sin listas, sin conjuntos, sin valores repetidos dentro de una celda.


![[Pasted image 20260325171455.png]]


## Segunda Forma Normal (2FN) — Sin dependencias parciales


**Regla:** debe cumplir 1FN, y además cada columna no-clave debe depender de **toda** la clave primaria, no solo de una parte de ella. Esto aplica cuando la clave primaria es **compuesta** (formada por más de una columna).

Si la tabla de pedidos tiene como clave `(id_pedido, id_producto)`, el nombre del cliente depende solo de `id_pedido` — eso es una dependencia parcial y viola la 2FN.


![[Pasted image 20260325171640.png]]


## Tercera Forma Normal (3FN) — Sin dependencias transitivas

**Regla:** debe cumplir 2FN, y además ninguna columna no-clave debe depender de otra columna no-clave. La dependencia debe ir siempre directo a la clave primaria.

El ejemplo clásico: si tienes `id_cliente → ciudad → país`, entonces `país` depende de `ciudad`, no directamente de `id_cliente`. Eso es una **dependencia transitiva** y viola la 3FN.


![[Pasted image 20260325171734.png]]


## Resumen: las tres formas normales de un vistazo


![[Pasted image 20260325171851.png]]


Generalmente este tipo de normalización se aplica en lenguajes [[SQL]]