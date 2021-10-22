# Practica 3: Despliegue de una aplicación MEAN en el IaaS de la ULL (2)

## Desplegar un segundo Backend

Para realizar esta terea tenemos que crear la segunda maquina backend, la cual la crearemos con los mismos pasos que en la practica 1. Añadiendo las mismas interfaces.

![](https://github.com/EindhovenLion99/SyTW/blob/master/Practica3/img/Cap1.jpg?raw=true)

Ya tendriamos la segunda maquina creada con sus interfaces configuradas.

## Instalar NFS y descargar BITNAMI

Al igual que hicimos en la practica 2, vamos a configurar NFS para conectarnos a la base de datos, asi como descargar el repositorio de la aplicacion BITNAMI.

## Configuracion del Proxy

Ahora que hemos configurado el backend clonado, vamos a configurar el Proxy, para que pueda mandar las peticiones a cualquiera de los dos backends. Para esto entramos en el fichero ```/etc/nginx/nginx.conf``` y añadimos los upstreams:

```bash
http {
  upstreams backends {
    server 172.16.12.2:3000;
    server 172.16.12.3:3000;
  }
}
```

Ahora vamos a configurar el ficher0 ```/etc/nginx/sites-available/server.conf```, el cual esta linkeado con ```/etc/nginx/sites-enable/server.conf```.

```bash
location 7 {
  proxy_pass http://backends;
}
```

http://backends; hace referencia al upstream que creamos anteriormente.

A continuacion reiniciamos el servicio de nginx, para cargar los nuevos cambios.

```bash
sudo service nginx configtest
sudo service nginx restart
sudo systemctl status nginx
```

Para ver que todo esta funcional vamos a entrar en el navegador a la direccion IP del balanceador de carga para que nos redirija a los backends. Podemos acceder a los logs del Proxy en /var/log/nginx/access.log y los de los backends lo podemos ver con sudo pm2 logs.

![Logs del Proxy](https://github.com/EindhovenLion99/SyTW/blob/master/Practica3/img/1159edce-3dac-49e1-82de-91e0726ceb78.jpg?raw=true)

![Logs del Backend clonado](https://github.com/EindhovenLion99/SyTW/blob/master/Practica3/img/a8b6deff-59bf-4636-ad22-7d41b21fbe59.jpg?raw=true)

![Logs del backend](https://github.com/EindhovenLion99/SyTW/blob/master/Practica3/img/d1c1f68d-2cca-4ab5-b773-84ebc4115fe1.jpg?raw=true)

Podemos ver que llegan peticiones a ambos backends despues de recargar la pagina varias veces. Aunque aparezca un error en el primer backend todo esta funcional, ya que es un error del pasado.

Con esto ya tendremos configurado el proxy y el backend clonado, para que tambien reciba peticiones del balanceador de carga.