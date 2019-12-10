# Monitorización de servicios.
## Operaciones usando "systemctl".
#### Activar el servicio sshd.
~~~
sudo systemctl enable sshd
~~~

#### Desactivar el servicio sshd.
~~~
sudo systemctl disable sshd
~~~

#### Iniciar el servicio sshd.
~~~
sudo systemctl start sshd
~~~

#### Parar el servicio sshd.
~~~
sudo systemctl stop sshd
~~~

#### Recargar el servicio sshd.
~~~
sudo systemctl restart sshd
~~~

### Journalctl para monitorizar los archivos log.
Con esta herramienta se puede acceder a los registros del sistema, también conocidos como archivos log. La forma más básica de ver todos los registros del sistema sin filtrar es:
~~~
journalctl
~~~
Si queremos ver alguno en concreto debemos especificarlo con:
~~~
journalctl > ejemplo.log
~~~
Además, journalctl cuenta con numerosos parámetros para hacer más efectiva y precisa la busqueda o seguimiento de un archivo log.

### Utilizar netstat para comprobar el puerto de escucha del servidor.
Netstat es una herramienta de línea de comandos que muestra un listado de las conexiones activas de un ordenador, tanto entrantes como salientes. Un ejemplo de su uso es el siguiente:
~~~
netstat -s -p 8080
~~~

### Cambiar el puerto de algún servicio.
En ocasiones es aconsejable cambiar el puerto de algunos servicios, como es el caso del puerto 22, perteneciente al servicio SSH. En caso de querer cambiar el puerto, debemos acceder al fichero de configuración y elegir el puerto que queramos. Debemos asegurarnos de que ese puerto no está siendo usado por otro servicio.
~~~
PORT 3344
~~~

### Crear una lista blanca de usuarios permitidos.
Se puede crear una lista blanca para permitir la conexión tanto por dirección IP como por usuarios. Si queremos filtrar por dirección ip debemos usar el comando ***iptables*** seguido de una serie de parámetros. Un ejemplo sería el siguiente:
~~~
iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
~~~
Filtrar por usuarios es más sencillo de realizar, ya que únicamente debemos editar el archivo de configuración de sshd modificando la siguiente línea:
~~~
AllowUsers nombre_usuario
~~~

### Permitir acceso a root solo con clave instalada en el servidor.
Hay que acceder al archivo de configuración de sshd, llamado ***sshd_config***. Una vez dentro modificamos la siguiente línea:
~~~
PermitRootLogin no
~~~

## Ejercicios sobre monitorización de servicios.
1. Activar el registro de logs para el servicio sshd.
   ~~~
   journalctl -t sshd
   ~~~
2. Probar conexión varias veces, con fallo y de manera exitosa.
   ~~~
   failed password for juanfran_debian from 10.10.1.34 por 3344 ssh2
   ~~~
3. Mostrar los últimos 10 mensajes generados por el servicio sshd y, exportarlos en formato json.
   ~~~
   journalctl -u ssh -10 > ./mensajes.json
   ~~~
4. Configurar journalctl para hacer persistentes los registros, pero que no ocupen más de 100MB de espacio en disco.
   Hay que modificar en el archivo de configuración la siguiente línea:
   ~~~
   SystemMaxFileSize=100M
   ~~~
5. Mostrar los registros autorizados de acceso remoto de la últimos 5 usuarios.
   ~~~
   journalctl -y sshd -b -1
   journalctl -y sshd -b -2
   journalctl -y sshd -b -3
   journalctl -y sshd -b -4
   journalctl -y sshd -b -5
   ~~~
6. Revisar los intentos con error de acceso generados por el servicio sshd en las últimas 24 horas.
   ~~~
   journalctl --until 24:00
   ~~~