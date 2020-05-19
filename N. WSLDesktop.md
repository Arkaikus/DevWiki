# Windows Subsystem for Linux **Desktop**

1. Activar Modo de Programador, en Configuración-> Actualización y Seguridad-> Para Programadores  

![3](wsld/3.png)

2. Activar la caracteristica "Subsistema de Windows para Linux" en Panel de Control-> Programas y Caracteristicas-> Activar o desactivar las caracteristicas de Windows y reiniciar  

![5](wsld/5.png)

3. Instalar distribución de Linux de preferencia desde la tienda de aplicaciones de microsoft (cada una tiene sus peculiaridades para instalar un manejador de ventanas, para el ejemplo: Ubuntu)  

![8](wsld/8.png)

3.1. Al abrir por primera vez se va a instalar el sistema operativo, luego de unos minutos al presionar la tecla ENTER para actualizar la consola, se pide establecer las credenciales  

![10](wsld/10.png)

3.2. Si se instala ubuntu, windows queda el comando "ubuntu.exe" desde cmd, si se instala debian, queda el comando "debian.exe" desde cmd  
3.2. Ejecutar (o lxde, o xfc4 (revisar como instalarlos)):

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install xubuntu-desktop -y
```

Si es debian se puede instalar lxde, xfce4, y se puede conseguir temas de xfce4 en [xfce-look.org](https://www.xfce-look.org/)  
En este ejemplo se decide instalar el escritorio de xubuntu ya que ubuntu-desktop presentó problemas reconociendo el servidor xorg

4. Instalar [VcXsrv](https://sourceforge.net/projects/vcxsrv/) en windows, xming también sirve, ambos son servidores xorg  
5. Crear una carpeta donde se prefiera para guardar los archivos de los pasos siguientes:  
6. Crear archivo de configuración **config.xlaunch** del servidor xorg creado con VcXsrv  

![x1](wsld/x1.jpg)
![x2](wsld/x2.jpg)
![x3](wsld/x3.jpg)
![x4](wsld/x4.jpg)
![x5](wsld/x5.jpg)

7. Descomprimir **pulseaudio-1.x.zip** de [PulseAudio](https://www.freedesktop.org/wiki/Software/PulseAudio/Ports/Windows/Support/) en la carpeta, este nos va a servir para transmitir y poder escuchar el escritorio de linux  
8. Crear un archivo .bat para iniciar el servidor y los procesos correspondientes

```bat
start /B config.xlaunch
start "" /B "<path/to>\pulseaudio\bin\pulseaudio.exe"
ubuntu.exe run "if [ -z \"$(pidof startxfce4)\" ]; then export DISPLAY=127.0.0.1:0.0; export PULSE_SERVER=tcp:127.0.0.1; startxfce4; pkill '(gpg|ssh)-agent'; taskkill.exe /IM pulseaudio.exe /F; taskkill.exe /IM vcxsrv.exe; fi;"
```

9. Ejecutar el archivo .bat, se debería abrir la consola, la ventana del servidor xorg y se debería cargar el manejador de ventanas
