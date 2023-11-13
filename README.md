# Examen_SRI

Contesta a las preguntas, justificandolas, en un README.md

  ##  1. Explica métodos para 'abrir' una consola/shell a un contenedor que se está ejecutando

    
    Para abrir una consola a un contenedor que se esta ejecutando en el Visual Studio Code es necesario estar en el apratado docker y con un click derecho seleccionar abrir shell.

    En la terminal es necesario utilizar un comando:

    ```sh
     docker exec -it nombre_conrtenedor
    ```

  ## 2. En el contenedor anterior con que opciones tiene que haber sido arrancado para poder interactuar con las entradas y salidas del contenedor

    El contenedor anterior debio ser arrancado con las opciones de imagen utilizada, puertos, volumenes y redes 

  ## 3. ¿Cómo sería un fichero docker-compose para que dos contenedores se comuniquen entre si en una red solo de ellos?
    
    
   ## 4. ¿Qué hay que añadir al fichero anterior para que un contenedor tenga la IP fija?
   
   ## 5. ¿Que comando de consola puedo usar para saber las ips de los contenedores anteriores? Filtra todo lo que puedas la salida.
    
   ## 6. ¿Cual es la funcionalidad del apartado "ports" en docker compose?
    
   ## 7. ¿Para que sirve el registro CNAME? Pon un ejemplo
    
    
   ## 8. ¿Como puedo hacer para que la configuración de un contenedor DNS no se borre si creo otro contenedor?
   
   
   ## 9. Añade una zona tiendadeelectronica.int en tu docker DNS que tenga
        www a la IP 172.16.0.1
        owncloud sea un CNAME de www
        un registro de texto con el contenido "1234ASDF"
        Comprueba que todo funciona con el comando "dig"
        Muestra en los logs que el servicio arranca correctamente
   
   
   ## 10. Realiza el apartado 9 en la máquina virtual con DNS
