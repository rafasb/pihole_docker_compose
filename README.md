# pihole_docker_compose
Desplegar PiHole usando docker-compose.

Adaptado para evitar el DHCP (delegado en router)

Password web: changeme <-- HAY QUE CAMBIARLO!!

## Prerequisitos

1) Tener instalado docker: https://docs.docker.com/engine/install/ubuntu/
2) Tener instalado docker-compose: https://docs.docker.com/compose/install/
3) Incluir al usuario en el grupo docker (Ubuntu): usermod -a -G docker $USER 

## Intrucciones básicas

Clonar el directorio con `git clone https://github.com/rafasb/pihole_docker_compose.git`

Cambiar el directorio con `cd pihole_docker_compose`

Ejecutar `docker-compose up -d`

Configurar el DHCP del router de casa para que el servidor DNS sea la IP de nuestro host. 

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

