# Hack The Box - Dancing

## Información básica
- Dificultad: Very Easy
- Plataforma: Hack The Box
- Tipo: Windows

---

## Enumeración
La enumeración inicial se automatizó con un script propio (enum_basic_v2.py) que genera ping.txt, nmap_fast.txt y nmap_full.txt.
### Conectividad
El host responde a ICMP y presenta un TTL cercano a 128, lo que indica un
sistema Windows.

```bash
ping -c 1 10.129.48.212
```
### Nmap

**Descubrimiento de puertos**

Se realizó un escaneo completo de puertos TCP para identificar servicios expuestos.
```bash
nmap -p- --min-rate 1000 10.129.48.212 -oN nmap_fast.txt
```

**Resultado relevante:**

- 135/tcp (MSRPC)

- 139/tcp (NetBIOS)

- 445/tcp (SMB)

- 5985/tcp (WinRM)

  **Resultado relevante:**

Servicio SMB activo

Firma SMB habilitada pero no obligatoria

**Implicación de seguridad:**
La presencia de SMB con firma no obligatoria sugiere posibles sesiones
anónimas o configuraciones débiles.  

## Acceso inicial

**Vector:** SMB con permisos de lectura inapropiados

Se descargaron los archivos presentes en el share accesible con un get.

```bash
smbclient //10.129.48.212/WorkShares -U ADMIN$
```
Share accesible: WorkShares

Se accedió al share identificado sin necesidad de credenciales válidas.

**Resultado**
El archivo James.P contenía la flag del sistema, permitiendo completar
el objetivo sin necesidad de autenticación adicional.

## Mitigación (ASIR)

- Restringir el acceso a recursos SMB y deshabilitar sesiones anónimas.

- Aplicar firma SMB obligatoria cuando sea posible.

- Revisar permisos de los shares y evitar exponer información sensible.

- Monitorizar accesos SMB y generar alertas ante accesos no autorizados.
