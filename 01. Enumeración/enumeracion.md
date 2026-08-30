# Enumeracion

Comenzamos la fase de reconocimiento realizando un escaneo de la máquina objetivo con Nmap, con el propósito de identificar los puertos abiertos, los servicios expuestos y sus respectivas versiones.

Para este primer escaneo utilizaremos los siguientes parámetros:
```
Parámetro	Función
-sV	Detecta las versiones de los servicios disponibles.
-p-	Escanea los 65.535 puertos TCP.
-sC	Ejecuta los scripts NSE predeterminados para obtener información adicional.
-vvv	Aumenta el nivel de detalle mostrado durante el escaneo.
```
El escaneo se ejecuta con el siguiente comando:
```
nmap -p- -sC -sV -vvv 10.129.125.26
```
