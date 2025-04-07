# Sistema Distribuido Tolerante a Fallas

![Docker](https://img.shields.io/badge/Docker-2CA5E0?style=flat&logo=docker&logoColor=white)
![Kafka](https://img.shields.io/badge/Apache_Kafka-231F20?style=flat&logo=apache-kafka&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat&logo=fastapi&logoColor=white)

## 📦 Componentes
- **API Gateway** (FastAPI) - Puerto: 8000
- **Orders Service** (PostgreSQL) - Puerto: 8001
- **Inventory Service** (Redis) - Puerto: 8002
- **Kafka** - Mensajería entre servicios

## 🚀 Instalación
```bash
git clone [repo-url]
cd proyecto-tolerante-fallas
docker compose up -d --build
