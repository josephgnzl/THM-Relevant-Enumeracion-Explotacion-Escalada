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
