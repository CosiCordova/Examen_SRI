# Examen_SRI

Contesta a las preguntas, justificandolas, en un README.md

  ##  1. Explica métodos para 'abrir' una consola/shell a un contenedor que se está ejecutando

    
Para abrir una consola a un contenedor que se esta ejecutando en el Visual Studio Code es necesario estar en el apratado docker y con un click derecho seleccionar abrir shell.

En la terminal es necesario utilizar un comando:

     
     docker exec -it nombre_conrtenedor
     

  ## 2. En el contenedor anterior con que opciones tiene que haber sido arrancado para poder interactuar con las entradas y salidas del contenedor

El contenedor anterior debio ser arrancado con las opciones de imagen utilizada, puertos, volumenes y redes 

  ## 3. ¿Cómo sería un fichero docker-compose para que dos contenedores se comuniquen entre si en una red solo de ellos?
    
Para que se comuniquen entre si en una red solo de ellos es necesario crear la red y luego lanzar el docker compose o en su defecto lanzar el docker compose con la creacion de la red en su interior:

    Este seria un docker compose con red externa:

    
    services:
         bind9:
             image: ubuntu/bind9
             container_name: asir_bind9
             ports:
                 - 53:53/tcp
                 - 53:53/udp
             networks:
                 bind9_subnet:
                     ipv4_address: 172.28.5.1
             volumes:
                     - ./conf:/etc/bind
                     - ./zonas:/var/lib/bind
             environment:
                     - TZ=Europe/Paris  
                     services:
     cliente:
         container_name: asir_cliente
         image: alpine
         tty: true
         stdin_open: true
         networks: 
             - bind9_subnet
         dns:
             - 172.28.5.1  
    networks:
         bind9_subnet: 
             external: true
    
    

   ## 4. ¿Qué hay que añadir al fichero anterior para que un contenedor tenga la IP fija?
   
El contenedor anterior ya tiene programada una ip fija, de no querer colocarla se tendria que quitar el apartado ipv4, en donde esta networks.

   ## 5. ¿Que comando de consola puedo usar para saber las ips de los contenedores anteriores? Filtra todo lo que puedas la salida.

Puedes utilizar el siguiente comando:

     docker inspect -q --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -q)

    
   ## 6. ¿Cual es la funcionalidad del apartado "ports" en docker compose?

Sirve para especificar como los puertos de los contenedores se le asignaran al host.
    
   ## 7. ¿Para que sirve el registro CNAME? Pon un ejemplo
    

El CNAME sirve para que un dominio sea el alias de otro, se suele utilizar para añadir subdominios nuevos a uno que ya existe por ejemplo:

Un registro CNAME suele tener la siguiente forma:

 
  www.example.net. CNAME www.example.com.
 

Para que la referencia a través de CNAME funcione, tiene que haber también un registro A o AAAA en el archivo de zona:

 
$TTL 11107
www.example.com.	IN	A		93.184.216.34
www.example.com.	IN	AAAA		2606:2800:220:1:248:1893:25c8:1946
www.example.net.	IN	CNAME		www.example.com.
www.example.org.	IN	CNAME		www.example.com.
 

Ambos registros CNAME remiten al registro A o AAAA. 

El “time to live” se aplica a la zona entera (lo cual se indica con el símbolo del dólar) y es de 11107 segundos, es decir, de algo más de tres horas.

    
  ## 8. ¿Como puedo hacer para que la configuración de un contenedor DNS no se borre si creo otro contenedor?
   
Para esto puedes crear una red personalizada, un volumen para almacenar la configuracion y el servicio que utilizara la red personalizada junto con el volumen.
   
  ## 9. Añade una zona tiendadeelectronica.int en tu docker 
   
   DNS que tenga
        www a la IP 172.16.0.1
        owncloud sea un CNAME de www
        un registro de texto con el contenido "1234ASDF"
        Comprueba que todo funciona con el comando "dig"
        Muestra en los logs que el servicio arranca correctamente
   
Primero creamos el docker-compose:
```
  services:
  bind9:
    image: ubuntu/bind9
    container_name: asir_examen
    ports:
      - 53:53/tcp
      - 53:53/udp
    networks:
      bind9_subnet:
        ipv4_address: 172.16.0.1
    volumes:
      - ./conf:/etc/bind
      - ./zonas:/var/lib/bind
    environment:
      - TZ=Europe/Paris
  networks:
  bind9_subnet: 
    external: true
```
   
Luego configuramos la zona

  ```
  $TTL 38400 ; 10 hours 40 minutes
  @       IN SOA  ns.tiendadeelectronica.int. some.email.address. (
                        20231009  ; serial
                        10800     ; refresh (3 hours)
                        3600      ; retry (1 hour)
                        604800    ; expire (1 week)
                        38400     ; minimum (10 hours 40 minutes)
                        )
  @       IN NS   ns.tiendadeelectronica.int.
  www     IN A    172.16.0.1
  owncloud IN CNAME www
  txt     IN TXT  "1234ASDF"
```

Para crear el contenedor colocamos 
```
  docker-compose up
```
![Imagen no carga](https://github.com/CosiCordova/Examen_SRI/blob/main/Docker_inicial.png)

Para instalar dns
```
 apt install dnsutils
```
Para ver los logs nos dirigimos al docker en "visual studio code", colocamos el apartadop del docker, vamos a click derecho en nustro contenedor y seleccionamos "View Logs"

![Imagen no carga](https://github.com/CosiCordova/Examen_SRI/blob/main/Imagen_logs_docker.png)

Para comprobar que el contenedor va de maravilla ponemos:
```
 dig @10.0.2.15 ns.tiendadeelectronic.int
```

A mi particularmente me da error por un motivo que desconozco, sin embargo adjunto capture igualmente de un intento de consulta.

![Imagen no carga](https://github.com/CosiCordova/Examen_SRI/blob/main/Dig_docker.png)

   ## 10. Realiza el apartado 9 en la máquina virtual con DNS

El apartado 9 en una maquina virtual lleva los mismo pasos, sin embargo los archivos de configuracion se guardan en:
```
 /Ubuntu/etc/bind
```
Mienstras que los de zona se guardan en:
```
 /Ubuntu/var/lib/bind
```
Una vez colocado todo utilizaremos los siguientes comadnos para realizar nuestro contenedor.

```
 systemctl start bind9
```

```
 systemctl status bind9
```

Para pararlo podemos utilizar:
```
 systemctl stop bind9
```

En la siguiente imagen podemos apreciar los logs y como el servidor esta corriendo.
![Imagen no carga](https://github.com/CosiCordova/Examen_SRI/blob/main/Prueba_server_activo.png)

A continuacion adjunto los 3 de dig:

![Imagen no carga](https://github.com/CosiCordova/Examen_SRI/blob/main/Dig_1.png)

![Imagen no carga](https://github.com/CosiCordova/Examen_SRI/blob/main/Dig_2.png)

![Imagen no carga](https://github.com/CosiCordova/Examen_SRI/blob/main/Dig_3.png)