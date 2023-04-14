# Tarea 1 - Despliegue de HAProxy

**Asignatura:** SISTEMAS OPERATIVOS Y SERVICIOS DE RED

**Integrantes:**
- BASANTE BENAVIDES JOHN SEBASTIAN - 2201725
- BONILLA POLO PEDRO ANDRES - 2201855
- PORTOCARRERO CAICEDO FELIPE - 2202129

A continuación de detalla las configuraciones necesarias que se implementaron en Vagrant para dar atención a la "Tarea, Despliegue de HAPROXY", en dónde se desplegan tres  diferentes máquinas virtuales, con diferentes funciones independientes:

1. Un balanceador de carga basado en HAProxy
2. Un servidor web basado en Apache
3. Un servidor web basado en Nginx

El diagrama de la topología es el que se muestra a continuación:

![image](https://user-images.githubusercontent.com/128847/230824475-69e3d59d-97b1-4256-a2d0-116938d24d72.png)

# Descripción general de archivos

- `.gitignore` -> Contiene la lista de archivos que serán ignorados por el repositorio
- `LICENSE` -> Detalla la licencia que rige al presente proyecto
- `README.md` -> Contiene toda la descripción del repositorio
- `Vagrantfile` -> Contiene la configuración del software Vagrant
- `haproxy.cfg` -> Almacena la configuración del balanceador de carga HAProxy

# Detalle del archivo Vagrantfile

- Se especifica el sistema operativo a utilizar. Es decir, Ubuntu Xenial (16.04):

`config.vm.box = "ubuntu/xenial64"`

- Se define la cantidad de memoria RAM y procesadores de cada máquina virtual:

```
vb.customize [
  'modifyvm', :id,
  '--natdnshostresolver1', 'on',
  '--memory', '512',
  '--cpus', '2'
]
```
- Se indica la dirección IP del primer servidor web:

`web1.vm.network :private_network, ip: "192.168.33.11"`

- Se establece los comandos a ejecutar posteriormente a la creación del segundo servidor web:

```
web2.vm.provision "shell", inline: <<-SHELL
  hostname web2
  echo "127.0.1.1  web2" >> /etc/hosts
  apt-get update
  apt-get install -y nginx
  echo "Hello world" > /var/www/html/index.html
SHELL
```

- Se copia el archivo de configuración del HAProxy hacia la MV:

`hap.vm.provision "file", source: "haproxy.cfg", destination: "/tmp/haproxy.cfg"`

# Detalle del archivo haproxy.cfg

- Se describe la lista de servidores web entre los cuales se puede distribuir el tráfico entrante:

```
backend web_servers
  server web1 192.168.33.11:80
  server web2 192.168.33.12:80
```

- Se configura el puerto por el cual se recibe el tráfico de la lista de servidores web:

```
frontend http_frontend
  bind *:80
  default_backend web_servers
```
