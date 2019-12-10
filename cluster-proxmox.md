# CLUSTER CON PROXMOX
## Objetivos de la práctica
- Crear un cluster con dos nodos.
- Configurar el almacenamiento compartido NFS.
- Máquina Linux en el Nodo 1.
- Migrar esa máquina al nodo 2.

### Configuración de Sistema de almacenamiento NFS
#### Servidor
Instalamos NFS
~~~
apt-get update
apt-get install nfs-kernel-server nfs-common
~~~
Creamos el directorio de NFS y le asignamos los permisos pertinentes.
~~~
mkdir /home/nfs
chown nobody:nogroup /home/nfs
chmod 755 /home/nfs
~~~
Añadimos el cliente al archivo ***/etc/exports***
~~~
/home/nfs-cliente   10.10.1.20(rw,sync,no_subtree_check)
/home/www-cliente   10.10.1.20(rw,sync,no_subtree_check,no_root_squash)
~~~
A continuación debemos reiniciar el servicio para que se guarden los cambios realizados:
~~~
service nfs-kernel-server restart
~~~
#### Cliente
Instalamos el cliente NFS
~~~
apt-get update
apt-get install nfs-common
~~~
Creamos los directorios en los que van los recursos compartidos.
~~~
mkdir /home/nfs-cliente
mkdir /home/www-cliente
~~~
Montamos los directorios para que pertenezcan con los del servidor.
~~~
mount 10.10.1.10:/home/nfs /home/nfs-cliente
mount 10.10.1.10:/home/www /home/www-cliente
~~~
Para comprobar que ha funcionado creamos un archivo en el directorio /home/nfs-cliente y comprobamos en el servidor que el archivo está visible.
### Crear el contenedor PROXMOX, el Cluster, añadimos la máquina al nodo y la migramos.
Descargamos una imagen de una máquina virtual y la guardamos en el directorio compartido. La descargamos de [aquí](download.proxmox.com/images/system).
Creamos un contenedor con la imagen que préviamente hemos descargado:
~~~
pct create 100 /home/nfs-cliente/debian.......tar.gz
~~~
Creamos el cluster:
~~~
pvecm creates cluster_juanfran
~~~
Añadimos el nodo al cluster. Para ello debemos acceder vía ssh al nodo número 2.
~~~
ssh 10.10.1.20 password
pvecm add 10.10.1.10
~~~
Listamos los nodos para comprobar que se ha añadido correctamente.
~~~
pvecm node
~~~
Mostramos el estado del cluster:
~~~
pvecm status
~~~
Migramos la máquina al otro nodo
~~~
pct migrate 100 juanfran2_debian
~~~
Comprobamos que se ha migrado la máquina. Realizamos un *pct list* en el nodo 1 para comprobar que la máquina ya no está. Posteriormente realizamos el mismo paso en el nodo 2 para ver que efectivamente la máquina se ha migrado al nodo número 2.