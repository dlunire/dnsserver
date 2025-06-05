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
$TTL 300
; Tiempo de vida (TTL) por defecto de los registros DNS: 300 segundos (5 minutos)

@ IN SOA ns1.daniel-alarmas.com. admin.daniel-alarmas.com. (
    2025052601 ; Número de serie (formato AAAAMMDDnn — cambia con cada modificación)
    3600       ; Tiempo de refresco del secundario (1 hora)
    86400      ; Tiempo de reintento si falla el refresco (24 horas)
    2419200    ; Tiempo de expiración si no se puede contactar al primario (28 días)
    300        ; TTL para respuestas negativas (no existe el dominio)
)

; -------------------------------------------------------------------------------------
; Registros principales del dominio daniel-alarmas.com
; -------------------------------------------------------------------------------------

; Dirección IP principal asignada al dominio raíz
@ IN A 192.168.56.200

; Servidor DNS autorizado (registro NS)
@ IN NS ns1.daniel-alarmas.com.

; -------------------------------------------------------------------------------------
; Registros A (direcciones IPv4 para subdominios)
; -------------------------------------------------------------------------------------

; Dirección IP del servidor DNS primario
ns1 IN A 192.168.56.150

; Subdominio www apuntando a la IP principal del dominio
www IN A 192.168.56.200

; Subdominio api apuntando a la misma IP que el dominio principal
api IN A 192.168.56.200

; -------------------------------------------------------------------------------------
; Registro CNAME (alias)
; -------------------------------------------------------------------------------------

; Subdominio tienda redirige a daniel-alarmas.com como alias
tienda IN CNAME daniel-alarmas.com.

```

