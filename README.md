# pihole_docker_compose
Desplegar PiHole usando docker-compose.

Adaptado para evitar el DHCP (delegado en router)

Password web: changeme <-- HAY QUE CAMBIARLO!!

## Prerequisitos del host
En el equipo donde vamos a instalar el servicio PiHole.
1) Tener instalado docker: https://docs.docker.com/engine/install/ubuntu/
2) Tener instalado docker-compose: https://docs.docker.com/compose/install/
3) Incluir al usuario en el grupo docker (Ubuntu): usermod -a -G docker $USER 
4) El usuario debe disponer de permisos para usar `sudo`

## Intrucciones básicas
1) Clonar el directorio con `git clone https://github.com/rafasb/pihole_docker_compose.git`
2) Cambiar el directorio con `cd pihole_docker_compose`
3) Ejecutar `docker-compose up -d`
4) Configurar el DHCP del router de casa para que el servidor DNS sea la IP de nuestro host 

### Para equipos Ubuntu con DNSMASQ o SYSTEMD-RESOLVED instalado:

Hay equipos que tienen ocupado el puerto 53 debido a que tienen instalado algún servicio como dnsmasq o el servicio por defecto systemd-resolved.

Verificamos si tenemos el servicio instalado y operativo (prueba también con dnsmaq en lugar de systemd-resolved) para posteriormente deshabilitarlo y detenerlo:

```bash
sudo systemctl status systemd-resolved 
sudo systemctl disable systemd-resolved
sudo systemctl stop systemd-resolved
```

Ahora tenemos que hacer la configuración correcta del servicio DNS de nuestro host

```bash
sudo rm /etc/resolv.conf
sudo touch /etc/resolv.conf
sudo cat <<EOF > /etc/resolv.conf
nameserver 127.0.0.1
options edns0 trust-ad
search lan
EOF
```

## Intrucciones complementarias

Podemos hacer uso de los comando de PiHole desde el host usando `docker-compose exec pihole <comando>`

1) Preparamos un alias para facilitar el trabajo. 
```bash
sudo bash -c "echo alias pihole=\'docker-compose exec pihole pihole\' >>  $HOME/.bashrc"
source $HOME/.bashrc
```

2) Cambio del password de servicio web (recomiendo realizar el cambio en el fichero `docker-compose.yml` con la variable de entorno WEBPASSWORD)
```bash
pihole -a -p
``` 

3) Otros comandos:

a) Estadísticas:

| COMANDO | USO |
| --- | --- |
| pihole status | Visualiza estado |
| pihole -t tail log | Streaming de logs |
| pihole -c | Estadísticas de uso en tiempo real |


b) Gestión de listas negras y listas blancas:

| COMANDO | USO |
| --- | --- |
| pihole -w -l | Dominios en lista blanca |
| pihole -w *midominio.com* | Añadir *midominio.com* a lista blanca |
| pihole -w -d *midominio.com* | Eliminar *midominio.com* de la lista blanca |
| pihole -b -l | Dominios en lista negra |
| pihole -b *midominio.com* | Añadir *midominio.com* a lista negra |
| pihole -b -d *midominio.com* | Eliminar *midominio.com* de la lista negra |

c) Otros

| COMANDO | USO |
| --- | --- |
| pihole -l off | Apagar las preguntas |
| pihole -l on | Encender las preguntas |
| pihole enable | Apagar |
| pihole disable | Encender |
| pihole disable 10m | Apagar 10 minutos |
| pihole disable 60s | Apagar 60 segundos |

#### Referencias

https://www.smarthomebeginner.com/pi-hole-setup-guide/

https://hub.docker.com/r/pihole/pihole
