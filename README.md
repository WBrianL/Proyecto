# 🚀 Sistema de Procesamiento de Pedidos Tolerante a Fallos

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white)
![Kafka](https://img.shields.io/badge/Kafka-231F20?style=for-the-badge&logo=apachekafka&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)

## 📌 Objetivo
Sistema distribuido para procesamiento de pedidos en línea con tolerancia integrada a fallos en:
- Microservicios
- Infraestructura de red
- Bases de datos
- Componentes críticos

## 🏗️ Arquitectura
```mermaid
graph TD
    F[Frontend] --> G[API Gateway]
    G --> O[Servicio de Pedidos]
    G --> I[Servicio de Inventario]
    O --> P[(PostgreSQL)]
    I --> R[(Redis)]
    O --> K[Apache Kafka]
    I --> K
    K --> M[Monitorización]
    M -->|Prometheus| GR[Grafana]
    P -->|Replicación| P2[(PostgreSQL Réplica)]
🛡️ Técnicas de Tolerancia a Fallos
Técnica	Implementación	Beneficio
Circuit Breaker	pybreaker con umbral de 3 fallos	Evita cascadas de fallos
Reintentos Exponenciales	tenacity (5 intentos max)	Manejo de fallos transitorios
Replicación PostgreSQL	Streaming replication	Alta disponibilidad de datos
Colas Resilientes	Kafka con retención 7 días	Cero pérdida de pedidos
Auto-Recuperación	Kubernetes + LivenessProbes	MTTR < 2 minutos
🚀 Despliegue
Requisitos
Docker 20.10+

Kubernetes 1.25+

Helm 3.10+ (opcional para monitorización)

1. Iniciar Infraestructura
bash
Copy
docker compose -f kafka-postgres.yml up -d
2. Desplegar Microservicios
bash
Copy
kubectl apply -f k8s-deployment.yml
3. Verificar Estado
bash
Copy
kubectl get pods -w
# O usando Lens/K9s
🔍 Simulación de Fallos
bash
Copy
# 1. Apagar réplica PostgreSQL
docker stop postgres-replica

# 2. Verificar failover automático
kubectl exec -it orders-service -- curl localhost:5000/health

# 3. Cortar tráfico a inventory-service
kubectl scale deploy inventory-service --replicas=0

# 4. Observar Circuit Breaker
watch -n 1 "kubectl logs orders-service | grep BREAKER"
📊 Métricas Clave
python
Copy
# Ejemplo de métricas en Prometheus
AVAILABILITY = """
sum(rate(http_requests_total{status!~"5.."}[5m])) 
/ 
sum(rate(http_requests_total[5m]))
"""

MTTR = """
avg(time() - process_start_time_seconds{job=~".*service"} 
* on(pod) group_left 
kube_pod_container_status_last_terminated_reason{reason="Error"})
"""
📂 Estructura del Proyecto
Copy
.
├── /frontend                 # Interfaz de usuario
├── /api-gateway              # Enrutamiento (FastAPI)
├── /orders-service           # Procesamiento de pedidos
│   ├── circuit_breaker.py    # Lógica de Circuit Breaker
│   └── kafka_producer.py     # Publicación a Kafka
├── /inventory-service        # Gestión de inventario
├── /k8s-deployment           # Configs Kubernetes
│   ├── hpa.yaml              # Auto-escalado
│   └── probes.yaml           # Health Checks
├── docker-compose.yml        # Orquestación local
└── chaos-test                # Pruebas de fallos
    └── network-partition.py  # Simulador de fallos
🧪 Pruebas de Resiliencia
python
Copy
# Ejemplo test con pytest
def test_order_processing_resilience():
    # 1. Simular fallo en inventory-service
    kill_pod("inventory-service-1")
    
    # 2. Enviar pedido
    response = post_order(test_order)
    
    # 3. Verificar recuperación
    assert response.status_code == 202  # Accepted
    assert kafka_message_exists("pending_orders")
📈 SLA Garantizado
Métrica	Objetivo	Implementación
Disponibilidad	99.95%	Replicación + Auto-healing
MTTR	< 2 min	Kubernetes + Probes
Pérdida de datos	0%	Kafka + Ack "all"
