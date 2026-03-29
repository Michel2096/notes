
## Seeds (Seeders)

Los **seeds** sirven para llenar la base de datos con datos de prueba automáticamente.

```
DB::table('usuarios')->insert([
    'nombre' => 'Miguel',
    'email' => 'miguel@gmail.com'
]);
```

Para qué sirven:

- Tener datos para probar tu sistema
- Simular usuarios, productos, etc.
- No tener que meter datos a mano



[[SQL]]
