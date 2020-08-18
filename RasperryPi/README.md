# Guía Configuracion Raspberry Pi #

Esta guia tiene como referencia el tutorial oficial de raspberrypi.org para la [instalación del Sistema Operarivo](https://www.raspberrypi.org/documentation/installation/installing-images/).

### Requerimientos ###
- SD card de 16 GB, clase 10.
- Raspberry Pi 3 / Raspberry Pi 4 (dependiendo del modelo puede ser 32 o 64 bits).

Recomendamos tener a disposicion una conexion ethernet para facilitar el proceso de configuración, y no requerir de monitor ni teclado. Si no cuenta con conexión ethernet, recomendamos hacer la configuracion a traves de teclado y monitor.

### Hardware Opcional ###
- Monitor HDMI y Teclado (Recomendado).

Las versiones mas recientes de Raspberry Pi 4, requieren una distribucion de 64 bits. Por favor consultarlo con la información tecnica del modelo.

### Descargar una distribución ###

Para poder trabajar con la Raspberry es necesario contar con una distribución de linux compatible. Para este caso recomendamos una de las siguientes opciones:

- [Ubuntu Mate](https://ubuntu-mate.org/download/) (recomendado para trabajar con ROS)
- [Raspberry Pi OS](https://www.raspberrypi.org/downloads/raspberry-pi-os/) (Sistema Operativo Oficial)

## Herramientas ##

### Windows y Mac ###
- Para Windows y Mac, se recomienda usar el software balenaEtcher el cual puede ser descargado de la página balena.io
- Una vez instalado, ejecutar balenaEtcher y seleccionar la opción "Select image".
![](./media/01.PNG)

### Distribuciones Linux ###
- Para usuarios mas experimentados recomendamos seguir la instalación de la [guia oficial](https://www.raspberrypi.org/documentation/installation/installing-images/linux.md).

## Configuración wifi ##

- En la siguiente guia, hay una manera práctica de realizar la configuración para la conexion wifi desde la sd, usando la unidad `boot` que aparece al conectarla a nuestro computador personal: [Setting up a Raspberry Pi headless](https://www.raspberrypi.org/documentation/configuration/wireless/headless.md).

- Si cuenta con la opción de teclado y monitor, realizar la configuración del wifi usando la configuración manual que ofrece el sistema operativo.

- Si cuenta con configuracion ethernet, realizar la siguiente configuracion siguiendo el siguente video tutorial: [Configuracion Wifi](https://www.youtube.com/watch?v=-vb9YOKQVeY)


## Después de haber instalado y configurado el S.O ##
Instalar Jupyterlab:
```
$ pip install jupyterlab
```

Luego, realizar la siguiente configuración para conectarse a jupyterlab:

Generar un archivo de configuración `configuration`
```
$ jupyter-lab --generate-config
```
Configurar un password para jupyter
```
$ jupyter notebook password
```

El archivo generado se crea en la ruta `/home/pi/.jupyter/jupyter_notebook_config.py`

Editar el archivo generado usando:
```
$ nano /home/pi/.jupyter/jupyter_notebook_config.py
```

Descomentar y configurar las siguientes opciones (buscarlas en el orden alfabético):
```
- c.NotebookApp.allow_origin = '*'
- c.NotebookApp.ip = ‘*’
- c.NotebookApp.open_browser = False
```

Con esta configuración, es necesario levantar jupyterlab manualmente:
```
$ jupyter-lab
```