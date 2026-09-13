# 🚀 NGINX Production Configuration & Docker Setup

Este repositorio contiene una plantilla de configuración de producción de **NGINX** optimizada para alta concurrencia, seguridad, balanceo de carga y soporte multiservicio (PHP, Python, Java).

---

## 📁 Estructura del Repositorio

```text
.
├── nginx.conf                 # Archivo de configuración principal de NGINX
├── Dockerfile                 # Dockerfile para construir la imagen personalizada
├── docker-compose.yml         # Orquestación de servicios (NGINX + Backends)
├── certs/                     # Certificados TLS/SSL para pruebas locales
│   ├── www.mi-web.com.crt
│   └── www.mi-web.com.key
├── html/                      # Archivos web estáticos
│   └── index.html
└── README.md                  # Guía de despliegue y pruebas
```

---

## 🏗️ Arquitectura de NGINX

NGINX emplea un modelo asíncrono y basado en el bucle de eventos (`epoll` en Linux) para gestionar un elevado volumen de conexiones concurrentes de manera eficiente con un bajo consumo de recursos:

* **Master Process:** Encargado de leer la configuración, abrir sockets de red y administrar los procesos trabajadores.
* **Worker Processes:** Procesan las solicitudes entrantes de los clientes en paralelo y de forma no bloqueante.

---

## 🛠️ Requisitos Previos

* [Docker Engine](https://docs.docker.com/get-docker/) (v20.10+)
* [Docker Compose](https://docs.docker.com/compose/install/) (v2.0+)
* `openssl` (para la generación de certificados SSL locales)

---

## 🚀 Despliegue Rápido con Docker

### 1. Clonar el repositorio
```bash
git clone https://github.com/tu-usuario/nginx-production-setup.git
cd nginx-production-setup
```

### 2. Generar certificados SSL autofirmados (Entorno Local)
```bash
mkdir -p certs
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout certs/www.mi-web.com.key \
  -out certs/www.mi-web.com.crt \
  -subj "/CN=mi-web.com"
```

### 3. Crear archivo de contraseñas para autenticación básica
```bash
htpasswd -c .htpasswd admin
```

### 4. Iniciar los contenedores
```bash
docker-compose up -d --build
```

---

## 🧪 Verificación y Pruebas de Endpoints

### Redirección HTTP a HTTPS (301)
```bash
curl -I http://localhost
# Respuesta esperada: HTTP/1.1 301 Moved Permanently -> Location: https://...
```

### Prueba de Conexión HTTPS (SNI)
```bash
curl -k https://localhost
```

### Prueba de Límite de Tasa de Peticiones (Rate Limiting)
```bash
for i in {1..15}; do curl -s -o /dev/null -w "%{http_code}\n" https://localhost/; done
# Observa cómo las peticiones que superan la ráfaga devuelven un código 503 Service Unavailable.
```

### Prueba de Área Restringida (Autenticación Básica)
```bash
curl -u admin:tu_contraseña https://localhost/privado/
```

---

## 📜 Licencia y Contribuciones

Este proyecto se distribuye bajo la licencia MIT. ¡Siente libre de hacer fork, enviar pull requests o utilizarlo como plantilla base para tus infraestructuras de producción!
