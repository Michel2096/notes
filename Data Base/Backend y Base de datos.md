
#### Postgress

Otra cosa a estudiar es como postgres trabaja con json y sql, por el cual se manejan en el backend nuevas funciones y se determinan asi.


#Ejemplo 

```
CREATE FUNCTION obtener_usuarios_activos()
RETURNS TABLE(nombre TEXT, email TEXT) AS $$
BEGIN
    RETURN QUERY
    SELECT nombre, email
    FROM usuarios
    WHERE activo = true;
END;
$$ LANGUAGE plpgsql;
```

### ¿Qué hace?

- Guarda la lógica en la BD
- Puedes llamarla desde backend así:

Por otro lado, podemos determinar [[Indices]], por los cuales podemos trabajar mas rápido dentro de cada uno de los registros 




[[No SQL y Mongo]]

