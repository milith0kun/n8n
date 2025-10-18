# 🚀 N8N + WAHA WhatsApp Automation Project

Este proyecto proporciona una configuración completa de N8N con WAHA para automatización de WhatsApp, optimizado para despliegue en servidor con IP pública.

## 🌐 Despliegue en Servidor (Recomendado)

### Requisitos del Servidor

- **Ubuntu 20.04+ / CentOS 7+ / Amazon Linux 2**
- **Docker** y **Docker Compose** instalados
- **Puerto 80** abierto en el firewall
- **IP pública** asignada al servidor

### Instalación Rápida en Servidor

```bash
# 1. Conectar al servidor
ssh ubuntu@tu_ip_publica

# 2. Instalar Docker (Ubuntu)
sudo apt update
sudo apt install docker.io docker-compose -y
sudo usermod -aG docker $USER
newgrp docker

# 3. Clonar el repositorio
git clone https://github.com/milith0kun/n8n.git
cd n8n

# 4. Configurar variables de entorno
cp .env.example .env
nano .env
# Editar SERVER_IP=tu_ip_publica

# 5. Iniciar servicios
docker-compose up -d

# 6. Verificar estado
docker-compose ps
```

### Acceso a los Servicios

Una vez desplegado, accede a través de tu IP pública:

- **🎯 N8N Dashboard**: `http://tu_ip_publica/`
- **📱 WAHA Dashboard**: `http://tu_ip_publica/waha/dashboard`
- **📚 WAHA API Docs**: `http://tu_ip_publica/waha-docs/`
- **🔍 Health Check**: `http://tu_ip_publica/health`

### Configuración de Variables de Entorno

Edita el archivo `.env` con tu configuración:

```bash
# IP pública de tu servidor
SERVER_IP=52.14.254.17

# Credenciales de N8N
N8N_USER=admin
N8N_PASSWORD=tu_password_seguro

# Credenciales de WAHA
WAHA_USER=admin
WAHA_PASSWORD=tu_password_waha
```

### Credenciales de WAHA

Las credenciales se generan automáticamente al iniciar WAHA. Para obtenerlas:

```bash
docker logs waha
```

Busca las líneas que contienen:
- `WAHA_API_KEY`
- `WAHA_DASHBOARD_USERNAME`
- `WAHA_DASHBOARD_PASSWORD`

### Configuración de WhatsApp

1. Accede al WAHA Dashboard: `http://tu_ip_publica/waha/dashboard`
2. Usa las credenciales obtenidas del log
3. Crea una nueva sesión de WhatsApp
4. Escanea el código QR con tu WhatsApp

## 💻 Instalación Local (Desarrollo)

Si prefieres ejecutar el proyecto localmente:

```bash
# 1. Clonar el repositorio
git clone https://github.com/milith0kun/n8n.git
cd n8n

# 2. Configurar para desarrollo local
cp .env.example .env
# Editar SERVER_IP=localhost

# 3. Iniciar servicios
docker-compose up -d

# 4. Acceder a:
# - N8N: http://localhost/
# - WAHA: http://localhost/waha/dashboard
## 📁 Estructura del Proyecto

```
n8n/
├── docker-compose.yml          # Configuración principal de servicios
├── nginx.conf                  # Configuración del proxy reverso
├── .env.example               # Plantilla de variables de entorno
├── .gitignore                 # Archivos excluidos del repositorio
├── n8n-data/                  # Datos persistentes de N8N
│   ├── config/               # Configuraciones de N8N
│   └── database.sqlite       # Base de datos SQLite
└── PLANTILLAS Adrián Sáenz/   # Workflows predefinidos
    ├── Automatización WhatsApp/
    ├── Integración APIs/
    └── Procesamiento de Datos/
```

## 🔧 Comandos Útiles

### Gestión de Servicios

```bash
# Iniciar todos los servicios
docker-compose up -d

# Ver estado de los servicios
docker-compose ps

# Ver logs en tiempo real
docker-compose logs -f

# Reiniciar un servicio específico
docker-compose restart n8n
docker-compose restart waha

# Detener todos los servicios
docker-compose down

# Detener y eliminar volúmenes
docker-compose down -v
```

### Monitoreo y Diagnóstico

```bash
# Ver logs de N8N
docker-compose logs n8n

# Ver logs de WAHA
docker-compose logs waha

# Ver logs de Nginx
docker-compose logs nginx

# Verificar conectividad
curl http://tu_ip_publica/health

# Verificar puertos abiertos
netstat -tlnp | grep :80
```

## 🛠️ Solución de Problemas

### Problemas Comunes

#### 1. Puerto 80 ocupado
```bash
# Verificar qué proceso usa el puerto 80
sudo lsof -i :80

# Detener Apache si está corriendo
sudo systemctl stop apache2
sudo systemctl disable apache2
```

#### 2. Permisos de Docker
```bash
# Agregar usuario al grupo docker
sudo usermod -aG docker $USER
newgrp docker
```

#### 3. Firewall bloqueando conexiones
```bash
# Ubuntu/Debian
sudo ufw allow 80/tcp

# CentOS/RHEL
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload
```

#### 4. N8N no accesible
```bash
# Verificar que N8N esté corriendo
docker-compose ps n8n

# Reiniciar N8N
docker-compose restart n8n

# Ver logs para errores
docker-compose logs n8n
```

## 🔒 Recomendaciones de Seguridad

### Configuración Básica

1. **Cambiar contraseñas por defecto**
   ```bash
   # Editar .env con contraseñas seguras
   nano .env
   ```

2. **Configurar HTTPS** (Recomendado para producción)
   ```bash
   # Instalar Certbot para SSL
   sudo apt install certbot python3-certbot-nginx
   sudo certbot --nginx -d tu_dominio.com
   ```

3. **Configurar firewall**
   ```bash
   # Permitir solo puertos necesarios
   sudo ufw enable
   sudo ufw allow ssh
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   ```

4. **Backup regular**
   ```bash
   # Crear backup de datos N8N
   docker-compose exec n8n tar -czf /backup/n8n-backup-$(date +%Y%m%d).tar.gz /home/node/.n8n
   ```

### Variables de Entorno Sensibles

Nunca commits archivos `.env` con credenciales reales. Usa siempre:
- Contraseñas fuertes (mínimo 12 caracteres)
- Diferentes credenciales para cada servicio
- Rotación periódica de contraseñas

## 📋 Plantillas Incluidas

El proyecto incluye workflows predefinidos para:

### Automatización WhatsApp
- **Respuestas automáticas**: Responder mensajes basados en palabras clave
- **Notificaciones**: Enviar alertas automáticas
- **Chatbot básico**: Interacciones conversacionales

### Integración APIs
- **Webhook handlers**: Procesar webhooks entrantes
- **API connectors**: Conectar con servicios externos
- **Data sync**: Sincronización de datos entre plataformas

### Procesamiento de Datos
- **CSV processing**: Procesar archivos CSV
- **Data transformation**: Transformar y limpiar datos
- **Scheduled tasks**: Tareas programadas

## 🤝 Contribución

1. Fork el repositorio
2. Crea una rama para tu feature (`git checkout -b feature/nueva-funcionalidad`)
3. Commit tus cambios (`git commit -am 'Agregar nueva funcionalidad'`)
4. Push a la rama (`git push origin feature/nueva-funcionalidad`)
5. Crea un Pull Request

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Ver el archivo `LICENSE` para más detalles.

## 🆘 Soporte

### Documentación Oficial
- [N8N Documentation](https://docs.n8n.io/)
- [WAHA Documentation](https://waha.devlike.pro/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

### Problemas y Sugerencias
- Reportar issues en: [GitHub Issues](https://github.com/milith0kun/n8n/issues)
- Discusiones en: [GitHub Discussions](https://github.com/milith0kun/n8n/discussions)

## 📞 Contacto

- **Autor**: milith0kun
- **Email**: milith0kun@gmail.com
- **GitHub**: [@milith0kun](https://github.com/milith0kun)

---

⭐ Si este proyecto te fue útil, ¡no olvides darle una estrella en GitHub!