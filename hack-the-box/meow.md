# Hack The Box - Meow

## Información básica
- Dificultad: Very Easy
- Plataforma: Hack The Box
- Tipo: Linux

---

## Enumeración

### Conectividad
El objetivo responde a ICMP y presenta un TTL cercano a 64, lo que sugiere un sistema Linux.

```bash
ping -c 1 10.129.48.159
```
### Nmap

**Descubrimiento de puertos**

Se realizó un escaneo completo de puertos TCP para identificar servicios expuestos.
```bash
nmap -p- --min-rate 1000 10.129.48.159 -oN nmap_fast.txt
```

Resultado relevante:

- 23/tcp open telnet

**Implicación de seguridad:**
Telnet transmite credenciales en claro y suele estar asociado a configuraciones antiguas o inseguras. Si existen credenciales débiles o por defecto, es un vector directo de acceso.  

## Acceso inicial

**Vector:** Telnet (23/tcp)

Se intentó autenticación en Telnet con credenciales débiles/por defecto (caso típico en esta máquina).

```bash
telnet 10.129.48.159
```
credenciales por defecto
- Acceso conseguido como root

## Mitigación (ASIR)

- Deshabilitar Telnet y usar SSH.
- Si por compatibilidad debe existir Telnet: restringir por firewall/ACL a IPs concretas y segmentar la red.
- Aplicar políticas de contraseñas fuertes y eliminar credenciales por defecto.
- Monitorizar intentos de login en el servicio (logs) y alertar ante fuerza bruta.
