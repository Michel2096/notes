
Esta es la estructura del proyecto que vamos hacer 

Api


App              // Punto de entrada de la aplicación
	
			/Controllers     // Controladores			
			/Services        // Logica del negocio	
			/Repositories    // Acceso a la BD
			/Modelo          // Modelo de BD
			/Schemas         // Seriacion de datos
			/Migraciones     // Migraciones


	Api
	
	Proyecto web

Estos dos los vamos hacer en un archivo bundle 


Vamos hacer la arquitectura limpia, ya no sera la mvc 
## Otra parte 

Cliente
↓
Controllers
↓
Services
↓
Repository
↓
Modelo
↓ 
BD


La arquitectura mvc, las rutas estarán en los controllers porque sera una arquitectura limpia. 


### Requisitos 

1. Post man 
2. Insomnia

### Venv
Los entornos virtuales se usaran para tener su propia version y no tenga conflictos con otros. 

Para evitar conflictos entre otros proyectos 

Para poder crear el entorno:

```
python -m venv "virtual"
	Script     > Para ejecutar en windows
	bin        > Para ejecutar en linux
	lib        > Aqui se guardan en las librerias
	pyenv.cfg  > Solo es un archivo de configuración
```

### Para activar el venv

```
virtual/Script/activate    //Windows
virtual/bin/activate
```

#### Para instalar algún paquete 

```
Se instala con pip 

	pip install flask 
	pip freeze > lib.txt 
	pip list
	pip install -r lib.txt	
```

## Para iniciar el proyecto 

```
app.py

from flask import * o Flask

	def app():
		app= Flask (__name__)
		
		@app.route("/"):
		def home ():
		return "Hola mundo"
		
		
	app = app(): 
	app.run(host = "0.0.0.0")
	
```

@ Decorador  > Hacer que se vea bonito el código 


	Ejemplo

Django 3.x
Django 2.x

## Lógica 
1. El cliente es el front

2. Los servicios son las validaciones 

3. Los repositorios estaran en la base de datos 


	
	

	
		App                   // Punto de entrada de la aplicacion
				El archivo base de app, en el archivo de                      inicio
			/Controllers     // Controladores
					Aqui seran los controladores
			/Services        // Logica del negocio
				
		/Repositories    // Acceso a la BD
			Solo tendremos acceso a la base de datos
		/Modelo          // Modelo de BD
		/Schemas         // Seriacion de datos
				Es la estructura de la base de datos de la                    tabla
		/Migraciones     // Migraciones
				 



# Abrir el proyecto 

Para abrir el swagger, debo abrir el apidocs, para poder ver la documentacion de la api


```
	http://127.0.0.1:5001/
	http://localhost:5001/apidocs/
```






# Indice
[[Estructura del proyecto]]
[[Git Gub]]
[[Richie/Extensiones]]
[[Documentation]]
[[Comandos]]
[[Historia de Abuso]]
[[Proceso de codigo]]
[[Richie/Microservicios]]
[[Examen]]

