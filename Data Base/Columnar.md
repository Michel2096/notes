
# Qué es una base de datos columnar?

 Es una base de datos que guarda los datos **por columnas en lugar de filas**

```
id | nombre | edad
1  | Miguel | 20
2  | Ana    | 25
```

	Fila1 completa → Fila2 completa → Fila3 completa

# ¿Para qué se usa?

Para:

- análisis de datos (analytics)
- reportes
- big data
- dashboards

#Ejemplo 

Imagina una red social:

millones de posts  
millones de usuarios  
millones de likes

---

##  Consulta típica:

	 “¿Cuántos likes hubo hoy?”

---

 Columnar lo hace MUY rápido porque:

	solo lee columna likes


## Bases columnar reales:

- Apache Cassandra
- ClickHouse
- Amazon Redshift
- BigQuery