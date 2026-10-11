Repositorio de práctica del la creación de una página web estático con 

## Índice

- [Entorno e instalación](#entorno-e-instalacion)
- [Configuración](#configuracion)
- [Comprobación](#comprobacion)
- [Problemas encontrados y solución](#problemas-encontrados-y-solucion)
- [Repositorio remoto](#repositorio-remoto)

## Entorno e instalación
Paso 1
Creamos la carpeta de nuestro proyecto y generamos los ficheros de Zensical.
Paso 2
Accedemos a la carpeta del proyecto, generamos el repositorio con gh y la añadimos como origen.
Paso 3
Instalamos los paquetes necesarios con apt, como python3, nginx, venv y nginx
Añadimos el documento de texto "requirements" con las versiones de gunicorn y flask que necesitamos que sean instaladas para que sean instaladas con pip a través de python3
Paso 4
Creamos nuestros ficheros de la web
Commiteamos los nuevos ficheros con la web estática y el programa de python para renderizar el html
Paso 5
Creamos nuestra web estática dentro de la carpeta web y hacemos una copia dentro de "/var/www" para poder cargarla a través con nginx con los permisos adecuados
Paso 6
Arrancamos Gunicorn en foreground. Dado que docker no utiliza systemctl tenemos que lanzarlo en primer plano
Para facilitarnos los comandos accedemos directamente al terminal de dock en vez de hacer un docker exec + comandos
Verificamos con curl que gunicorn responde a http://127.0.0.1:8000. Si bien no podemos acceder desde aquí a la url porque docker sólo está redireccionando el puerto 80. En nuestro caso desde el 82 al 80, por tanto, http://127.0.0.1:82
Paso 7
Editamos el fichero de configuración nginx para que redirija el puerto 80 hacia nuestra app de python renderizando el html
Paso 8
Activamos la configuración de nginx y lanzamos el servidor





## Configuración
Paso 1
```bash
alumno@37016ff2f098:~/dpl$ uv init
alumno@37016ff2f098:~/dpl$ uv add --dev zensical
alumno@37016ff2f098:~/dpl$ uv run zensical
```
Paso 2
```bash
alumno@37016ff2f098:~/dpl$ gh repo create Raluido/ae2 --public
✓ Created repository Raluido/ae2 on GitHub
alumno@37016ff2f098:~/dpl$ git remote add origin git@github.com:Raluido/ae2.git
alumno@37016ff2f098:~/dpl$ git add .
alumno@37016ff2f098:~/dpl$ git commit -m "Primer commit con los ficheros de zensical"
[main (root-commit) a1b2c3d] Primer commit con los ficheros de zensical
 4 files changed, 25 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 README.md
 create mode 100644 pyproject.toml
 create mode 100644 uv.lock
alumno@37016ff2f098:~/dpl$ git push -u origin master
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 4 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (6/6), 620 bytes | 620.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:Raluido/ae2.git
 * [new branch]      master -> master
branch 'master' set up to track 'origin/master'.
```

Paso3
```bash
alumno@37016ff2f098:~/dpl/ae2$ git add web/requirements.txt
alumno@37016ff2f098:~/dpl/ae2$ git commit -m "Crear el entorno virtual e instalar dependencias" -m "python3-venv dentro de dpl-lab; venv en .venv/ (ignorado por .gitignore); requirements.txt con flask y gunicorn en versiones orientativas."
[master 40a4c68] Crear el entorno virtual e instalar dependencias
 1 file changed, 2 insertions(+)
 create mode 100644 web/requirements.txt
```

Paso 4
```bash
alumno@37016ff2f098:~/dpl/ae2$ git add web/app.py web/templates/index.html
alumno@37016ff2f098:~/dpl/ae2$ git commit -m "Crear la web estática con estilos integrados" -m "app.py que renderiza el html y templates con el index.html"
[master a8ce819] Crear la web estática con estilos integrados
 2 files changed, 139 insertions(+)
 create mode 100644 web/app.py
 create mode 100644 web/templates/index.html
```

Paso 5
```bash
raluidovbox@raluidovbox-terminal:~$ docker exec dpl-lab mkdir -p /var/www/apacheDocs
raluidovbox@raluidovbox-terminal:~$ docker exec dpl-lab bash -c 'cp -r /home/alumno/dpl/ae2/web/. /var/www/apacheDocs'
```

Paso 6
```bash
alumno@37016ff2f098:~/dpl/ae2/web$ sudo /home/alumno/dpl/ae2/.venv/bin/gunicorn --bind 127.0.0.1:8000 --workers 2 --access-logfile - --error-logfile - app:app | sudo tee /var/log/apacheDocs.log
[sudo] password for alumno: 
[2026-10-11 20:15:11 +0100] [224] [INFO] Starting gunicorn 23.0.0
[2026-10-11 20:15:11 +0100] [224] [INFO] Listening at: http://127.0.0.1:8000 (224)
[2026-10-11 20:15:11 +0100] [224] [INFO] Using worker: sync
[2026-10-11 20:15:11 +0100] [225] [INFO] Booting worker with pid: 225
[2026-10-11 20:15:11 +0100] [226] [INFO] Booting worker with pid: 226


alumno@37016ff2f098:~/dpl/ae2/web$ ps aux | grep gunicorn
root         219  0.0  0.1  12300  6228 pts/2    S+   20:15   0:00 sudo /home/alumno/dpl/ae2/.venv/bin/gunicorn --bind 127.0.0.1:8000 --workers 2 --access-logfile - --error-logfile - app:app
root         221  0.0  0.0  12300  2300 pts/3    Ss+  20:15   0:00 sudo /home/alumno/dpl/ae2/.venv/bin/gunicorn --bind 127.0.0.1:8000 --workers 2 --access-logfile - --error-logfile - app:app
root         224  0.1  0.6  31296 24224 pts/3    S    20:15   0:00 /home/alumno/dpl/ae2/.venv/bin/python3 /home/alumno/dpl/ae2/.venv/bin/gunicorn --bind 127.0.0.1:8000 --workers 2 --access-logfile - --error-logfile - app:app
root         225  0.0  0.7  39076 29560 pts/3    S    20:15   0:00 /home/alumno/dpl/ae2/.venv/bin/python3 /home/alumno/dpl/ae2/.venv/bin/gunicorn --bind 127.0.0.1:8000 --workers 2 --access-logfile - --error-logfile - app:app
root         226  0.0  0.7  39076 29560 pts/3    S    20:15   0:00 /home/alumno/dpl/ae2/.venv/bin/python3 /home/alumno/dpl/ae2/.venv/bin/gunicorn --bind 127.0.0.1:8000 --workers 2 --access-logfile - --error-logfile - app:app
alumno       248  0.0  0.0   4100  2092 pts/5    S+   20:17   0:00 grep gunicorn
alumno@37016ff2f098:~/dpl/ae2/web$ curl -I http://127.0.0.1.8000
curl: (6) Could not resolve host: 127.0.0.1.8000
alumno@37016ff2f098:~/dpl/ae2/web$ curl -I http://127.0.0.1:8000
HTTP/1.1 200 OK
Server: gunicorn
Date: Sun, 11 Oct 2026 19:17:37 GMT
Connection: close
Content-Type: text/html; charset=utf-8
Content-Length: 4325
alumno@37016ff2f098:~/dpl/ae2/web$ tail -n 5 /var/log/apacheDocs.log
127.0.0.1 - - [11/Oct/2026:20:17:37 +0100] "HEAD / HTTP/1.1" 200 0 "-" "curl/8.5.0"
alumno@37016ff2f098:~/dpl/ae2/web$ 

alumno@37016ff2f098:~/dpl/ae2$ git add README.md          
git commit -m "Arrancar Gunicorn en foreground" -m "gunicorn --bind 127.0.0.1:8000 --workers 2 desde el venv; arranque sin systemd con docker exec -d; comprobado con ps y curl directo."
[master 484325f] Arrancar Gunicorn en foreground
 1 file changed, 88 insertions(+)

```

Pasos 8
```bash
alumno@37016ff2f098:~/dpl/ae2$ sudo nginx -s reload
[sudo] password for alumno: 
2026/10/11 20:45:54 [notice] 260#260: signal process started
```

Paso 9
```bash
alumno@37016ff2f098:/$ curl -I http://localhost/   
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Sun, 11 Oct 2026 21:06:54 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 4325
Connection: keep-alive

alumno@37016ff2f098:/$ curl -s http://localhost/ | grep -o "<h1>.*</h1>"
<h1>Guía Básica del Servidor Apache</h1>
alumno@37016ff2f098:/$ tail -n 5 /var/log/apacheDocs.log
alumno@37016ff2f098:/$ tail -n 5 /var/log/nginx/apacheDocs.access.log
::1 - - [11/Oct/2026:22:06:54 +0100] "HEAD / HTTP/1.1" 200 0 "-" "curl/8.5.0"
::1 - - [11/Oct/2026:22:07:14 +0100] "GET / HTTP/1.1" 200 4325 "-" "curl/8.5.0"
::1 - - [11/Oct/2026:22:07:26 +0100] "POST / HTTP/1.1" 405 153 "-" "curl/8.5.0"
::1 - - [11/Oct/2026:22:07:47 +0100] "POST / HTTP/1.1" 405 153 "-" "curl/8.5.0"
::1 - - [11/Oct/2026:22:08:57 +0100] "POST / HTTP/1.1" 405 153 "-" "curl/8.5.0"
alumno@37016ff2f098:/$ 


```


## Comprobación
Se verifica la carga de la web estática desde la direccion http://127.0.0.1:82 apuntando a /var/www/apacheDocs

## Problemas encontrados y solución
He tenido problemas de conexión que finalmente tras cambiar varias veces los puertos pensando en que podrían estar fallando y lanzar gunicorn con la ip 0.0.0.0 apuntando a todas las interfaces, el problema estaba en que había que reiniciar el procedimiento docker con sudo systemctl docker restart para poder cargar la web estática con http://127.0.0.1:82 (puesto que el puerto que configuré en mi docker es 80:82)

## Repositorio remoto

git@github.com:Raluido/ae2.git








