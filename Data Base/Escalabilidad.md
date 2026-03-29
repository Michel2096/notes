

 Capacidad de tu sistema para crecer

## Tipos:

##  Vertical

 mejorar servidor

	más RAM, más CPU

## Horizontal

 más servidores

	varias bases de datos


---


# 6. REPLICACIÓN

 Copiar la base de datos
## Ejemplo:

Master → escribe  
Replica → solo lee


## Uso

- muchas lecturas
- alta disponibilidad

```
App
 ↓
├── DB Master (writes)
└── DB Replica (reads)
```


---



[[Data Base/Beginnings|Beginnings]]