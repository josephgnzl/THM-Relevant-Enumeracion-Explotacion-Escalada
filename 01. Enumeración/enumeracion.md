# Enumeración

Comenzamos la fase de reconocimiento realizando un escaneo de la máquina objetivo con Nmap, con el propósito de identificar los puertos abiertos, los servicios expuestos y sus respectivas versiones.

Para este primer escaneo utilizaremos los siguientes parámetros:

---
| Parámetro | Función |
|---|---|
| `-sV` | Detecta las versiones de los servicios. |
| `-p-` | Escanea los 65.535 puertos TCP. |
| `-sC` | Ejecuta los scripts NSE predeterminados. |
| `-vvv` | Aumenta el nivel de detalle. |
---

El escaneo se ejecuta con el siguiente comando:
```
nmap -p- -sC -sV -vvv 10.129.125.26
```

## Puertos abiertos

| Puerto | Estado | Servicio | Posible función / análisis |
|---:|:---:|---|---|
|`80/tcp`| open | HTTP | Servidor Web. |
|`135/tcp`| open | MSRPC | Servicio RPC de Windows. Permite identificar componentes y servicios asociados al sistema. |
|`139/tcp`| open | NetBIOS-SSN | Asociado a SMB sobre NetBIOS. Permite enumeración de recursos compartidos. |
|`445/tcp`| open | Microsoft-DS / SMB | Servicio SMB. Prioridad alta para enumerar shares, usuarios y posibles configuraciones inseguras. |
|`3389/tcp`| open | RDP | Escritorio remoto de Windows. Puede ser utilizado para acceso remoto si se obtienen credenciales válidas. |
|`49663/tcp`| open | HTTP | Posible servicio web adicional ejecutándose en un puerto no estándar. Requiere enumeración independiente. |
|`49667/tcp`| open | MSRPC | Puerto RPC dinámico de Windows. Puede proporcionar información adicional sobre servicios del sistema. |

### Análisis

La superficie de ataque está dominada por servicios propios de Windows, especialmente `SMB/RPC`, junto con servicios web y RDP.
El puerto `445/tcp` será una de las prioridades de enumeración debido a la posibilidad de encontrar recursos compartidos, archivos o información sensible.
RDP (`3389/tcp`) representa otro posible vector de acceso remoto, aunque requiere credenciales válidas o una vulnerabilidad específica.
La siguiente fase será enumerar cada servicio para determinar cuál proporciona el primer punto de entrada.

## Enumeración de SMB

Ya identificado el servicio, procedemos a listar los recursos compartidos que tiene el servidor alojado. 
Vamos a utilizar `SMBCLIENT` para nuestra enumeración.

El comando es el siguiente: 

```smbclient -L \\\\10.129.125.26\\```

Resultado:

| Recurso | Acceso | Observación |
|---|---|---|
| `ADMIN$` | Denegado | Recurso administrativo de Windows. |
| `C$` | Denegado | Recurso administrativo del sistema. |
| `IPC$` | Accesible | Recurso utilizado para comunicación entre procesos. |
| `nt4wrksv` | **Accesible** | Recurso compartido con acceso anónimo. |

En pentesting también podemos utilizar nxc para enumerar recursos compartidos en entornos Windows. Sin embargo, para este CTF no resulta necesario, ya que smbclient nos proporciona la información necesaria.

El recurso **`nt4wrksv`** resulta especialmente interesante debido a que permite acceso sin proporcionar credenciales.

