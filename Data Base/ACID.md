

**ACID** es un conjunto de **propiedades que garantizan que las transacciones en una base de datos sean seguras y confiables**.

Ejemplo de transacción:

```
Transferir dinero de cuenta A → cuenta B
```

# Significado de ACID

- **A** → Atomicidad
- **C** → Consistencia
- **I** → Aislamiento
- **D** → Durabilidad

## Ejemplo

```
BEGIN;

Restar $100 cuenta A
Sumar $100 cuenta B

COMMIT;
```

|  Propiedad   |   **Significa**do    |
| :----------: | :--------------: |
|  Atomicidad  |   Todo o nada    |
| Consistencia |  Datos válidos   |
| Aislamiento  | No interferencia |
| Durabilidad  |   Persistencia   |


[[Tipos de normalizacion]]