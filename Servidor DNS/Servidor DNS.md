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

