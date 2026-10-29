---

name: Reporte de Incidente

about: Usa este formulario para reportar un incidente crítico

title: "[INCIDENTE] Caída total de la API - Agotamiento de conexiones en PostgreSQL"

labels: [incident, urgent]

assignees: 'ops-team-lead'

---


## Descripción del incidente

La API principal (`://dominio.com`) no responde a ninguna petición entrante. El balanceador de carga (AWS ALB) está devolviendo errores HTTP 504 Gateway Timeout debido a que las instancias de la API no pueden conectarse a la base de datos principal de producción.


## Impacto

* **Afecta a:** El 100% de los usuarios de las aplicaciones móviles (iOS y Android) y el panel web.

* **Gravedad:** Interrupción total del servicio (Downtime).


## Tiempo de detección

* **Ocurrencia:** 28 de Octubre de 2026, 08:05 AM UTC

* **Detección:** 28 de Octubre de 2026, 08:06 AM UTC (Alerta automática de PagerDuty / CloudWatch).


## Pasos para reproducir

1. Intentar hacer `curl -I https://://dominio.com/health` desde cualquier terminal.

2. Ver el error `HTTP/2 504`.


## Logs / Evidencia

```log

FATAL: remaining connection slots are reserved for non-replication superuser connections

[AWS RDS] CPU Utilization: 99.4%

[AWS RDS] DatabaseConnections: 500/500 (Maxed out)

```


## Acciones tomadas

* Se incrementó temporalmente el timeout del balanceador de carga.

* Se inició el aprovisionamiento de una réplica de lectura para desviar tráfico analítico que sospechamos está saturando la base de datos.


## Responsable(s)

* `@ops-team-lead` (Infraestructura)

* `@db-admin-pro` (Administrador de Base de Datos)


## Próximos pasos

- [x] Identificar consultas lentas activas

- [ ] Forzar la terminación de conexiones inactivas (Idle) mediante script

- [ ] Aplicar PgBouncer para el pool de conexiones

- [ ] Reiniciar la instancia de base de datos si el pool no se libera en los próximos 10 minutos

Usa el código con precaución.