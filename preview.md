# Primeros pasos antes de la instalación de los servidores

[← Volver al índice principal](./Readme.md "Ir al contenido principal")

Antes de iniciar el proceso de instalación, es fundamental contar con una imagen del sistema operativo optimizado para entornos de servidor: **Ubuntu Server**.

Puedes descargar la versión más reciente desde su [sitio web oficial](https://ubuntu.com/download/server "Descargar Ubuntu Server").

> **Importante:** Aunque es posible utilizar cualquier distribución de Linux orientada a servidores, en este tutorial se ha elegido **Ubuntu Server** debido a su estabilidad, soporte a largo plazo (LTS) y amplia disponibilidad de documentación tanto oficial como comunitaria.
>
> Todos los procedimientos descritos, incluyendo la configuración de los servidores DNS y Web, han sido realizados sobre esta distribución.

---

## Creación de las máquinas virtuales

Antes de proceder con la creación de los servidores, es necesario instalar un software de virtualización que nos permita generar máquinas virtuales independientes para cada servicio.

> **Importante:** En este tutorial se ha utilizado **VirtualBox** como herramienta de virtualización.

### Instalación en sistemas basados en Debian (como Ubuntu)

```bash
sudo apt install virtualbox
```

### Instalación en otros sistemas operativos

Si estás utilizando Windows, macOS u otra distribución de Linux, puedes descargar VirtualBox directamente desde su [sitio web oficial](https://www.oracle.com/virtualization/technologies/vm/downloads/virtualbox-downloads.html "Descargar VirtualBox").