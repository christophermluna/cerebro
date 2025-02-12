# ESP32-CAM
Cámara montada sobra una placa con un ESP32.

Se puede poner cámaras con mayor ángulo de visión.
La OV2640 tiene 160º en vez de los 74º de la cámara estandar.

Librerías para interactuar con la cámara: https://github.com/espressif/esp32-camera

## ESPHome
Podemos usar ESPHome para crear un firmware para usar com HomeAssistant.
https://esphome.io/components/esp32_camera.html#configuration-for-ai-thinker-camera

## Micropython
mirar micropython/camara.md


## Otros modelos
https://projetsdiy.fr/esp32-cam-choix-modele-2021-aithinker-ttgo-m5stack/

## Lilygo-Camera
http://www.lilygo.cn/prod_view.aspx?TypeId=50030&Id=1273&FId=t3:50030:3

ESP32 con cámara, sensor de movimiento, pantalla y dos botones.


## Ai Thinker
http://www.ai-thinker.com/pro_view-24.html
https://www.arducam.com/esp32-machine-vision-learning-guide/
https://dronebotworkshop.com/esp32-cam-intro/
https://www.kubii.es/tarjetas-de-extension/3324-placa-de-desarrollo-esp32-cam-wifi-3272496306448.html

Viene sin puerto USB.
Hace falta un adaptador FTDI (UART to USB)

Podemos usar otro ESP32/ESP8266 como programador (alguna de las placas que vienen con puerto USB)
https://www.instructables.com/Programming-ESP32-CAM-With-ESP8266/
  como conectar un ESP8266 para programar la cámara y como cargar un programa de ejemplo que monta un server web en la cámara para mostrar las imágenes
  tras subir, quitar el cable de programación, desconectar el ESP8266, conectar a 5v y esperar a que aparezca el device en la wifi.
  Luego acceder a http://IP


Guía paa solucionar problemas
https://randomnerdtutorials.com/esp32-cam-troubleshooting-guide/

ESP_ERR_NOT_FOUND
  mirar en el .ino si solo tenemos un único define descomentado

Brownout detector was triggered
  tuve que desconectarlo del ESP8266 y alimentarlo directamente con 5v (otros pines distintos)
  No tengo claro si puedo alimentarlo al mismo tiempo que está conectado al puerto serie, no lo he probado


### Conectar una antena wifi externa
Por si queremos más rango
https://tropratik.fr/programmer-esp32-cam-avec-arduino


### Flash
Tiene un pequeño led que se puede usar como flash si no usamos la tarjeta microsd.
Mirar https://projetsdiy.fr/esp32-cam-aithinker-flash-firmware-test/
    Utiliser la LED Flash dans vos projets


### Consumo de energía
https://www.waveshare.com/esp32-cam.htm#:~:text=Deep%2DSleep%3A%20as%20low%20as,low%20as%206.7mA%405V

Flash off: 180mA@5V
Flash on and brightness max: 310mA@5V
Deep-Sleep: as low as 6mA@5V
Modern-Sleep: as low as 20mA@5V
Light-Sleep: as low as 6.7mA@5V
