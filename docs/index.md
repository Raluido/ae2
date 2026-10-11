# Manual Práctico de Nginx para Administradores de Sistemas

Este manual condensa los conceptos fundamentales, configuraciones esenciales y comandos críticos para administrar un servidor web y proxy inverso **Nginx** en entornos de desarrollo y producción.

---

## 1. Arquitectura y Conceptos Clave

A diferencia de servidores tradicionales como Apache, Nginx utiliza una **arquitectura orientada a eventos y no bloqueante**. 
* **Master Process:** Gestiona la configuración, abre puertos y coordina los procesos hijo.
* **Worker Processes:** Procesos independientes que atienden las conexiones reales. Se recomienda configurar un *worker* por cada núcleo de CPU disponible.

---

## 2. Estructura de Directorios Estándar

En sistemas basados en Debian/Ubuntu, la configuración se organiza de la siguiente manera:

* `/etc/nginx/nginx.conf`: Archivo de configuración maestro del sistema.
* `/etc/nginx/sites-available/`: Directorio que almacena los archivos de configuración de cada sitio web (activos o inactivos).
* `/etc/nginx/sites-enabled/`: Directorio que contiene enlaces simbólicos (accesos directos) a los archivos de `sites-available`. Nginx solo lee los sitios que están aquí.
* `/var/log/nginx/`: Directorio por defecto para los registros de tráfico (`access.log`) y fallos (`error.log`).

---

## 3. Comandos de Control Esenciales

Todos los comandos de administración deben ejecutarse con privilegios de administrador (`sudo`):

### Verificar la Sintaxis de Configuración
Antes de reiniciar o recargar el servidor, comprueba que no haya errores tipográficos ni falten puntos y comas (`;`):
```bash
sudo nginx -t
```

### Aplicar Cambios sin Cortar Conexiones (Recomendado)
Vuelve a leer los archivos de configuración de forma limpia, manteniendo las visitas de los usuarios activos:
```bash
sudo nginx -s reload
# O mediante el sistema:
sudo service nginx reload
```

### Control del Servicio Completo
```bash
sudo service nginx start     # Iniciar el servidor
sudo service nginx stop      # Detener el servidor
sudo service nginx restart   # Forzar un reinicio completo
sudo service nginx status    # Verificar si está activo y ver errores rápidos
```

---

## 4. Bloques de Configuración Comunes

### A. Servidor Web Estático Básica
Configuración para servir archivos HTML, CSS y JS desde un directorio local.

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name misitioweb.com ://misitioweb.com;

    root /var/www/misitioweb;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### B. Proxy Inverso (Conexión a Gunicorn / Node.js)
Configuración para redirigir el tráfico del puerto web exterior (`80`) hacia una aplicación que corre internamente en otro puerto (ej. `8000`).

```nginx
server {
    listen 80;
    server_name ://misitioweb.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        
        # Mapeo de cabeceras críticas para la aplicación interna
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

---

## 5. Diagnóstico y Resolución de Problemas (Troubleshooting)

Cuando la web no carga y el comando `nginx -t` da una validación correcta, los errores suelen registrarse en los logs internos.

### Inspeccionar Errores en Tiempo Real
Abre la terminal y ejecuta este comando mientras intentas recargar la web en tu navegador:
```bash
tail -f /var/log/nginx/error.log
```

### Errores Comunes de Conexión

1. **`502 Bad Gateway` (connect() failed - 111: Connection refused)**
   * *Causa:* Nginx funciona bien, pero el servicio trasero (Gunicorn/Python/Node) está apagado o escucha en un puerto diferente al configurado en el `proxy_pass`.
   * *Solución:* Verifica el puerto con `ss -tlnp` y levanta el servicio interno.

2. **`403 Forbidden`**
   * *Causa:* Nginx no tiene permisos de lectura sobre la carpeta del parámetro `root` o falta el archivo `index.html`.
   * *Solución:* Ajusta los permisos con `sudo chown -R www-data:www-data /var/www/ruta`.

3. **`404 Not Found`**
   * *Causa:* La ruta solicitada en el navegador no existe en el disco duro o la directiva `try_files` está mal estructurada.
