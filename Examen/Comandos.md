
1. Borrar los contenedores 

		docker compose down

2. Limpiar el cache 

		docker compose build --no-cache

3. Recomponer los contendores 

		docker compose up -d


## Construir contenedores espescifico

4. Reconstruir docker del frontend para actulizar el front
   
	   docker compose -f docker-compose.yml up --build frontend


## Llave de entorno de aws

### Clave de acceso

```
AKIAYCIFCRGIP6KPOIV4
```


### Clave de acceso secreta

```
`Hzf//ecL7lnCHHynWCHDcuae5yajn/ouU5/e2AwB`
```

### Código de llave 

```
`aws secretsmanager get-secret-value --secret-id taurus`
```


Crear una carpeta setting y dentro de ella un archivo key.py, una clase, dos funciones una para obtener informacion y set para mantener la informacion ^X