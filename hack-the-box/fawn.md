# Hack The Box - Fawn

## Información básica
- Dificultad: Very Easy
- Plataforma: Hack The Box
- Tipo: Linux

---

## Enumeración
La enumeración inicial se automatizó con un script propio (enum_basic_v2.py) que genera ping.txt, nmap_fast.txt y nmap_full.txt.
### Conectividad
El objetivo responde a ICMP y presenta un TTL cercano a 64, lo que sugiere un sistema Linux.

```bash
ping -c 1 10.129.48.184
```
### Nmap

**Descubrimiento de puertos**

Se realizó un escaneo completo de puertos TCP para identificar servicios expuestos.
```bash
nmap -p- --min-rate 1000 10.129.48.184 -oN nmap_fast.txt
```

Resultado relevante:
- 21/tcp open ftp
- Servicio: vsftpd 3.0.3

- Acceso anónimo habilitado

**Implicación de seguridad:**
El acceso FTP anónimo permite a cualquier usuario listar y descargar
archivos sin autenticación, lo que puede provocar exposición de
información sensible.

## Acceso inicial

**Vector:** FTP con acceso anónimo
Se accedió al servicio FTP utilizando el usuario anonymous, sin
necesidad de credenciales válidas
```bash
ftp 10.129.48.184
```
**Resultado**

Acceso al sistema de archivos del servicio

Descarga del archivo flag.txt

## Mitigación (ASIR)

- Deshabilitar el acceso FTP anónimo.

- Restringir el servicio FTP mediante firewall o segmentación de red.

- Revisar periódicamente permisos y contenido expuesto en servicios
públicos.

- Sustituir FTP por protocolos seguros como SFTP cuando sea posible.
