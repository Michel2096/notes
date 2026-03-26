
Lo que quiero hacer para entregar este proyecto es lo siguiente

1. Dockerizar todo tanto en front, back y db

```
proyecto/
├── docker-compose.yml
├── frontend/          ← React
│   └── Dockerfile
├── backend/           ← Flask
│   └── Dockerfile
└── db/                ← PostgreSQL (ya lo tienes)
```

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

[[Examen]]