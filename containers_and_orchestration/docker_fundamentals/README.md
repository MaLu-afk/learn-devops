# 1. Fundamentos de Docker

Docker es una herramienta esencial para gestionar y publicar soluciones de software mediante contenedores. La esencia y el punto fuerte de Docker radica en su capacidad de aislar aplicaciones, asegurando que estas funcionen de manera consistente en diferentes sistemas operativos.  

**Características:**

- Empaqueta toda una solución de software.
- Incluye dependencias y configuraciones. 

**¿Cómo funciona?**

![image.png](image.png)

Esta es la arquitectura básica de Docker.

#### 1. Docker CLI (Capa externa):

+ Se refiere a la interfaz de línea de comandos.
+ Como usuario, se ejecuta los comandos (`docker built`, `docker run`, `docker ps`, etc.).

#### 2. REST API (Interfaz de comunicación):

+ Es la capa que sirve como un traductor entre el CLI y el corazón de Docker en solicitudes HTTP.

#### 3. Docker Daemon:

+ Es el motor central que gestiona todo.
+ Recibe la solicitudes desde la REST API y actúa, por ejemplo, crear contenedores, descargar imágenes, etc.

#### 4. Recursos que administra el daemon:

    1. Container: Es el entorno aislado donde se ejecutan las aplicaciones.
    2. Image: Es la plantilla de solo lectura que define qué software hay en un contenedor.
    3. Network: Cómo se comunican los contenedores entre sí. 
    4. Volume: Es un mecanismo que guarda datos. Es decir, se conserva los datos almacenados dentro de un contenedor incluso después de que el contenedor se detiene, elimina o reinicia.

**¿Cuál es la diferencia entre una máquina virtual (VM) y un contenedor?**

|Característica|Contenedor Docker|Máquina Virtual|
|-|-|-|
|Virtualiza|Sistema operativo (a nivel de aplicación)|El hardware completo (a nivel del sistema)|
|Ideal para|Microservicios, CI/CD, entornos ligeros|Simular entornos completos (redes con routers, firewalls, etc.), probar múltiples SO|
|Hardware|Sin hardware|Hardware real|
|Necesita|Control de versiones|Mantenimiento|
|Requiere|Sin software adicional|Software adicional|



# 2. Creación de Imágenes con Docker

![image.png](image-1.png)

Lo que sigues es ejecutar el siguiente comando para crear la imagen:

`docker build .`

Ahora verifiquemos la imagen creada:

`docker images`

Como se pudo notar, no tiene un nombre adecuado. Ahora, para eliminar forzadamente se hace lo siguiente:
`docker mri -f [IMAGE ID]`

Ahora, sí ejecutamos el comando asignando un nombre y etiqueta:
`docker build -t sitioweb:latest .`

Vamos a crear el contenedor con:
`docker run -it --rm -d -p 8080:80 --name web sitioweb`
    
    Este comando, hace lo siguiente:
    + `docker run`: este comando principal se usa para crear y ejecutar un contenedor desde la imagen de Docker.
    + `-it`: (interactivo) permite ver los logs dentro de la terminal.
    + `--rm`: eliminar las versiones anteriores del contendor.
    + `-d`: el contenedor se ejecuta en un segundo plano listo para usarse posteriomente.
    + `-p`: mapea el puerto del contendor con el puerto de la aplicación para exponerla.
    + `-- name`: nombre del contenedor.

Puede pasar que el puerto que se designe esté ocupado. Para verificar y liberar el puerto, hacemos lo siguiente:
1. ejecutar `netstat -aon | findstr :8080`: 
- `netstat`: muestra el estado de las conexiones de red en la computadora.
- `-a`: Muestra todas las conexiones activas o los puertos en escucha.
- `-o`: Muestra el PID (Process ID) del programa que está usando el puerto.
- `-n`: Muestra direcciones y puertos en formato numérico. 
- `|`: el símbolo pipe envía la salida de `netstat -aon` como entrada de `findstr`.
- `findstr :8080`: este comando solo busca las líneas que contiene el texto `:8080`, es decir, todas las conexiones o puertos en uso que incluyen el puerto 8080. 

2. ejecutar `tasklist | findstr [PID]`:
- `tasklist`: muestra todos los procesos actualmente en ejecución.
- `findstr [PID]`: este comando filtra la salida, mostrando solo las líneas que contengan el PID.

3. ejutar `taskkill /PID [PID] /F`: Sirve para forzar el cierre de un proceso específico.

Continuando con el aprendizaje de nuevos comandos, ahora si queremos buscar las imágenes pero en base a los tags, lo podemos hacer como sigue:
`docker images --filter=reference='*:1.0'`

Si queremmos visualizar el images ID más extendida real, usamos lo siguiente:
`docker images --no-trunc`

Para cambiar el tag de una imagen escribimos como sigue:
`docker images tag sitioweb:latest amin/sitioweb:latest`

Para eliminar una imagen se usa:
`docker rmi amin/sitioweb:latest`

O eliminarlo forzadamente con el id de la imagen:
`docker rmi -f [IMAGE ID]`

Cabe mencionar que los nombres de los contenedores no debe repetirse.

Si queremos visulizar todos los contenederes, incluso los que no se ejecuten, se usa:
`docker ps -a`

Ahora, si queremos visualizar la estadística del desempeño de nuestro computador al ejecutar un contenedor desde la línea de comandos, se usa:
``docker stats`

# 3. Volúmenes y Redes  de Docker

Los volúmenes son espacios compartidos entre una máquina local y un contenedor de Docker.

Por ejemplo, se puede ejecutar el siguiente comando para permitir esto:
` docker run -it --rm -d -p 8080:80 -v ./sitio:/user/share/nginx/html/sitio --name web nginx`

Lo que hace este comando, en especial la parte de `-v ./sitio:/user/share/nginx/html/sitio`, monta el directorio local `./sitio` sobre el directorio `/user/share/nginx/html/sitio`. Los cambios que se hagan de manera local, se mostrarán en el contendor. Por ejemplo, si en el archivo linktree.html se cambia la imagen preliminar por una que se encuentra en la carpeta assets, se verá los cambios realizados. Asimismo, si se eliminar un archivo desde el CLI de un contenedor Docker se evidenciará ese cambio en la máquina local.

Ahora bien, podemos hacer lo mismo si se declara desde el dockerfile, el cual se ve así:
`VOLUME ["/sitio", "/usr/share/nginx/html"]`, este comando es susceptible a cambios. Mientras que el siguiente comando: `COPY /sitio /usr/share/nginx/html`, hace que la información se almacene de forma permanente en la imagen.