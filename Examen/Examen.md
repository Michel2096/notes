
Ayudame hacer esto dentro de un nuevo proyecto en aws, este proyecto la api se desarrollara en python, pero la base de datos debe estar dentro de docker y lo vamos a instanciar en aws. Este es el proceso general que se debe hacer: Debemos de tener un fronten, la api y la base de datos. Tambien ayudame a instanciar todo en aws paso a paso, tanto conectar la terminal de aws como desarrollar, cognito, subirlo en amplify, usar bien las autenticacion de codigo wr y lo que mas se necesite como usar lo que se llama secretos entre otras cosas

```
Login React
↓
Amazon Cognito
↓
MFA
↓
Cognito devuelve JWT
↓
React guarda token
↓
React llama Flask
↓
Flask valida JWT
↓
Acceso a la API
↓
Base de datos
```

---

# Estructura general del proyecto


    Directorio: C:\Users\Tauru\Downloads\tio_rico_app


`Mode                 LastWriteTime         Length Name`
`----                 -------------         ------ ----`
`d-----         3/14/2026  12:59 PM                backend`
`d-----          3/9/2026   3:18 PM                controllers`
`d-----          3/9/2026   2:27 PM                entorn`
`d-----         3/14/2026   9:10 AM                frontend`
`d-----          3/9/2026   3:18 PM                migrations`
`d-----          3/9/2026   3:18 PM                models`
`d-----          3/9/2026   3:18 PM                repository`
`d-----          3/9/2026   3:18 PM                services`
`d-----          3/9/2026   3:18 PM                settings`
`d-----          3/9/2026   3:18 PM                __pycache__`
`-a----          3/9/2026   3:18 PM            213 .env`
`-a----          3/9/2026   3:18 PM             24 .env.example`
`-a----          3/9/2026   3:18 PM            180 .gitignore`
`-a----          3/9/2026   3:18 PM            261 .gitlab-ci.yml`
`-a----          3/9/2026   3:18 PM           2232 app.py`
`-a----          3/9/2026   3:18 PM            465 config.py`
`-a----          3/9/2026   3:18 PM            349 docker-compose.yml`
`-a----          3/9/2026   3:18 PM            572 extension.py`
`-a----          3/9/2026   3:18 PM              0 README.md`
`-a----          3/9/2026   3:18 PM           1140 requirements.txt`


---

# Lista de dependecias 

```
alembic==1.18.4
attrs==25.4.0
blinker==1.9.0
boto3==1.42.65
botocore==1.42.65
certifi==2026.2.25
cffi==2.0.0
charset-normalizer==3.4.5
click==8.3.1
colorama==0.4.6
cors==1.0.1
ecdsa==0.19.1
filelock==3.25.1
flasgger==0.9.7.1
Flask==3.1.3
flask-cors==6.0.2
Flask-JWT-Extended==4.7.1
Flask-Migrate==4.1.0
Flask-SQLAlchemy==3.1.1
future==1.0.0
gevent==25.9.1
greenlet==3.3.2
idna==3.11
itsdangerous==2.2.0
Jinja2==3.1.6
jmespath==1.1.0
jsonschema==4.26.0
jsonschema-specifications==2025.9.1
Mako==1.3.10
MarkupSafe==3.0.3
mistune==3.2.0
packaging==26.0
psycopg2-binary==2.9.11
pyasn1==0.6.2
pycparser==3.0
PyJWT==2.11.0
PySocks==1.7.1
python-dateutil==2.9.0.post0
python-jose==3.5.0
PyYAML==6.0.3
referencing==0.37.0
requests==2.32.5
requests-file==3.0.1
rpds-py==0.30.0
rsa==4.9.1
s3transfer==0.16.0
six==1.17.0
SQLAlchemy==2.0.48
tldextract==5.3.1
typing_extensions==4.15.0
urllib3==2.6.3
Werkzeug==3.1.6
zope.event==6.1
zope.interface==8.2
```


```
pip install alembic attrs blinker boto3 botocore certifi cffi charset-normalizer click colorama cors ecdsa filelock flasgger Flask flask-cors Flask-JWT-Extended Flask-Migrate Flask-SQLAlchemy future gevent greenlet idna itsdangerous Jinja2 jmespath jsonschema jsonschema-specifications Mako MarkupSafe mistune packaging psycopg2-binary pyasn1 pycparser PyJWT PySocks python-dateutil python-jose PyYAML referencing requests requests-file rpds-py rsa s3transfer six SQLAlchemy tldextract typing_extensions urllib3 Werkzeug zope.event zope.interface
```


```
pip install python-dotenv
```



[[Aws]]
[[Estructura]]
