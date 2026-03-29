

Las extensiones en PostgreSQL son como "complementos" o "plugins" que añaden funcionalidades extra a la base de datos.


- **Generar IDs especiales** (UUIDs)
    
- **Encriptar datos** de forma segura
    
- **Buscar texto** de manera inteligente (con errores, sin tildes)
    
- **Optimizar rendimiento** para casos concretos


#Ejemplo 

```
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE EXTENSION IF NOT EXISTS "pgcrypto";

CREATE EXTENSION IF NOT EXISTS "pg_trgm";

CREATE EXTENSION IF NOT EXISTS "btree_gin";

CREATE EXTENSION IF NOT EXISTS "unaccent";
```


### Explicación de funcionamiento


## **1. uuid-ossp**

**Para generar identificadores únicos universales (UUID)**


```
-- Ejemplo: Generar un ID único para cada usuario
CREATE TABLE usuarios (
    id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
    nombre TEXT
);
```

**¿Cuándo usarlo?** Cuando necesitas IDs únicos que no se repitan aunque combines datos de diferentes bases de datos.


## **2. pgcrypto**

**Para encriptar datos y funciones de hash**


```
-- Ejemplo: Guardar contraseñas de forma segura
CREATE TABLE usuarios (
    email TEXT,
    password TEXT DEFAULT crypt('mi_password', gen_salt('bf'))
);
-- Verificar contraseña
SELECT * FROM usuarios 
WHERE email = 'user@ejemplo.com' 
AND password = crypt('contraseña_ingresada', password);
```

**¿Cuándo usarlo?** Para proteger información sensible como contraseñas, datos personales, o tokens.

## **3. pg_trgm**

**Para búsquedas por similitud de texto (como "Google te sugiere")**


```
-- Ejemplo: Buscar productos aunque el usuario escriba mal
SELECT * FROM productos 
WHERE similarity(nombre, 'telefono') > 0.3;
-- Encontraría "teléfono", "telefono movil", etc.
```

**¿Cuándo usarlo?** Para buscadores, autocompletado, o cuando quieres encontrar coincidencias aunque haya errores tipográficos.

## **4. btree_gin**

**Para crear índices más rápidos en búsquedas complejas**


```
-- Ejemplo: Índice que acelera búsquedas combinadas
CREATE INDEX idx_busqueda ON productos 
USING GIN (nombre, descripcion, categoria);
```

**¿Cuándo usarlo?** Cuando haces búsquedas en múltiples columnas y necesitas que sean muy rápidas.

## **5. unaccent**

**Para eliminar tildes y caracteres especiales**

```
-- Ejemplo: Buscar "José" aunque escriban "Jose"
SELECT * FROM usuarios 
WHERE unaccent(nombre) ILIKE unaccent('Jose');
-- Encuentra: José, Jose, JOSÉ, etc.
```

**¿Cuándo usarlo?** Para que las búsquedas de texto ignoren tildes y sean más flexibles.


---


![[Pasted image 20260327011040.png]]


[[Data Base/Beginnings|Beginnings]]