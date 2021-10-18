# Despliegue de una aplicacion MEAN

En esta practica vamos a configurar la maquina proxy, para que nos redirija hacia la maquina backend, que se encarga de resolver las peteciones, y esta, a su ves, se comunicara con la maquina de base de datos para obtener los datos necesarios que necesite el propio backend, pudiendo asi despleguer la aplicacion web.

## Configurar la MV que hace la funcion de Proxy HTTP

Empezaremos configurando la maquina Proxy, en la cual habra que configurar que escuche por el puerto por defecto, el 80, y redirigir las peticiones a la maquina backend, que estara escuchando en el puerto 3000. Para eso vamos a instalar en la maquina proxy ```nginx```:

```bash
sudo apt update
sudo apt install nginx
```

Luego configuramos nginx para que escuche por el puerto 80:

```bash
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

Podemos comprobar el estado de nginx en cualquier momento con el siguiente comando:

```bash
sudo systemctl status nginx
```

Ahora vamos a dirigirnos a la carpeta ```/etc/nginx/sites-available```, donde vamos a crear una copia del fichero ```default```:

```bash
cd /etc/nginx/sites-available
cp default backend.conf
```

Vamos a cambiar el contenido de backend por lo siguiente:

```c
server {

  listen 80;

  location / {

    proxy_pass http://172.16.12.2:3000

  }

}
```

Una vez creado el fichero vamos a deslinkear el fichero default y linkear este nuevo backend.conf:

```bash
sudo unlink /etc/nginx/sites-available/default
sudo ln -s /etc/nginx/sites-available/backend.conf /etc/nginx/sites-enabled
```

Una vez linkeada la nueva configuracion vamos a resetear nginx:

```bash
sudo service nginx configtest
sudo service nginx restart
```