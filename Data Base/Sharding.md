

 **Sharding = dividir los datos en varias bases de datos (servidores)**


## Ejemplo simple

Tienes una tabla de usuarios enorme:

	1,000,000 usuarios

 En lugar de una sola base de datos:

	❌ DB única → muy lenta

 Lo divides:

	DB1 → usuarios 1 - 500,000  
	DB2 → usuarios 500,001 - 1,000,000


---

## Sharding (datos)

 Divide la **información**

	Usuario 1 → DB1  
	Usuario 2 → DB2

Divides los libros:

	Edificio 1 → A-M  
	Edificio 2 → N-Z

---

## Load Balancing (balanceo)

 Divide el **tráfico**

	Request 1 → servidor A  
	Request 2 → servidor B

Varios empleados atienden:

	Persona 1 → empleado A  
	Persona 2 → empleado B


#Ejemplo 

```
App
 ↓
Backend
 ↓
├── DB Shard 1
├── DB Shard 2
├── DB Shard 3
```

---

#Resumen

|  **Concepto**  | **Qué divide** |
| :------------: | :------------: |
|    Sharding    |     datos      |
|  Replicación   |     copias     |
| Load balancing |    tráfico     |



[[Data Base/Beginnings|Beginnings]]



