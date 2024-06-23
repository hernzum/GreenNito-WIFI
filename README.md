# GreenNito-WIFI
Un bot de Telegram que te envía un mensaje cuando el ESP se inicia
Este sketch utiliza WiFiManager para configurar dinámicamente la conexión WiFi.
    Después de cargar el sketch, busca y conecta automáticamente a la red WiFi 
    guardada previamente. Si no puede conectarse, crea un punto de acceso 'AutoConnectAP'
    para que puedas configurar las credenciales WiFi mediante un portal web.

    El bot de Telegram envía mensajes según la lectura del sensor de humedad del suelo.
    Si la humedad es inferior al 50%, enciende un LED y envía un mensaje de advertencia.
    De lo contrario, apaga el LED y envía un mensaje de humedad normal.
