# Relevant 

Fecha: 29/08/2026
---
Autor: Joseph González 
---

El objetivo de este repositorio es documentar de forma clara y reproducible el proceso de enumeración, explotación, movimiento lateral y escalada de privilegios, mostrando los comandos utilizados y el razonamiento detrás de cada paso. Asimismo, se proporcionan las pautas necesarias para mitigar las vulnerabilidades identificadas durante el proceso.


## Información de la máquina
---
| Plataforma | TryHackMe |
|---|---|
| OS | Windows |
| Dificultad | Media |
| Tipo | CTF |
---
## Cadena de Ataque
```
Reconocimiento
     ↓
Enumeración de puertos y servicios
     ↓
    SMB
     ↓
Enumeración de recursos compartidos
     ↓
Acceso a SMB con credenciales encontradas
     ↓
Obtención de archivos / información sensible
     ↓
Identificación del servicio vulnerable
     ↓
Explotación
     ↓
Shell como usuario de servicio
     ↓
Enumeración local
     ↓
Identificación de privilegios/configuración vulnerable
     ↓
Privilege Escalation
     ↓
NT AUTHORITY\SYSTEM
```


