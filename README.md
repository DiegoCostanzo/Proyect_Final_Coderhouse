# Ecosistema de Automatización IA — Clasificación de Leads VIP

**Entrega Final — Coderhouse (Curso de Automatización IA con n8n)**

Sistema de clasificación automática de consultas de una agencia de viajes, con procesamiento por IA (Google Gemini), validación humana (HITL) para leads VIP, y trazabilidad completa en Airtable.

## Stack

- **Orquestador:** n8n
- **Base de datos / memoria:** Airtable
- **Procesamiento IA:** Google Gemini 2.5 Flash
- **Salida multicanal:** Gmail (respuestas al cliente) + Slack (aprobación humana VIP)

## Estructura del repositorio

```
/
├── README.md                          → este archivo
├── documentacion/
│   └── Documentacion_Tecnica_Entrega_Final_Leads_VIP.pdf
│                                       → arquitectura, schema de datos, JSON de
│                                         integraciones, matriz de costos, seguridad
│                                         y resiliencia, testing, y anexos con
│                                         evidencia completa + JSON del flujo
├── flujo/
│   └── Entrega_Final_-_Clasificacion_Leads_VIP.json
│                                       → export del workflow de n8n
├── evidencia/
│   └── capturas de pantalla del proceso de armado y de las 5 corridas de prueba
└── video/
    └── enlace al video demo (ver abajo)
```

## Enlaces del proyecto

| Recurso | Enlace |
|---|---|
| Base de Airtable (modo lectura) | https://airtable.com/invite/l?inviteId=invIVvnj2fqbx8HNa&inviteToken=0eefddfd0bf22e1eb2c24df9b7fe8de0b693dadc08b47e753afdf20b3244aa82 |
| Dashboard de Control (Shared View agrupada por Estado) | https://airtable.com/appgE0chAL2PJ8tNd/shrih8NDLsoOiKt15 |
| Video demo | `[PEGAR AQUÍ el link de YouTube/Drive, o indicar que está en /video]` |


## Caso de negocio

Un formulario/webhook recibe consultas de potenciales clientes (nombre, email, destino, presupuesto, mensaje). El sistema, sin intervención manual:

1. Valida que los datos estén completos y bien tipados.
2. Clasifica la consulta como **VIP** o **Estándar** mediante Gemini, y redacta un borrador de respuesta.
3. Para leads **VIP**: pausa el flujo y espera aprobación humana en Slack antes de enviar cualquier respuesta.
4. Para leads **Estándar**: envía la respuesta directamente por Gmail.
5. Registra el estado de cada lead y cualquier error (datos faltantes o falla de la IA) en Airtable, con aviso al equipo por Gmail ante fallas.

Ver el PDF en `/documentacion` para el detalle completo de arquitectura, schema de datos, costos y seguridad.

## Cómo probar el flujo

El Webhook de n8n queda expuesto en modo test — la URL de abajo solo responde mientras el workflow está en "Listen for test event" dentro del editor de n8n. Ejemplos de los 5 casos usados para el testing (camino feliz y camino infeliz):

```bash
# Caso 1 - VIP claro
curl -X POST https://cdcostanzo.app.n8n.cloud/webhook-test/webhook-leads \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Juana Pérez","email":"juana@mail.com","destino":"Bariloche","presupuesto":800000,"mensaje":"Queremos algo exclusivo para nuestra luna de miel, sin límite de presupuesto"}'

# Caso 2 - Estándar claro
curl -X POST https://cdcostanzo.app.n8n.cloud/webhook-test/webhook-leads \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Carlos Gómez","email":"carlos@mail.com","destino":"Villa Gesell","presupuesto":80000,"mensaje":"Quiero saber precios de cabañas para 4 personas en enero"}'

# Caso 3 - Ambiguo
curl -X POST https://cdcostanzo.app.n8n.cloud/webhook-test/webhook-leads \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Marina López","email":"marina@mail.com","destino":"Ushuaia","presupuesto":250000,"mensaje":"Necesitamos algo urgente, viajamos en dos semanas"}'

# Caso 4 - VIP por lenguaje, no por presupuesto
curl -X POST https://cdcostanzo.app.n8n.cloud/webhook-test/webhook-leads \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Roberto Díaz","email":"roberto@mail.com","destino":"Iguazú","presupuesto":150000,"mensaje":"Buscamos un servicio premium, todo incluido, atención personalizada"}'

# Caso 5 - Camino infeliz: dato faltante
curl -X POST https://cdcostanzo.app.n8n.cloud/webhook-test/webhook-leads \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Lucía Fernández","email":"lucia@mail.com","destino":"Mendoza","presupuesto":"","mensaje":"Consulta por paquetes"}'
```

## Autor

Diego Costanzo — Entrega Final, curso de Automatización con IA (n8n), Coderhouse.
