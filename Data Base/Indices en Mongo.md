

## Ejemplo básico

```
db.usuarios.createIndex({ email: 1 })
```

- `1` = ascendente
- `-1` = descendente

## Qué pasa?

Cuando haces:

```
db.usuarios.find({ email: "test@gmail.com" })
```

👉 MongoDB usa el índice automáticamente  
👉 Igual que PostgreSQL, tú no lo llamas


---

### Índice compuesto

```
db.usuarios.createIndex({ nombre: 1, edad: -1 })
```

### Índice único

```
db.usuarios.createIndex({ email: 1 }, { unique: true })
```


---

### Índice de texto (búsquedas tipo Google)

```
db.productos.createIndex({ nombre: "text" })

db.productos.find({ $text: { $search: "laptop gamer" } })
```


### Índice geoespacial (muy usado en apps tipo Uber 🚗)

```
db.ubicaciones.createIndex({ location: "2dsphere" })
```


|     **Concepto**      | **PostgreSQL** |    **MongoDB**     |
| :-------------------: | :------------: | :----------------: |
|  Funciones guardadas  |      ✅ Sí      |     ❌ No real      |
| Queries reutilizables |      ✅ Sí      | ⚠️ Backend / Views |
|    Vistas (views)     |      ✅ Sí      |        ✅ Sí        |
|     Lógica en BD      |      Alta      |     Media-baja     |
|     Flexibilidad      |     Media      |        Alta        |
