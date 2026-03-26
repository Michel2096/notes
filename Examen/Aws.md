

# Introduccion

Esta aplicación de AWS, se desarrolla con la finalidad de estudiar  como conectar tanto apis como el front. No obstante, se debe estudiar completamente toda la base.


	API
	FRONTEND
	BACKEND
	BASE DE DATOS
	


### Boto 3
En python en SDK (Software developer kit)

  
Boto3 simplifica el uso de los servicios de AWS porque ofrece un conjunto de bibliotecas que a los desarrolladores que usan Python les resultan lógicas y familiares. Todos los AWS SDK admiten consideraciones del ciclo de vida de las API, como administración de credenciales, reintentos, cálculo de referencias de datos y serialización.



> [!NOTE] NOTA
Para poder entender como es que funciona las credenciales, necesito el boton3, para definir credenciales y para analizar el entorno. Esto es necesario que lo considere para poder crear mis creedenciales  


## Bucket

El bucket es para almacenamiento escalable en la nube, estudiar los datos y mejorar las cargas de rendimiento de los datos 

	Hay diferencias entre start, lib, y npm run dev, pero cada uno tiene diferentes caracteristicas entorno al desarrollo del proyecto. 

```
	>>> import boto3
>>> s3 = boto3.resource('s3')
>>> for bucket in s3.buckets.all():
        print(bucket.name)
```


## Pagina para modificar la secret manager

```
https://docs.aws.amazon.com/boto3/latest/guide/secrets-manager.html
```

Cuando instanciamos un constructor dentro de una clase, la función init,


## Bucket 

Para poder configurar el bucket, se requiere abrir directemnte la  shell en aws, no solo para configurar directamente el comando en python dentro de aws. 


--- 

Ejemplo correcto de como se debe hacer: 
```
C:\Users\Tauru>python
Python 3.11.6 (tags/v3.11.6:8b6ee5b, Oct  2 2023, 14:57:12) [MSC v.1935 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import boto3
>>> client = boto3.client("s3")
>>> response = client.list_buckets()
>>> print(response)
{'ResponseMetadata': {'RequestId': '2WES5FX5YM77S84M', 'HostId': 'PcEkJrnX62rKvfxB8Ae48CsYfiVy08g6POHEkUi6yN2G6z2r0LKm4Zf6tKnQhe3hv/NlW/lb4Nw=', 'HTTPStatusCode': 200, 'HTTPHeaders': {'x-amz-id-2': 'PcEkJrnX62rKvfxB8Ae48CsYfiVy08g6POHEkUi6yN2G6z2r0LKm4Zf6tKnQhe3hv/NlW/lb4Nw=', 'x-amz-request-id': '2WES5FX5YM77S84M', 'date': 'Sat, 28 Feb 2026 16:09:58 GMT', 'content-type': 'application/xml', 'transfer-encoding': 'chunked', 'server': 'AmazonS3'}, 'RetryAttempts': 0}, 'Buckets': [{'Name': 'tio-rico-dev', 'CreationDate': datetime.datetime(2026, 2, 28, 15, 35, 22, tzinfo=tzutc()), 'BucketArn': 'arn:aws:s3:::tio-rico-dev'}], 'Owner': {'ID': 'cfb5d3b4fa2bb5fdefd1918dfbc388926d352c0d23792ff975f35f1c7f45f530'}}
>>>
```

## Region

Todo estara en la region 1 de virginia y despues haremos el cambio a la de ohio 




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

## Valor del secreto 

Este valor de secreto debe de tener estos datos, dentro de mi codigo y dentro de aws para que no tenga errores; Estos valores son importantes, debo de considerar colocarlos de acuerdo a las necesidades.

![[Pasted image 20260303184837.png]]

```
{
  "DB_USER": "root",
  "DB_PASSWORD": "",
  "DB_HOST": "localhost",
  "DB_PORT": "5432",
  "DB_NAME": "api",
  "JWT_SECRET_KEY": "super_secret_key"
}
```

#### ¿Que es amazon cognito?

Autenticar usuarios, maneja expresiones de sesiones, consideración de multifacor 
Es un servicio de de autenticacion en la nube 

	Dentro de nuestra tabla ya no va a existir el campo password 

Registro de usuarios y validacion de los tokens: Guardamos contraseña y usuario 


	Generaremos un correo de prueba: Para enviar un envio de email. 


Manejo de sesion y validacion de sesion y proteccion de datos. 


	Seguir usando JWT, e implementar relgas de seguridad
		1. Vamos a clocar expresiones 

## Pool

Dentro del pool, debemos de tomar en cuenta el access client, y el jwt para el client


Aunque yo genere un pull, yo no puedo ver la contraseña y tine expiracion y se puede hacer un backen y es responsable de la seguridad y nunca se ve la contraseña 


Configurar region
Configurar terminal
Crear bucket
Crear cognito
Amazon email Service: Para configurar el correo


# Lambda 

Hacer una confirmacion de registro, y/o cuando se autentifique y tambien sse ejecutara uno de custom. 


En el pool del usuario


Una mayuscula, minuscula y un caracters espescial 


## End point de cognito


Hacerlo para que quede de forma post, solo con nombre, password email.


# Autenticación por código QR




Subir el front a gitLab


Aws amplify

	Tengo que crear el ampify en aws


![[Pasted image 20260307093100.png]]


## DEPLOYAR

Para poder $deployar$ en los proyecto, debo colocar la ruta para poder iniciar tanto el front como el backend, para poder determinar la ruta correcta donde debe iniciar el sistema 

# Route 53 Para tener un dominio


Crear una spa > App client information  Vamos a crear dos cientes, uno par el back y otro para el front