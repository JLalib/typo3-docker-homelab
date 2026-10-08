# 🐳 TYPO3 Docker Homelab

[![GitHub Stars](https://img.shields.io/github/stars/martin-helmich/docker-typo3?style=for-the-badge&logo=github)](https://github.com/martin-helmich/docker-typo3)
[![Docker Pulls](https://img.shields.io/docker/pulls/martinhelmich/typo3?style=for-the-badge&logo=docker)](https://hub.docker.com/r/martinhelmich/typo3)
[![License](https://img.shields.io/badge/License-GPL--3.0-blue?style=for-the-badge)](https://www.gnu.org/licenses/gpl-3.0.html)

## 📋 Descripción general

TYPO3 en Docker: CMS enterprise autohospedado con PHP 7.4-8.2, soporte flexible para PostgreSQL/MySQL/MariaDB, múltiples versiones LTS (6.2-13.4), backend robusto para editores, control de acceso granular, sistema de extensiones potente, multi-idioma nativo y templating flexible con Fluid/TypoScript. Imagen Docker oficial `martinhelmich/typo3` preconfigurada para desarrollo, testing y evaluación en homelab. **117+ GitHub stars**, GPL-3.0 open source, multi-arquitectura.

> ⚠️ **Importante**: La imagen `martin-helmich` es para **desarrollo/testing**. NO es production-ready out-of-the-box. Lee la [documentación oficial](https://docs.typo3.org/) antes de usar en producción.

## ✨ Características principales

- 🔢 **Multi-versión LTS**: 6.2, 7.6, 8.7, 9.5, 10.4, 11.5, 12.4, 13.4 LTS (latest)
- 🗄️ **Base de datos flexible**: PostgreSQL 10+, MySQL 5.7+, MariaDB 10.3+, SQLite
- 🎛️ **Backend robusto**: Editor seguro, control de acceso granular, flujos de publicación, historial de versiones
- 🧩 **Extensiones potentes**: Sistema de plugins, community extensions, customización infinita
- 🌍 **Multi-idioma nativo**: Traducción automática, fallback chains, documentación en español
- 🎨 **Templating flexible**: Fluid templates, TypoScript, YAML, libertad de diseño total
- 🔓 **Sin vendor lock-in**: GPL-3.0 open source, control total de tus datos
- 🧪 **Orientado a desarrollo/testing**: Ideal para homelab, learning, evaluation pre-producción

## 📋 Requisitos del sistema

- **Docker y Docker Compose**: versión reciente
- **PHP 7.4-8.2**: incluido dentro de la imagen Docker
- **Base de datos**: PostgreSQL 10+, MySQL 5.7+, MariaDB 10.3+
- **RAM**: mínimo 2 GB; recomendado 4+ GB
- **Almacenamiento**: 10-50 GB (contenido, extensiones, uploads, typo3temp)
- **Puertos**: 80 (web), 443 (HTTPS opcional), 3306 (MySQL/MariaDB) o 5432 (PostgreSQL)

## 🐳 Instalación

### Paso 1: Crear `docker-compose.yml`

```yaml
version: '3.8'

services:
  typo3:
    image: martinhelmich/typo3:13
    ports:
      - "80:80"
    environment:
      TYPO3_DB_HOST: postgres
      TYPO3_DB_PORT: 5432
      TYPO3_DB_NAME: typo3
      TYPO3_DB_USER: typo3
      TYPO3_DB_PASSWORD: contraseña_db_segura
      TYPO3_ADMIN_USER: admin
      TYPO3_ADMIN_PASSWORD: contraseña_admin_segura
      TZ: Europe/Madrid
    depends_on:
      - postgres
    volumes:
      - ./fileadmin:/var/www/html/fileadmin
      - ./typo3conf:/var/www/html/typo3conf
      - ./typo3temp:/var/www/html/typo3temp
    networks:
      - typo3-net

  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: typo3
      POSTGRES_USER: typo3
      POSTGRES_PASSWORD: contraseña_db_segura
      TZ: Europe/Madrid
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - typo3-net

volumes:
  postgres_data:

networks:
  typo3-net:
```

### Paso 2: Lanzar Docker Compose

```bash
docker compose up -d
```

### Paso 3: Acceder a la interfaz

La aplicación estará disponible en **http://localhost** en ~60 segundos.

- **Frontend**: http://localhost
- **Backend**: http://localhost/typo3 (user: `admin`, pass: la configurada en `TYPO3_ADMIN_PASSWORD`)

## ⚙️ Configuración

1. **Cambiar versión de TYPO3**: Modifica la etiqueta de la imagen en `docker-compose.yml`:
   - `martinhelmich/typo3:6.2` | `:7.6` | `:8.7` | `:9.5` | `:10.4` | `:11.5` | `:12.4` | `:13` (latest 13.4 LTS)

2. **Usar MySQL/MariaDB en lugar de PostgreSQL**: Reemplaza el servicio `postgres` por:
   ```yaml
   mysql:
     image: mysql:8.0
     environment:
       MYSQL_ROOT_PASSWORD: root
       MYSQL_DATABASE: typo3
       MYSQL_USER: typo3
       MYSQL_PASSWORD: contraseña_db
       TZ: Europe/Madrid
     volumes:
       - mysql_data:/var/lib/mysql
   ```
   Y actualiza `TYPO3_DB_HOST: mysql`, `TYPO3_DB_PORT: 3306` en el servicio typo3.

3. **Variables de entorno principales**:
   - `TYPO3_DB_HOST` / `TYPO3_DB_PORT` / `TYPO3_DB_NAME` / `TYPO3_DB_USER` / `TYPO3_DB_PASSWORD`
   - `TYPO3_ADMIN_USER` / `TYPO3_ADMIN_PASSWORD`
   - `TZ` (zona horaria, ej: `Europe/Madrid`)

4. **Persistencia de datos**: Los volúmenes `./fileadmin`, `./typo3conf`, `./typo3temp` mapean contenido, configuración y cache local.

5. **HTTPS con Let's Encrypt**: Usa Caddy o Nginx reverse proxy con certificados automáticos delante de TYPO3 (puerto 80/443 → 80 contenedor).

## 🚀 Primeros pasos

1. Clona este repositorio o crea el `docker-compose.yml` mostrado arriba
2. Edita las contraseñas en `docker-compose.yml` (usa contraseñas seguras)
3. Ejecuta `docker compose up -d`
4. Espera ~60 segundos a que los contenedores estén healthy
5. Abre http://localhost en tu navegador
6. Accede al backend en http://localhost/typo3 con usuario `admin` y tu contraseña
7. Explora el backend: crea una página de prueba, configura extensiones según necesidad

## 💡 Casos de uso

- 🎓 **Aprendizaje**: Familiarízate con TYPO3 en homelab antes de producción
- 🧪 **Testing**: Prueba extensiones, configuración, upgrades en entorno seguro
- 💻 **Desarrollo**: Build sitios TYPO3 localmente, deploy después a producción
- 🔍 **Evaluación**: ¿Es TYPO3 para mi proyecto? Prueba diferentes versiones LTS
- 💾 **Backup/Restore**: Test flujos de backup en homelab antes de producción
- 👥 **Capacitación**: Entrena equipo editorial en TYPO3 sin riesgo

## 🔒 Acceso remoto seguro

Para exponer TYPO3 de forma segura a internet:

1. **Reverse Proxy**: Despliega Caddy o Nginx Proxy Manager delante del contenedor
2. **Certificados TLS**: Configura Let's Encrypt automático (Caddy lo hace nativamente)
3. **Autenticación adicional**: Añade Authelia/Keycloak/OAuth2 Proxy para 2FA
4. **Red restringida**: Usa Tailscale/ZeroTier/VPN para acceso solo a dispositivos autorizados
5. **Firewall**: Limita puertos 80/443 solo a IPs de confianza si no usas VPN

## 🛠️ Gestión y mantenimiento

### Ver estado y logs
```bash
docker compose ps
docker compose logs -f typo3
```

### Detener contenedores
```bash
docker compose down
```

### Actualizar TYPO3
```bash
docker compose pull
docker compose up -d
```
> ⚠️ **Cuidado**: Haz backup antes de actualizar. Lee las [release notes](https://typo3.org/download/release-notes/) de la versión objetivo.

### Backup de datos
```bash
# Backup base de datos PostgreSQL
docker exec typo3_postgres_1 pg_dump -U typo3 typo3 > typo3_backup.sql

# Backup archivos (fileadmin, typo3conf, typo3temp)
tar -czf fileadmin_backup.tar.gz ./fileadmin/
tar -czf typo3conf_backup.tar.gz ./typo3conf/
tar -czf typo3temp_backup.tar.gz ./typo3temp/
```

### Monitoreo
- **Páginas creadas**: Backend → Pages
- **Usuarios**: Backend → List → Users
- **Extensiones activas**: Backend → Admin Tools → Extensions
- **Logs**: Backend → System → Log + `docker compose logs`

## 📝 Licencia

Este proyecto usa la imagen Docker `martinhelmich/typo3` licenciada bajo **GPL-3.0**. TYPO3 CMS es software libre bajo licencia GPL-2.0-or-later. Consulta [LICENSE](https://www.gnu.org/licenses/gpl-3.0.html) para detalles.

---

> 📖 **Guía completa**: [Cómo instalar TYPO3 en Docker: CMS Enterprise Autohospedado](https://genbyte.blogspot.com/2026/10/como-instalar-typo3-en-docker-cms.html)  
> 🎥 **Video tutorial**: [Canal GENBYTE en YouTube](https://www.youtube.com/@GENBYTE)  
> ⭐ **Imagen Docker**: [martinhelmich/typo3 en Docker Hub](https://hub.docker.com/r/martinhelmich/typo3)  
> 🐙 **Repo upstream**: [martin-helmich/docker-typo3](https://github.com/martin-helmich/docker-typo3)