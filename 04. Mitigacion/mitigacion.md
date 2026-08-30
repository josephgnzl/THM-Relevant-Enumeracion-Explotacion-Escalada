# Mitigación

## 1. SMB

Deshabilitar el acceso anónimo y aplicar permisos mínimos sobre los recursos compartidos.

## 2. IIS

Mantener IIS y ASP.NET actualizados y evitar que directorios compartidos sean utilizados para ejecutar contenido web.

## 3. Credenciales

No almacenar credenciales en archivos accesibles mediante SMB. Rotar inmediatamente cualquier credencial expuesta. Base64 no proporciona cifrado.

## 4. SeImpersonatePrivilege

Retirar SeImpersonatePrivilege de las cuentas de servicio cuando no sea estrictamente necesario.

## 5. Ejecución de archivos

Restringir la ejecución de binarios desde directorios temporales y aplicar controles como AppLocker o WDAC.

## 6. Monitorización

 Supervisar accesos SMB, modificaciones en directorios web y ejecución anómala de procesos desde IIS.


happy hacking, h4ck the planet! 🥇

Joseph! 
