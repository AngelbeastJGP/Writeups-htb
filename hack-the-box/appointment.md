# Hack The Box - Appointment

## Información básica
- Plataforma: Hack The Box
- Dificultad: Very Easy
- Vector principal: Web (login)
- Tipo: Auth bypass (validación insegura / posible SQLi)

---

## Enumeración

La enumeración inicial se automatizó mediante un script propio en Python
(`enum_basic_v2.py`) para organizar resultados (`ping.txt`, `nmap_fast.txt`,
`nmap_full.txt`).

### Servicio expuesto
Se identificó un servicio web accesible desde el navegador (login).

---

## Enumeración Web

### Observación del formulario
Se revisó el formulario de login y los campos enviados por POST.
Campos detectados:
- `username`
- `password`

### Comportamiento del backend
El sistema diferencia entre usuario y contraseña:
- Con `admin` y una contraseña incorrecta, el mensaje indica fallo de contraseña,
  lo que sugiere que el usuario existe y se valida en primer lugar.

Además, la opción “Forgot password” solo modifica el fragmento de la URL (`#`),
lo que indica que es un comportamiento del lado cliente (frontend) sin ruta real
en backend.

---

## Acceso inicial (Auth Bypass)

**Vector de ataque:** Bypass de autenticación mediante entrada mal validada en el campo `username`.

Se introdujo un valor especial en el campo `username` que altera la lógica de
verificación en el backend, permitiendo acceso incluso con una contraseña
arbitraria.

**Resultado:**
- Acceso a la aplicación sin conocer credenciales válidas.

---

## Mitigación (visión ASIR)

Medidas recomendadas:

- Usar consultas parametrizadas / prepared statements (evitar concatenación de strings).
- Validación y sanitización robusta de entradas en backend (no confiar en el cliente).
- Mensajes de error genéricos (no revelar si el usuario existe).
- Implementar controles anti-abuso (rate limiting, lockout progresivo).
- Logging y alertas ante patrones anómalos de login.
