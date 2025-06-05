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
