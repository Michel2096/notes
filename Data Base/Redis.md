
	 Es una **base de datos en memoria (RAM)**  
	 Ultra rápida (milisegundos → microsegundos)


## 1. CACHE (lo más importante)

 Guardar datos que se consultan mucho


## Ejemplo

Usuario entra a su perfil

Sin Redis:
	
	→ consulta a PostgreSQL (lento)

Con Redis:

	→ primero busca en Redis (rápido)  
	→ si no existe → consulta BD → guarda en Redis



# 2. Sesiones (login)

 Guardar sesiones de usuarios

	user_id → token

 Cuando haces login:

	Backend → guarda sesión en Redis

 Cuando haces requests:

	→ Redis valida rápido
## Flujo

```
Frontend
   ↓
Backend
   ↓
Redis (cache)
   ↓
PostgreSQL (solo si no hay cache)
```


# Resumen rápido

 Redis = nombre del software  
 Tipo = key-value  
 Categoría = NoSQL  
 General = DBMS

#Ejemplo 

```
async function getUser(id) {
  // 1. Buscar en Redis
  const cachedUser = await redis.get(`user:${id}`);

  if (cachedUser) {
    console.log("⚡ Datos desde Redis");
    return JSON.parse(cachedUser);
  }

  // 2. Si no existe → ir a BD (simulación)
  console.log("🐢 Datos desde DB");
  const userFromDB = {
    id,
    name: "Miguel"
  };

  // 3. Guardar en Redis (cache)
  await redis.set(`user:${id}`, JSON.stringify(userFromDB), "EX", 60);

  return userFromDB;
}
```

```
user:1
post:45
session:abc123
```


Otra seria guardar por columnas como:

[[Columnar]]