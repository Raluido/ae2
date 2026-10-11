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
raluidovbox@raluidovbox-terminal:~$ docker exec dpl-lab mkdir -p /var/www/apacheDocs
raluidovbox@raluidovbox-terminal:~$ docker exec dpl-lab bash -c 'cp /home/alumno/dpl/ae2/public/template/index.html var/www/apacheDocs/'
cp: cannot stat '/home/alumno/dpl/ae2/public/template/index.html': No such file or directory
raluidovbox@raluidovbox-terminal:~$ docker exec dpl-lab bash -c 'cp /home/alumno/dpl/ae2/public/template/index.html /var/www/apacheDocs/'
cp: cannot stat '/home/alumno/dpl/ae2/public/template/index.html': No such file or directory
raluidovbox@raluidovbox-terminal:~$ docker exec dpl-lab bash -c 'cp /home/alumno/dpl/ae2/public/templates/index.html /var/www/apacheDocs/'
raluidovbox@raluidovbox-terminal:~$ docker exec dpl-lab chmod -R a+rX /var/www/apacheDocs
raluidovbox@raluidovbox-terminal:~$ docker exec dpl-lab ls -l /var/www/apacheDocs
total 8
-rw-r--r-- 1 root root 4326 Oct  8 21:52 index.html
```



## Comprobación

## roblemas encontrados y solución

## Repositorio remoto

git@github.com:Raluido/ae2.git








