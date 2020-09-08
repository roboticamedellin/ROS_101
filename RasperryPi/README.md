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
- Insertar la tarjeta SD en en equipo, en balenaEtcher seleccionar boton "Select Target" y proceder con la instalación.

### Distribuciones Linux ###
- Para usuarios mas experimentados recomendamos seguir la instalación de la [guia oficial](https://www.raspberrypi.org/documentation/installation/installing-images/linux.md).

## Configuración wifi ##

- En la siguiente guia, hay una manera práctica de realizar la configuración para la conexion wifi desde la sd, usando la unidad `boot` que aparece al conectarla a nuestro computador personal: [Setting up a Raspberry Pi headless](https://www.raspberrypi.org/documentation/configuration/wireless/headless.md).

- Si cuenta con la opción de teclado y monitor, realizar la configuración del wifi usando la configuración manual que ofrece el sistema operativo.

- Si cuenta con configuracion ethernet, realizar la siguiente configuracion siguiendo el siguente video tutorial: [Configuracion Wifi](https://www.youtube.com/watch?v=-vb9YOKQVeY)


## Instalar JupyterLab ##

Actualizar el S.O (para evitar problemas con python 3.5):
```
$ sudo apt upgrade
```

Instalar pip (En caso de no tenerlo):
```
$ sudo apt install python-pip
```

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
- c.NotebookApp.ip = ‘raspberrypi.local’ # Poner el hostname
- c.NotebookApp.open_browser = False
```

Con esta configuración, es necesario levantar jupyterlab manualmente:
```
$ jupyter-lab
```

### Iniciar Jupyter-Lab con el S.O ###

Crear el archivo de servicio:
```
sudo touch /etc/systemd/system/jupyter.service
sudo chmod 644 /etc/systemd/system/jupyter.service
```

Open an editor with sudo nano /etc/systemd/system/jupyter.service and copy/paste the following text

Abrir con el editor `sudo nano /etc/systemd/system/jupyter.service` y copiar/pegar el siguiente texto. (Asegurarse bien de los valores en `User`, `Group`, y `WorkingDirectory`):

```
[Unit]
Description=Jupyter Notebook

[Service]
Type=simple
ExecStart=/usr/local/bin/jupyter lab
User=pi
Group=pi
WorkingDirectory=/home/pi
Restart=always
RestartSec=10
#KillMode=mixed

[Install]
WantedBy=multi-user.target
```

Luego el servicio puede ser manualmente manipulado con los siguientes comandos:

```
$ sudo systemctl start jupyter.service
$ sudo systemctl status jupyter.service
$ sudo systemctl stop jupyter.service # Solo en caso de ser necesario
```

Despues, se habilita el servicio para correr con el arranque del S.O:
```
$ sudo systemctl enable jupyter.service 
```

Reiniciar la Rapsberry, e ingresar en el navegador a la url `raspberrypi.local:8888`. Requerira del password previamente configurado para acceder.

## Programar Arduino con RaspberryPi desde consola ##
El proposito de esta parte es poder programar y hacer un debug correcto con el microcontrolador sugerido para la arquitectura con la que se va a trabajar, en este caso la placa Arduino.

Instalar `minicom` y `arduino-mk`: 

```
sudo apt install minicom arduino-mk
```

Verificar la carpeta `arduino` en la ruta `/usr/share/arduino/`, con el comando:

```
$ cd /usr/share/arduino/
$ # luego volver a la carpeta home
$ cd
```

crear la carpeta `arduinoLibraries` donde almacenaremos todas las librerias que encontremos en repositorios y que deseemos instalar a futuro, en la carpeta `/home/pi`:

```
$ mkdir ~/arduinoLibraries
```

En cualquier parte, podemos crear un archivo con extensión `.ino` para poder cargarlo a la tarjeta y para ello es necesario crear un archivo `Makefile` para especificar los parametros de compilación:

Crear el archivo Makefile:

```
$ nano Makefile
```

Agregar lo siguiente:

```
# Carpeta Arduino
ARDUINO_DIR = /usr/share/arduino
# Puerto
ARDUINO_PORT = /dev/ttyACM*
# Tipo de tarjeta. Mas información (usando este archivo): make show_boards
BOARD_TAG = uno
# Carpeta de librerias
USER_LIB_PATH = /home/pi/arduinoLibraries
# Para agregar librerias al proyecto, descomentar y especificar en la siguiente variable
# ARDUINO_LIBS = /zumo /Wire


include /usr/share/arduino/Arduino.mk

```

Podemos ahora probar con un simple HelloWorld con el archivo `hello.ino`:

```
void setup(){
    Serial.begin(9600);
}

void loop(){
    Serial.println("Hello world");
    delay(1000);
}
```

Compilar y cargar a la tarjeta con el comando:
```
$ sudo make upload
```

