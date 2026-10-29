---

name: Reporte de Incidente

about: Usa este formulario para reportar un incidente crítico

title: "[INCIDENTE] Vulnerabilidad IDOR activa en endpoint de facturas (`/api/v1/invoices/`)"

labels: [incident, urgent]

assignees: 'security-officer, dev-lead'

---


## Descripción del incidente

Se ha detectado una vulnerabilidad de tipo IDOR (Insecure Direct Object Reference). Cualquier usuario autenticado puede acceder a los datos de facturación e información personal (dirección, email, nombres) de cualquier otro cliente simplemente cambiando el número de ID correlativo en la URL del API.


## Impacto

* **Afecta a:** Confidencialidad de los datos de todos los clientes de la plataforma.

* **Gravedad:** Brecha de seguridad crítica (Riesgo regulatorio y de privacidad GDPR/CCPA).


## Tiempo de detección

* **Ocurrencia:** Desconocida (Vulnerable desde el despliegue del viernes pasado).

* **Detección:** 28 de Octubre de 2026, 08:30 AM UTC (Reportado de forma ética por un investigador externo a través de security@dominio.com).


## Pasos para reproducir

1. Iniciar sesión con la Cuenta A (ID de factura propia: 1005).

2. Hacer una petición GET usando Postman a `https://dominio.com` (Factura de la Cuenta B).

3. Ver que el servidor devuelve el JSON completo con los datos confidenciales de la Cuenta B en lugar de un error 403 Forbidden.


## Logs / Evidencia

```json

// GET /api/v1/invoices/1004 (Realizado desde el token del usuario 1005)

{

"id": 1004,

"client_name": "John Doe",

"billing_address": "123 Secure St, NY",

"total_amount": 5400.00

}

```


## Acciones tomadas

* Se bloqueó el endpoint `/api/v1/invoices/*` en el WAF (Web Application Firewall) para responder 403 de forma masiva mientras se corrige el código.

* Se validó que el reporte del investigador es verídico.


## Responsable(s)

* `@security-officer` (CISO)

* `@dev-lead` (Líder Técnico)


## Próximos pasos

- [x] Mitigación en el WAF (Bloqueo temporal de rutas de facturación)

- [ ] Modificar el controlador en Backend para añadir validación de pertenencia: `if (invoice.user_id !== current_user.id)`

- [ ] Revisar logs de auditoría para determinar si actores maliciosos explotaron esto antes de la detección

- [ ] Notificar a los usuarios afectados si la auditoría confirma fuga de datos masiva

Usa el código con precaución.