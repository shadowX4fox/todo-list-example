# 3-Tier To-Do List Application
## Presentación para Partes Interesadas del Negocio | Febrero 2026

---

## Agenda

1. Resumen Ejecutivo
2. Valor de Negocio
3. Problema y Solución
4. Usuarios Objetivo y Casos de Uso
5. Rendimiento y Disponibilidad del Sistema
6. Principios de Arquitectura
7. Escalabilidad y Costos
8. Soporte Operacional
9. Resumen y Próximos Pasos

---

## SECCIÓN 1: Resumen Ejecutivo

---

## Resumen Ejecutivo

- **Sistema**: Aplicación web cloud-native de gestión de tareas diarias
- **Arquitectura**: 3 capas clásicas — Angular → Spring Boot → PostgreSQL en Azure AKS
- **Disponibilidad objetivo**: 99.9% uptime (SLA garantizado)
- **Capacidad de diseño**: 20 TPS lectura / 10 TPS escritura sostenidos
- **Usuarios objetivo**: Profesionales productivos y usuarios generales
- **Versión**: 1.0.0 — Estado: Draft

---

## SECCIÓN 2: Valor de Negocio

---

## Valor de Negocio

| Beneficio | Descripción | Impacto |
|-----------|-------------|---------|
| **Cero pérdida de datos** | Persistencia ACID garantizada con PostgreSQL | Alto |
| **Tiempos de respuesta rápidos** | <500ms para la mayoría de operaciones | Alto |
| **Arquitectura escalable** | De 100 a 10,000+ usuarios sin rediseño | Alto |
| **Experiencia simple** | Solo 4 funciones esenciales, sin complejidad | Medio |

---

## SECCIÓN 3: Problema y Solución

---

## El Problema

- Las tareas se pierden con métodos físicos (notas, papel, pegadinas)
- Las herramientas complejas tienen curvas de aprendizaje muy altas
- Sin persistencia de datos entre sesiones o dispositivos
- Rendimiento inconsistente bajo carga concurrente de múltiples usuarios

---

## La Solución

- Aplicación web accesible desde cualquier lugar con autenticación futura
- Toda la información de tareas persiste y **nunca se pierde**
- Interfaz simple con **solo 4 funciones esenciales**: agregar, visualizar, completar y eliminar
- Cloud-native en Azure AKS para escalar automáticamente con la demanda

---

## SECCIÓN 4: Usuarios y Casos de Uso

---

## Usuarios Objetivo

| Segmento | Perfil | Necesidad Principal |
|----------|--------|---------------------|
| **Profesionales Productivos** (Primario) | Gestión diaria de tareas de trabajo y vida personal | Confiabilidad y acceso desde cualquier lugar |
| **Usuarios Generales** (Secundario) | Seguimiento simple sin complejidad extra | Simplicidad y velocidad |

---

## Casos de Uso Principales

1. **Agregar Nueva Tarea** — Crear un ítem con validación de entrada (max. 500 caracteres)
2. **Visualizar Todas las Tareas** — Vista completa con indicadores de estado
3. **Marcar Tarea Completa / Incompleta** — Alternar estado de progreso con un clic
4. **Eliminar Tarea** — Eliminar tareas permanentemente con confirmación opcional

- Tasa de éxito objetivo: **>99.5%** para todas las operaciones
- **80%** de solicitudes de lectura servidas desde caché Redis (respuesta <200ms)

---

## SECCIÓN 5: Rendimiento y Disponibilidad

---

## Rendimiento del Sistema

| Operación | Objetivo (p95) | Fuente de Medición |
|-----------|----------------|-------------------|
| Agregar Tarea (POST) | **<500ms** | Spring Boot Actuator + Prometheus |
| Visualizar Tareas (caché activa) | **<200ms** | Redis Metrics + Prometheus |
| Visualizar Tareas (sin caché) | **<1,000ms** | Spring Boot Actuator |
| Marcar Completa (PATCH) | **<300ms** | Spring Boot Actuator |
| Eliminar Tarea (DELETE) | **<300ms** | Spring Boot Actuator |

---

## Disponibilidad del Sistema

- **SLA objetivo**: 99.9% uptime — máximo **8.76 horas/año** de mantenimiento planificado
- **Redundancia**: 2+ instancias de cada capa desplegadas simultáneamente en AKS
- **Auto-escalado**: Kubernetes Horizontal Pod Autoscaler (HPA) — hasta 10 pods en pico
- **Tasa de caché**: >80% de solicitudes servidas desde Redis (evita carga en la base de datos)
- **Pruebas de carga**: Escenarios con JMeter/Gatling para validar objetivos en staging

---

## SECCIÓN 6: Principios de Arquitectura

---

## Principios de Arquitectura (Top 5)

1. **Separación de Responsabilidades** — Cada capa con rol independiente; sin acceso directo a la base de datos desde la capa de presentación
2. **Alta Disponibilidad** — 99.9% uptime con recuperación automática, health checks y auto-restart en AKS
3. **Escalabilidad Primero** — Diseño stateless permite escalar horizontalmente de 100 a 10,000+ usuarios
4. **Seguridad por Diseño** — Protección OWASP Top 10 en todas las capas (XSS, SQLi, CORS, TLS 1.3)
5. **Observabilidad** — Monitoreo completo con Prometheus, Grafana y Azure Monitor desde el primer día

---

## SECCIÓN 7: Escalabilidad y Costos

---

## Planificación de Capacidad

| Escenario | Infraestructura | Costo Estimado/mes |
|-----------|-----------------|-------------------|
| **100 usuarios** (inicial) | 2 pods (1 vCPU, 1GB RAM), DB básica 2 vCPU / 8GB, Redis 4GB | ~$300–$400 USD |
| **1,000 usuarios** (crecimiento) | 5 pods, DB 4 vCPU / 16GB, Redis 13GB | ~$800–$1,000 USD |
| **10,000 usuarios** (escala) | 10 pods, DB 8 vCPU / 32GB + 2 read replicas, Redis 26GB | ~$2,000–$3,000 USD |

---

## SECCIÓN 8: Soporte Operacional

---

## Soporte Operacional

**Monitoreo continuo:**
- Dashboards en Grafana: rendimiento, base de datos, caché e infraestructura
- Alertas automáticas vía Prometheus Alertmanager + Azure Monitor

**Respaldo y Recuperación:**
- Backups diarios automáticos — retención 30 días en Azure Blob Storage (geo-redundante)
- **RTO**: <2 horas | **RPO**: <1 hora (transaction logs cada 5 minutos)

**Respuesta a Incidentes:**
- **P1 Crítico**: Respuesta <15 minutos (PagerDuty — sistema caído, pérdida de datos)
- **P2 Alto**: Respuesta <1 hora (Slack + Email — rendimiento degradado)
- **P3 Medio**: Respuesta <4 horas — revisiones trimestrales de recuperación ante desastres (DR Drills)

---

## SECCIÓN 9: Resumen y Próximos Pasos

---

## Resumen

- Aplicación **cloud-native** desplegada en Azure AKS con arquitectura de 3 capas probada
- **SLA de 99.9%** de disponibilidad con escalabilidad desde 100 hasta 10,000+ usuarios
- **Cero pérdida de datos** garantizada por PostgreSQL con transacciones ACID
- **Rendimiento optimizado** — operaciones <500ms con caché Redis (>80% hit rate)
- **Simplicidad estratégica**: 4 casos de uso esenciales que cubren el 100% del flujo de valor

---

## Próximos Pasos

- **Revisar** los casos de uso con el equipo de producto y validar requisitos de negocio
- **Aprobar** el presupuesto inicial de infraestructura Azure AKS (~$300–$400 USD/mes)
- **Definir** el cronograma de desarrollo, sprints y entregables del MVP
- **Establecer** métricas de éxito: tasa de adopción, retención y SLA cumplido

> Para mayor detalle técnico, consultar `ARCHITECTURE.md`

**Equipo de Arquitectura** — v1.0.0 | Febrero 2026