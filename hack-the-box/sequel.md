# Hack The Box - Sequel

## Información básica
- Plataforma: Hack The Box
- Dificultad: Very Easy
- Sistema operativo: Linux
- Servicio afectado: MariaDB / MySQL

---

## Enumeración

La enumeración inicial se automatizó mediante un script propio en Python
(`enum_basic_v2.py`), generando los archivos `ping.txt`, `nmap_fast.txt`
y `nmap_full.txt`.

---

### Descubrimiento de puertos

Se realizó un escaneo completo de puertos TCP para identificar servicios
expuestos.

```bash
nmap -p- --min-rate 1000 IP -oN nmap_fast.txt
```
---

**Resultado relevante:**

- 3306/tcp open mysql

---

## Enumeración del servicio MySQL / MariaDB

Se identificó un servicio MariaDB accesible desde red sin controles
adicionales.

Al intentar conectar inicialmente, se detectó un problema de negociación
SSL entre cliente y servidor, que se resolvió forzando una conexión sin
SSL.
```bash
mysql -h IP -u root --skip-ssl
```

---

**Resultado:**

- Acceso concedido sin autenticación

- Usuario con privilegios elevados

---

### Acceso a la base de datos

Una vez dentro del servicio, se enumeraron las bases de datos disponibles.
```bash
SHOW DATABASES;
```

Base relevante:

- htb
```bash
USE htb;
SHOW TABLES;
```

Tablas identificadas:

- users

- config

---

### Obtención de información sensible

Se consultó la tabla config, donde se encontró información sensible
almacenada en texto claro.
```bash
SELECT * FROM config;
```

**Resultado:**

- Flag obtenida correctamente desde la base de datos

---

## Mitigación (visión ASIR)

- Medidas recomendadas para asegurar el servicio de base de datos:

- No exponer servicios de bases de datos directamente a redes no confiables.

- Restringir el acceso mediante firewall o binding a localhost.

- Configurar autenticación robusta y eliminar accesos sin contraseña.

- Revisar y asegurar la configuración SSL/TLS de cliente y servidor.

- Monitorizar accesos y consultas realizadas sobre el servicio.
