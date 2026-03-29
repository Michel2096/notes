
##  Completo

CRUD es lo básico de cualquier base de datos. Son las 4 operaciones principales:

- **C (Create)** → Crear datos  
    Ejemplo: registrar un usuario nuevo
- **R (Read)** → Leer datos  
    Ejemplo: mostrar la lista de usuarios
- **U (Update)** → Actualizar datos  
    Ejemplo: cambiar el nombre de un usuario
- **D (Delete)** → Eliminar datos  
    Ejemplo: borrar un usuario


## Migraciones

Las **migraciones** son como un historial de cambios de tu base de datos.

Sirven para:

- Crear tablas
- Modificar columnas
- Eliminar tablas

👉 Es como tener un control de versiones (tipo Git pero para la BD)

Ejemplo en Laravel:

```
Schema::create('usuarios', function (Blueprint $table) {
    $table->id();
    $table->string('nombre');
    $table->timestamps();
});
```

Ventajas:

- Puedes recrear la base de datos desde cero
- Todo el equipo tiene la misma estructura
- Evitas errores manuales


[[SQL]]