# Proceso-de-Selecci-n-de-Altos-Mandos-en-la-Industria-Automotriz
Proceso de Selección de Altos Mandos en la Industria Automotriz


AutoExec - Sistema de Gestion e Infraestructura

Este repositorio integra la arquitectura completa del proyecto AutoExec, abarcando servicios de backend, aplicaciones multiplataforma y una infraestructura de observabilidad y seguridad orquestada mediante contenedores.

## 1. Estructura del ProyectoPlaintextAutoExec/
- ├── AppMovil/               # Aplicacion movil (Expo + React Native)
- ├── Backend/                # Nucleo de servicios de logica de negocio
- │   ├── main.py             # API principal (FastAPI)
- │   ├── web/                # Portal de gestion administrativa (Laravel)
- │   ├── models/             # Definiciones de esquemas de datos
- │   ├── routers/            # Modulos de rutas y endpoints
- │   ├── security/           # Protocolos de autenticacion y cifrado
- │   └── data/               # Gestion de persistencia y migraciones
- ├── crowdsec/               # Sistema de prevencion de intrusiones (IPS)
- ├── grafana/                # Visualizacion de metricas y dashboards
- ├── haproxy/                # Balanceador de carga de servicios (Layer 7)
- ├── loki/                   # Agregacion y almacenamiento de logs
- ├── nginx/                  # Servidor web y proxy inverso
- ├── prometheus/             # Base de datos de series temporales para metricas
- ├── promtail/               # Agente de recoleccion de logs
- ├── scripts/                # Automatizacion de despliegue y mantenimiento
- ├── docker-compose.yml      # Manifiesto de orquestacion de servicios
- └── targets.json            # Configuracion de objetivos para Prometheus

- 
## 2. Requisitos de Instalacion

**2.1. Entorno de Produccion/SimulacionDocker Engine v24.0 o superior**

Docker Compose v2.0 o superior2.2.

Entorno de Desarrollo (Nativo)Python 3.11+ (Entorno virtual recomendado)

PHP 8.2+ y Composer 2.0+Node.js v20+ y NPM v10+PostgreSQL 15+

## 3.Guia de Despliegue 

**3.1. Ejecucion mediante Docker Compose**

El sistema esta diseñado para ejecutarse de forma integral mediante contenedores, lo que configura automaticamente redes, volumenes y dependencias entre servicios.

Bash# 

Construccion de imagenes e inicio de servicios


`docker compose up -d --build`

Limpiar caché de Laravel (solo si hay errores)

`docker exec laravel_web php artisan config:clear`

`docker exec laravel_web php artisan route:clear`

Acceso

| Servicio | URL |

| Panel Web Laravel | http://localhost:8080 |

| API FastAPI | http://localhost:5000 |

| Docs de la API | http://localhost:5000/docs |

| PostgreSQL | localhost:5434 |

Credenciales por defecto para acceder como reclutador en la app web

| Campo | Valor |

| Email | admin@automotriz.com |

| Contraseña | admin123 |

**3.2. Requerimentos para el uso de las apps (web + Movil)**

Entrar a la carpeta del proyecto laravel (app web)

`cd AutoExec\Backend\`
Ejecutar el comando para instalar las dependencias de PHP y Node
`composer install
npm install`

Crear el proyecto Laravel (solo la primera vez)

`docker-compose run --rm web sh
composer create-project laravel/laravel /tmp/laravel --prefer-dist
cp -r /tmp/laravel/. /var/www/html/
chmod -R 775 storage bootstrap/cache
exit`

Configurar el .env de Laravel

Copia el archivo de ejemplo:

cp web/.env.example web/.env

Edita web/.env y asegúrate de tener:

APP_NAME="Automotive Recruitment Admin"

APP_URL=http://localhost:8080

API_BASE_URL=http://api:8000

API_TIMEOUT=30

SESSION_DRIVER=file

CACHE_STORE=file

**Antes de levantar el server de la app movil revisar el archivo**

AppMovil/src/config.js Es imperativo configurar la IP privada del host para permitir la conectividad desde dispositivos externos. asegurarse de poner la ip de su red (en cmd `ip config` seleccionar la ipv4 ya sea ethernet o inalambrica dependiendo su modo de conexión)

En la terminal del proyecto, navegar a la ruta de la app movil

`cd AutoExec/AppMovil/`

ejecutar el comando

`npm install`

Levantamos el servidor de expo

`npx expo start`

(Escanea el código QR con la app Expo Go en tu celular para ver la aplicación).

# Verificacion de estado de los contenedores

`docker compose ps`

**3.3. Configuracion de Variables de Entorno**

Backend Administrativo (Laravel) Ubicacion: Backend/web/.env

Fragmento de código

DB_HOST=postgres

DB_PORT=5432

DB_DATABASE=autoexec

API_BASE_URL=http://api_core:8000

Aplicacion Movil

Ubicacion: AppMovil/src/config.js Es imperativo configurar la IP privada del host para permitir la conectividad desde dispositivos externos.

JavaScriptexport const API_URL = "http://[IP_PRIVADA_HOST]:8000";

## 4. Mapeo de Puertos y Servicios
  
Servicio Puerto

Host Protocolo Descripcion Frontend Web80HTTP 

Interfaz de usuario administrativa

API

Gateway

8000HTTP

Punto de entrada unico via HAProxyGrafana3000HTTP

Dashboard de observabilidad

Prometheus9090HTTP

Motor de metricasLoki3100HTTP

Gestion de logs distribuidos

## 5. Infraestructura y Seguridad

**5.1. Balanceo de Carga**

El servicio HAProxy actua como Gateway, distribuyendo las solicitudes entrantes entre las instancias disponibles de la API, garantizando alta disponibilidad y tolerancia a fallos.

**5.2. Seguridad de RedCrowdSec**

Funciona como un motor de deteccion de comportamientos anomalos. Analiza los registros de Nginx y HAProxy para identificar ataques de fuerza bruta o escaneos de vulnerabilidades, aplicando bloqueos a nivel de red mediante el firewall del host.5.3.

Observabilidad

El stack de monitoreo permite la trazabilidad completa del sistema:

Logs: Promtail extrae registros que son indexados en Loki.Metricas: Los servicios exponen endpoints para que Prometheus recolecte datos de rendimiento.

Visualizacion: Grafana consolida ambas fuentes de datos en paneles criticos para el equipo de infraestructura.
