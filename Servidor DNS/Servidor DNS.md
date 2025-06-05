# Instalación y configuración del servidor DNS (guía paso a paso)

A continuación, se detallan los pasos seguidos para instalar y configurar un servidor DNS de forma estructurada y funcional.

[← Volver al índice principal](../Readme.md "Ir al contenido principal")

## Antes de configurar el servidor DNS

Importante: Antes de instalar el servidor DNS, es necesario haber instalado previamente el sistema operativo Ubuntu Server, si aún no lo has hecho, puedes hacerlo siguiendo los pasos descritos en el siguiente enlace:

* [Pasos previos a la instalación y configuración de los servidores](./preview.md "Primeros pasos antes de la instalación")

## Instalación de los servicios necesarios

Antes de comenzar a configurar el servidor DNS, es necesario instalar los servicios necesarios escribiendo el siguiente comando en la terminal:

```bash
sudo apt install bind9 dnsutils
```

## Configuración del servidor DNS

Después de completar la instalación indicados en el paso anterior, procedemos a configurar el servidor DNS. El primer paso es agregar una zona DNS para el dominio que queremos configurar.

Para ello, escribimos el siguiente comando en la terminal para editar el archivo `named.conf.local`:

```bash
sudo vi /etc/bind/named.conf.local
```

Y luego agregamos la siguiente línea al archivo:

```bash
zone "daniel-alarmas.com" {
    type master;
    file "/etc/bind/zones/db.daniel-alarmas.com";
};
```

Guardamos el archivo y salimos del editor. Pero para hacerlo, presionamos la tecla `ESC` y luego escribimos `:wq`.

### Configuración de la zona DNS

Ahora, procedamos a crear la zona DNS creando el archio `db.daniel-alarmas.com`, pero antes creamos el directorio `zones` en la ruta `/etc/bind/`:

```bash
sudo mkdir zones
```

Y luego, creamos el archivo `db.daniel-alarmas.com` dentro del directorio `zones`:

```bash
cd zone;
touch db.daniel-alarmas.com
```

Luego de haber creado el archivo, procedamos a abrirlo:

```bash
sudo vi /etc/bind/zones/db.daniel-alarmas.com
```

Y agregue las siguientes líneas en el archivo presionando las teclas <key>Ctrl</key> + <key>Shift</key> + <key>V</key>:

```bash
$TTL   300
@       IN      SOA     ns1.daniel-alarmas.com. admin.daniel-alarmas.com. (
                          2025052601 ; Serial (formato AAAAMMDDnn)
                          3600       ; Refresh each hour
                          86400      ; Retry
                          2419200    ; Expire
                          300 )   ; Negative Cache TTL

; Dirección IP asignada al dominio:
@	IN	A	192.168.56.200

; Servidor DNS
@       IN      NS      ns1.daniel-alarmas.com.

; Registros A
ns1     IN      A       192.168.56.150
www     IN      A       192.168.56.200
api     IN      A       192.168.56.200
tienda	IN	CNAME	daniel-alarmas.com.
```

