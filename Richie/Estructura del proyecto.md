
```
project-root/
│
├── docker-compose.yml
├── .env
├── README.md
│
├── nginx/
│   └── nginx.conf
│
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   │
│   └── app/
│       ├── main.py                # Punto de entrada FastAPI
│       ├── database.py            # Conexión SQLAlchemy
│       ├── core/
│       │   ├── config.py          # settings, JWT, env
│       │   └── security.py        # auth, hash, tokens
│       │
│       ├── Controllers/           # Rutas / Endpoints
│       │   ├── auth_controller.py
│       │   └── user_controller.py
│       │
│       ├── Services/              # Lógica de negocio
│       │   ├── auth_service.py
│       │   └── user_service.py
│       │
│       ├── Repositories/          # Acceso a BD
│       │   ├── user_repository.py
│       │   └── auth_repository.py
│       │
│       ├── Modelo/                # Modelos SQLAlchemy
│       │   └── user_model.py
│       │
│       ├── Schemas/               # Pydantic (DTOs)
│       │   ├── user_schema.py
│       │   └── auth_schema.py
│       │
│       └── Migraciones/
│           └── alembic/
│               ├── env.py
│               ├── script.py.mako
│               └── versions/
│
├── frontend/
│   ├── Dockerfile
│   ├── index.html
│   ├── package.json
│   ├── tsconfig.json
│   └── src/
│       ├── main.tsx
│       ├── App.tsx
│       ├── api/
│       │   ├── axios.ts
│       │   └── auth.api.ts
│       ├── pages/
│       │   ├── Login.tsx
│       │   └── Dashboard.tsx
│       ├── components/
│       └── hooks/
│
└── postgres_data/   # volumen Docker (NO tocar)

```


