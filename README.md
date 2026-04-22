# Proceso-de-Selecci-n-de-Altos-Mandos-en-la-Industria-Automotriz
Proceso de Selección de Altos Mandos en la Industria Automotriz
AutoExec - Sistema de Gestion e InfraestructuraEste repositorio integra la arquitectura completa del proyecto AutoExec, abarcando servicios de backend, aplicaciones multiplataforma y una infraestructura de observabilidad y seguridad orquestada mediante contenedores.1. Estructura del ProyectoPlaintextAutoExec/
├── AppMovil/               # Aplicacion movil (Expo + React Native)
├── Backend/                # Nucleo de servicios de logica de negocio
│   ├── main.py             # API principal (FastAPI)
│   ├── web/                # Portal de gestion administrativa (Laravel)
│   ├── models/             # Definiciones de esquemas de datos
│   ├── routers/            # Modulos de rutas y endpoints
│   ├── security/           # Protocolos de autenticacion y cifrado
│   └── data/               # Gestion de persistencia y migraciones
├── crowdsec/               # Sistema de prevencion de intrusiones (IPS)
├── grafana/                # Visualizacion de metricas y dashboards
├── haproxy/                # Balanceador de carga de servicios (Layer 7)
├── loki/                   # Agregacion y almacenamiento de logs
├── nginx/                  # Servidor web y proxy inverso
├── prometheus/             # Base de datos de series temporales para metricas
├── promtail/               # Agente de recoleccion de logs
├── scripts/                # Automatizacion de despliegue y mantenimiento
├── docker-compose.yml      # Manifiesto de orquestacion de servicios
└── targets.json            # Configuracion de objetivos para Prometheus
2. Requisitos de Instalacion2.1. Entorno de Produccion/SimulacionDocker Engine v24.0 o superiorDocker Compose v2.0 o superior2.2. Entorno de Desarrollo (Nativo)Python 3.11+ (Entorno virtual recomendado)PHP 8.2+ y Composer 2.0+Node.js v20+ y NPM v10+PostgreSQL 15+3. Guia de Despliegue3.1. Ejecucion mediante Docker ComposeEl sistema esta diseñado para ejecutarse de forma integral mediante contenedores, lo que configura automaticamente redes, volumenes y dependencias entre servicios.Bash# Construccion de imagenes e inicio de servicios
docker compose up -d --build

# Verificacion de estado de los contenedores
docker compose ps
3.2. Configuracion de Variables de EntornoBackend Administrativo (Laravel)Ubicacion: Backend/web/.envFragmento de códigoDB_HOST=postgres
DB_PORT=5432
DB_DATABASE=autoexec
API_BASE_URL=http://api_core:8000
Aplicacion MovilUbicacion: AppMovil/src/config.jsEs imperativo configurar la IP privada del host para permitir la conectividad desde dispositivos externos.JavaScriptexport const API_URL = "http://[IP_PRIVADA_HOST]:8000";
4. Mapeo de Puertos y ServiciosServicioPuerto HostProtocoloDescripcionFrontend Web80HTTPInterfaz de usuario administrativaAPI Gateway8000HTTPPunto de entrada unico via HAProxyGrafana3000HTTPDashboard de observabilidadPrometheus9090HTTPMotor de metricasLoki3100HTTPGestion de logs distribuidos5. Infraestructura y Seguridad5.1. Balanceo de CargaEl servicio HAProxy actua como Gateway, distribuyendo las solicitudes entrantes entre las instancias disponibles de la API, garantizando alta disponibilidad y tolerancia a fallos.5.2. Seguridad de RedCrowdSec funciona como un motor de deteccion de comportamientos anomalos. Analiza los registros de Nginx y HAProxy para identificar ataques de fuerza bruta o escaneos de vulnerabilidades, aplicando bloqueos a nivel de red mediante el firewall del host.5.3. ObservabilidadEl stack de monitoreo permite la trazabilidad completa del sistema:Logs: Promtail extrae registros que son indexados en Loki.Metricas: Los servicios exponen endpoints para que Prometheus recolecte datos de rendimiento.Visualizacion: Grafana consolida ambas fuentes de datos en paneles criticos para el equipo de infraestructura.
