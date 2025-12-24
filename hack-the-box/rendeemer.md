# Hack The Box - Rendeemer

## Información básica
- Dificultad: Very Easy
- Plataforma: Hack The Box
- Tipo: Linux

---

## Enumeración
La enumeración inicial se automatizó con un script propio (enum_basic_v2.py) que genera ping.txt, nmap_fast.txt y nmap_full.txt.

---

### Conectividad
El host responde a ICMP y presenta un TTL cercano a 64, lo que indica un
sistema linux.

```bash
ping -c 1 10.129.48.244
```

---

### Nmap

**Descubrimiento de puertos**

Se realizó un escaneo completo de puertos TCP para identificar servicios expuestos.
```bash
nmap -p- --min-rate 1000 10.129.48.244 -oN nmap_fast.txt
```

**Resultado relevante:**

- 6379/tcp open redis
  
---

### Enumeración del servicio Redis

Se analizó el servicio Redis detectado para comprobar si existía
autenticación o restricciones de acceso.

```bash
redis-cli -h 10.129.48.244
```
  **Resultado:**
- Acceso permitido sin autenticación

- Base de datos accesible

Se enumeraron las claves almacenadas en la base de datos.
```bash
scan 0
```
Claves encontradas:

- numb

- stor

- flag

- temp

---

## Acceso a la información sensible:
Al no existir autenticación, fue posible leer directamente el contenido
de las claves almacenadas. 

```bash
mget flag
```

**Resultado:**

- Flag obtenida correctamente

---

## Mitigación (ASIR)

- Restringir el acceso a Redis mediante firewall o binding a localhost.

- Habilitar autenticación (requirepass) y control de acceso.

- Evitar exponer Redis directamente a redes no confiables.

- Monitorizar accesos y comandos ejecutados sobre el servicio.
