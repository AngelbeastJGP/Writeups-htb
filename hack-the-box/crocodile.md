# Hack The Box - Crocodile

## Información básica
- Plataforma: Hack The Box
- Dificultad: Very Easy
- Sistema operativo: Linux
- Vector principal: FTP + reutilización de credenciales

---

## Enumeración

La enumeración inicial se automatizó mediante un script propio en Python
(`enum_basic_v2.py`) para organizar los resultados.

### Descubrimiento de servicios

```bash
nmap -p- --min-rate 1000 IP -oN nmap_fast.txt
```
Servicios relevantes:

- 21/tcp open ftp

- 80/tcp open http

---

## Enumeración FTP

Se detectó acceso FTP anónimo, permitiendo listar y descargar archivos sin
autenticación.
```bash
ftp IP
```

Archivos obtenidos:

- allowed.userlist

- allowed.userlist.passwd

Estos archivos contenían listas de usuarios y contraseñas en texto claro.

---

## Enumeración Web

Se realizó fuerza bruta de rutas para identificar recursos web ocultos.
```bash
gobuster dir -u http://IP -w /usr/share/wordlists/dirb/common.txt -x php
```

Rutas relevantes:

/login.php

/dashboard/

---

## Acceso inicial

Las credenciales obtenidas a través de FTP fueron reutilizadas en el
formulario de login web.

**Resultado:**

- Acceso al panel /dashboard/

- Obtención de la flag

---

## Mitigación (visión ASIR)

- Deshabilitar el acceso FTP anónimo.

- No almacenar credenciales en texto claro.

- Evitar la reutilización de contraseñas entre servicios.

- Restringir el acceso a paneles administrativos.

- Monitorizar accesos y actividad sospechosa.
