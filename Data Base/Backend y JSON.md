
# ¿Por qué pedían un JSON?

El **JSON** es el formato estándar para enviar datos entre sistemas.

 Básicamente:

- El **frontend** manda JSON
- El **backend** recibe JSON
- La **base de datos** guarda esos dato

El flujo es así:

```
Frontend → JSON → Backend → Base de datos
```

El frontend envía:

```
{
  "nombre": "Miguel",
  "email": "miguel@gmail.com",
  "password": "123456"
}
```

### El backend recibe eso:

Ejemplo en Laravel:

```
$request->input('nombre');
```

### Y lo guarda:

```
User::create($request->all());
```

---

## 1. Definir la estructura de datos (MUY común)

Antes de programar, alguien define:

 “¿Qué datos va a tener esto?”

Ejemplo:

{  
  "nombre": "",  
  "email": "",  
  "telefono": ""  
}

💡 Ese JSON puede ir vacío, solo como **plantilla**.

 Sirve para:

- Saber qué campos existen
- Acordar frontend ↔ backend

---

## 2. Probar endpoints (API testing)

El backend necesita probar si funciona.

Herramientas:

- Postman
- Insomnia

 Ahí mandan JSON manualmente.


[[JSON]]