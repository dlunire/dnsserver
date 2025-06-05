# Guía de Instalación y Configuración de un Servidor DNS en Ubuntu Server

Esta guía describe, de forma estructurada y profesional, el proceso de instalación y configuración de un servidor DNS utilizando **BIND9** en un entorno Ubuntu Server.

📎 [← Volver al índice principal](../Readme.md "Ir al contenido principal")

---

## 1. Requisitos Previos

Antes de proceder con la configuración del servidor DNS, es indispensable contar con un sistema operativo Ubuntu Server debidamente instalado.

🔗 Si aún no has realizado este paso, consulta la siguiente guía de preparación del entorno:

* [Pasos previos a la instalación y configuración de los servidores](./preview.md "Primeros pasos antes de la instalación")

---

## 2. Instalación de los Servicios DNS

Instala los paquetes necesarios mediante el siguiente comando:

```bash
sudo apt install bind9 dnsutils
```

Este comando instalará:

* **BIND9**: el servicio principal del servidor DNS.
* **dnsutils**: herramientas útiles como `dig` y `nslookup` para realizar pruebas.

---

## 3. Configuración del Servidor DNS

### 3.1 Definición de la Zona DNS

1. Edita el archivo de configuración de zonas locales:

```bash
sudo vi /etc/bind/named.conf.local
```

2. Agrega el siguiente bloque al final del archivo:

```bash
zone "daniel-alarmas.com" {
    type master;
    file "/etc/bind/zones/db.daniel-alarmas.com";
};
```

💡 *Este bloque define que el servidor actuará como maestro para la zona `daniel-alarmas.com` y especifica la ruta del archivo que contendrá los registros DNS.*

3. Guarda los cambios y cierra el editor (`:wq` en modo comando de `vi`).

---

### 3.2 Creación del Archivo de Zona

1. Crea el directorio `zones` dentro de `/etc/bind/` si aún no existe:

```bash
sudo mkdir -p /etc/bind/zones
```

2. Crea el archivo de la zona DNS:

```bash
sudo touch /etc/bind/zones/db.daniel-alarmas.com
```

3. Abre el archivo para editarlo:

```bash
sudo vi /etc/bind/zones/db.daniel-alarmas.com
```

4. Añade el siguiente contenido:

```dns
$TTL 300
; Tiempo de vida (TTL) por defecto de los registros DNS: 300 segundos (5 minutos)

@ IN SOA ns1.daniel-alarmas.com. admin.daniel-alarmas.com. (
    2025052601 ; Serial (AAAAMMDDnn — incrementar con cada cambio)
    3600       ; Refresh (1 hora)
    86400      ; Retry (24 horas)
    2419200    ; Expire (28 días)
    300        ; Negative TTL (5 minutos)
)

; -------------------------------------------------------------------------------------
; Registros principales de la zona daniel-alarmas.com
; -------------------------------------------------------------------------------------

@    IN A     192.168.56.200       ; Dirección IP principal del dominio
@    IN NS    ns1.daniel-alarmas.com. ; Servidor DNS autorizado

; -------------------------------------------------------------------------------------
; Registros A: asignación de direcciones IPv4 a subdominios
; -------------------------------------------------------------------------------------

ns1  IN A     192.168.56.150       ; Dirección del servidor DNS primario
www  IN A     192.168.56.200       ; Subdominio www apuntando al dominio principal
api  IN A     192.168.56.200       ; Subdominio API apuntando al dominio principal

; -------------------------------------------------------------------------------------
; Registros CNAME: alias de subdominios
; -------------------------------------------------------------------------------------

tienda IN CNAME daniel-alarmas.com. ; Alias del subdominio tienda
```

---

## 4. Verificación y Recarga del Servicio DNS

1. Verifica la sintaxis de los archivos de configuración:

```bash
sudo named-checkconf
sudo named-checkzone daniel-alarmas.com /etc/bind/zones/db.daniel-alarmas.com
```

2. Recarga el servicio BIND para aplicar los cambios:

```bash
sudo systemctl restart bind9
```

3. Habilita el servicio para que se inicie automáticamente al arrancar el sistema:

```bash
sudo systemctl enable bind9
```

---

## 5. Pruebas de Funcionamiento

Puedes verificar la correcta resolución del dominio y subdominios usando `dig` o `nslookup`:

```bash
dig @localhost daniel-alarmas.com
dig @localhost www.daniel-alarmas.com
```

---

## ✅ Conclusión

Con esta configuración, tu servidor DNS ya debería estar operativo para resolver peticiones dentro de la red local (o pública, si se habilita). Asegúrate de mantener actualizados los registros de zona conforme evolucione tu infraestructura de red o servicios.