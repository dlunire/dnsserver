# 🖧 Guía de instalación paso a paso del servidor DNS

Esta guía documenta detalladamente el procedimiento que se llevó a cabo para instalar y configurar un **servidor DNS** utilizando el software **BIND9**, una herramienta robusta y ampliamente utilizada para la gestión de zonas y registros DNS en entornos `Linux`.

## 1. Requisitos Previos

Antes de comenzar con la instalación del servidor DNS, se deben cumplir los siguientes requisitos:

* Un servidor Ubuntu instalado (se utilizó `Ubuntu Server 25.04`).
* Acceso administrativo con privilegios `sudo`.
* Conectividad básica de red funcional.

👉 Si aún no has preparado el entorno, consulta la guía correspondiente:

🔗 [Pasos previos a la instalación y configuración de los servidores](./preview.md "Primeros pasos antes de la instalación")

## 2. Configuración de Red con Netplan

Para garantizar que el servidor DNS sea accesible desde otras máquinas de la red, es necesario asignarle una IP estática en una de sus interfaces. Esto se realiza mediante Netplan, el sistema de configuración de red predeterminado en `Ubuntu Server`.

1. Se abrió el archivo de configuración de `Netplan` (la ruta podría variar en función de la versión de Ubuntu). Se utilizó `Ubuntu Server 25.04`.

    ```bash
    sudo vi /etc/netplan/50-cloud-init.yaml
    ```

2. Sustituye su contenido por el siguiente ejemplo (ajustando a tu red):

    ```yaml
    network:
    version: 2
    renderer: networkd
    ethernets:
        enp0s3:
        dhcp4: true
        nameservers:
            addresses: []
        enp0s8:
        dhcp4: false
        addresses:
            - 192.168.56.150/24
        nameservers:
            addresses:
            - 192.168.56.150
    ```

> **📌 Notas importantes:**
> 
> `enp0s3` debe coincidir con el nombre real de tu interfaz de red conectada a Internet (verifícalo con ip a).
> 
> enp0s8 es la interfaz usada para la red interna; se configura con una IP estática (192.168.56.150).
> 
> `gateway4` especifica la puerta de enlace predeterminada para el tráfico saliente.
> 
> `nameservers.addresses` incluye la IP del propio **servidor DNS**.


3. Aplica la configuración:

    ```bash
    sudo netplan apply
    ```

4. Verifica que la IP esté correctamente asignada:

    ```bash
    ip a
    ```

## 3. Instalación de los Servicios DNS

Procede a instalar los paquetes necesarios para que el servidor DNS funcione:

```bash
sudo apt update
sudo apt install bind9 dnsutils
```

🔍 **Explicación**:

* `bind9`: es el servidor DNS ampliamente utilizado en entornos `Linux`.
* `dnsutils`: contiene herramientas útiles como `dig` y `nslookup` para pruebas DNS.

---

## 4. Configuración del Servidor DNS

### 4.1 Definición de la Zona DNS

Para definir una zona DNS propia (por ejemplo, `daniel-alarmas.com`), se editó el archivo de configuración local de BIND:

```bash
sudo vi /etc/bind/named.conf.local
```

Y luego se agregó estas estas líneas para definir la zona DNS:

```bash
zone "daniel-alarmas.com" {
    type master;
    file "/etc/bind/zones/db.daniel-alarmas.com";
};
```

📌 **Explicación**:

* `type master`: indica que este servidor es el autoritativo para la zona.
* `file`: ruta del archivo que contendrá los registros DNS de esta zona.

Luego, se guardó y cerró el archivo presionando `ESC` y luego, escribiendo esto: `:qw`

### 4.2 Creación del Archivo de Zona

1. Se creó el directorio donde se almacenaron los archivos de zona:

    ```bash
    sudo mkdir -p /etc/bind/zones
    ```

2. Y luego se creó el archivo de zona por medio del comando `touch`:

    ```bash
    sudo touch /etc/bind/zones/db.daniel-alarmas.com
    ```

3. Se abrió para editarlo:

    ```bash
    sudo vi /etc/bind/zones/db.daniel-alarmas.com
    ```

4. Y se insertó el siguiente contenido, que es el contenido del registro DNS, donde se definieron, el dominio principal, subdominios y alias:

    ```dns
    $TTL 300
    @ IN SOA ns1.daniel-alarmas.com. admin.daniel-alarmas.com. (
        2025052601 ; Serial: formato AAAAMMDDnn, debe incrementarse con cada cambio
        3600       ; Refresh: cada cuánto tiempo un servidor esclavo debe consultar cambios
        86400      ; Retry: espera en caso de fallo al contactar con el maestro
        2419200    ; Expire: tiempo que mantiene datos válidos sin contacto
        300        ; Negative TTL: tiempo para respuestas negativas
    )

    ; ------------------------- Registros de zona -------------------------

    @    IN A     192.168.56.200           ; Dirección IP principal del dominio
    @    IN NS    ns1.daniel-alarmas.com. ; Servidor de nombres para la zona

    ; ---------- Registros A (IPv4) ----------

    ns1  IN A     192.168.56.150           ; Dirección del servidor DNS
    www  IN A     192.168.56.200           ; Subdominio www
    api  IN A     192.168.56.200           ; Subdominio API

    ; ---------- Registros CNAME (alias) ----------

    tienda IN CNAME daniel-alarmas.com.   ; Alias para tienda
    ```

📌 **Notas importantes**:

* `@` representa el nombre base de la zona (`daniel-alarmas.com`).
* `SOA` (Start of Authority) contiene información crítica de sincronización.
* Todos los registros deben finalizar con punto (`.`) si están completamente calificados.


## 5. Verificación y Recarga del Servicio DNS

1. Se verificó que los archivos de configuración no tengan errores de sintaxis:

```bash
sudo named-checkconf
sudo named-checkzone daniel-alarmas.com /etc/bind/zones/db.daniel-alarmas.com
```

2. Se comprobó que no hubo errores, se reinició el servicio BIND para aplicar los cambios:

```bash
sudo systemctl restart bind9
```

3. Se aseguró que BIND se inició automáticamente con el sistema:

```bash
sudo systemctl enable bind9
```

## 6. Pruebas de Funcionamiento

Se probó la configuración configuración y resolución de nombres por medio de los siguientes comandos:

```bash
dig @localhost daniel-alarmas.com
dig @localhost www.daniel-alarmas.com
```

📌 **Consejo**: Si obtienes una respuesta con las direcciones IP configuradas, la resolución está funcionando correctamente. También puedes usar `nslookup`.

## ✅ Conclusión

Con estos pasos finalizados, el servidor DNS con `BIND9` queda correctamente configurado y operativo. Esta configuración permite resolver nombres dentro de la red local, y está preparada para escalabilidad y mantenimiento continuo. Se recomienda:

* Mantener el número de serie actualizado en cada modificación de la zona.
* Verificar regularmente los registros mediante `named-checkzone`.
* Realizar respaldos periódicos de los archivos de zona.

<br>

> ⚠️ IMPORTANTE
> 
> Durante la fase de desarrollo, el valor TTL (Time to Live) se configuró en 300 segundos (5 minutos) con el objetivo de permitir una propagación rápida de los cambios en los registros DNS.
> 
> Esta configuración fue útil para facilitar pruebas frecuentes. Sin embargo, en un entorno de producción se recomienda establecer un TTL más alto para mejorar la eficiencia del sistema y reducir la carga en los resolvers.
> 
> Por convención, suele utilizarse un valor de 604800 segundos (equivalente a una semana) en producción, a menos que se justifique otra política de caché.