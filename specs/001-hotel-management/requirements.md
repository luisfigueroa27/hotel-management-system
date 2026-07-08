# Requisitos del Sistema — Sistema de Gestión Hotelera

**Versión:** 1.0  
**Fecha:** 2026-07-08  
**Estado:** Borrador  
**Referencia:** [Visión del Proyecto](./vision.md)

---

## Convenciones

| Prefijo | Tipo | Prioridad |
|---|---|---|
| `RF-XXX` | Requisito Funcional | — |
| `RNF-XXX` | Requisito No Funcional | — |
| **[ALTA]** | Indispensable para v1.0 | Bloquea el lanzamiento |
| **[MEDIA]** | Importante pero no bloqueante | Puede entrar en iteración 2 |
| **[BAJA]** | Mejora deseable | Backlog futuro |

---

## 1. Requisitos Funcionales

### 1.1 Módulo: Autenticación y Sesión

| ID | Requisito | Prioridad |
|---|---|---|
| RF-001 | El sistema debe permitir el inicio de sesión mediante usuario y contraseña. | ALTA |
| RF-002 | El sistema debe cerrar sesión automáticamente tras 30 minutos de inactividad. | ALTA |
| RF-003 | El sistema debe permitir al administrador restablecer la contraseña de cualquier usuario. | ALTA |
| RF-004 | El sistema debe registrar la fecha, hora y dirección IP de cada inicio de sesión exitoso y fallido. | MEDIA |
| RF-005 | El sistema debe bloquear una cuenta tras 5 intentos fallidos consecutivos de inicio de sesión. | MEDIA |

---

### 1.2 Módulo: Usuarios y Roles

| ID | Requisito | Prioridad |
|---|---|---|
| RF-010 | El sistema debe permitir al administrador crear, editar, desactivar y eliminar cuentas de usuario. | ALTA |
| RF-011 | El sistema debe soportar los roles: Administrador, Gerente, Recepcionista, Cajero y Housekeeping. | ALTA |
| RF-012 | Cada usuario debe tener asignado exactamente un rol. | ALTA |
| RF-013 | El acceso a cada módulo y operación debe estar restringido según el rol del usuario. | ALTA |
| RF-014 | El sistema debe impedir que un usuario modifique su propio rol o nivel de acceso. | ALTA |
| RF-015 | El sistema debe registrar en auditoría toda creación, modificación o desactivación de usuario. | MEDIA |

**Matriz de permisos por rol:**

| Módulo / Operación | Admin | Gerente | Recepcionista | Cajero | Housekeeping |
|---|:---:|:---:|:---:|:---:|:---:|
| Gestión de habitaciones (CRUD) | ✓ | — | — | — | — |
| Ver disponibilidad de habitaciones | ✓ | ✓ | ✓ | ✓ | ✓ |
| Actualizar estado de limpieza | ✓ | — | — | — | ✓ |
| Gestión de huéspedes (CRUD) | ✓ | — | ✓ | — | — |
| Crear/modificar/cancelar reservas | ✓ | — | ✓ | — | — |
| Check-In | ✓ | — | ✓ | — | — |
| Check-Out | ✓ | — | ✓ | — | — |
| Registrar pagos | ✓ | — | — | ✓ | — |
| Emitir facturas | ✓ | — | — | ✓ | — |
| Registrar servicios adicionales | ✓ | — | ✓ | ✓ | — |
| Ver Dashboard | ✓ | ✓ | ✓ | ✓ | — |
| Generar reportes | ✓ | ✓ | — | — | — |
| Gestión de usuarios | ✓ | — | — | — | — |
| Configuración del sistema | ✓ | — | — | — | — |

---

### 1.3 Módulo: Gestión de Habitaciones

| ID | Requisito | Prioridad |
|---|---|---|
| RF-020 | El sistema debe permitir registrar habitaciones con: número, tipo, piso, capacidad máxima, descripción y precio base por noche. | ALTA |
| RF-021 | El sistema debe soportar los tipos de habitación: Individual, Doble, Suite, Suite Junior, Familiar. | ALTA |
| RF-022 | Cada habitación debe tener uno de los siguientes estados: Disponible, Ocupada, En limpieza, Fuera de servicio. | ALTA |
| RF-023 | El sistema debe mostrar un mapa o listado del estado actual de todas las habitaciones en tiempo real. | ALTA |
| RF-024 | El sistema debe permitir al Housekeeping actualizar el estado de limpieza de una habitación (En limpieza → Disponible). | ALTA |
| RF-025 | El sistema debe impedir crear una reserva para una habitación con estado Fuera de servicio. | ALTA |
| RF-026 | El sistema debe permitir registrar fotografías y amenidades de cada habitación. | BAJA |
| RF-027 | El sistema debe permitir definir tarifas especiales por temporada o tipo de cliente. | MEDIA |

---

### 1.4 Módulo: Gestión de Huéspedes

| ID | Requisito | Prioridad |
|---|---|---|
| RF-030 | El sistema debe permitir registrar el perfil del huésped con: nombre completo, tipo y número de documento, nacionalidad, fecha de nacimiento, teléfono, correo electrónico y dirección. | ALTA |
| RF-031 | El número de documento debe ser único en el sistema; no pueden existir dos huéspedes con el mismo tipo y número. | ALTA |
| RF-032 | El sistema debe permitir buscar huéspedes por nombre, número de documento o correo electrónico. | ALTA |
| RF-033 | El sistema debe mostrar el historial completo de reservas y estadías de cada huésped. | ALTA |
| RF-034 | El sistema debe registrar la fecha de alta y el usuario que creó o modificó cada perfil de huésped. | MEDIA |
| RF-035 | El sistema debe permitir marcar un huésped como VIP o con observaciones especiales. | BAJA |

---

### 1.5 Módulo: Reservas

| ID | Requisito | Prioridad |
|---|---|---|
| RF-040 | El sistema debe permitir crear una reserva indicando: huésped, habitación, fecha de llegada, fecha de salida, número de personas y observaciones. | ALTA |
| RF-041 | El sistema debe validar la disponibilidad de la habitación para el rango de fechas solicitado antes de confirmar la reserva. | ALTA |
| RF-042 | El sistema debe calcular automáticamente el costo total estimado de la reserva basado en las noches y la tarifa de la habitación. | ALTA |
| RF-043 | Cada reserva debe tener uno de los siguientes estados: Pendiente, Confirmada, En curso (check-in realizado), Completada, Cancelada. | ALTA |
| RF-044 | El sistema debe permitir modificar fechas y habitación de una reserva siempre que la nueva habitación esté disponible. | ALTA |
| RF-045 | El sistema debe permitir cancelar una reserva y registrar el motivo de cancelación. | ALTA |
| RF-046 | El sistema debe generar un número de confirmación único para cada reserva. | ALTA |
| RF-047 | El sistema debe enviar una notificación interna al Recepcionista cuando haya check-ins programados para el día actual. | MEDIA |
| RF-048 | El sistema debe permitir registrar una reserva para un grupo (múltiples habitaciones, mismo huésped responsable). | MEDIA |

---

### 1.6 Módulo: Check-In

| ID | Requisito | Prioridad |
|---|---|---|
| RF-050 | El sistema debe permitir realizar el check-in de una reserva confirmada, cambiando su estado a "En curso". | ALTA |
| RF-051 | Durante el check-in el sistema debe permitir registrar: hora real de llegada, número de acompañantes, observaciones y depósito recibido. | ALTA |
| RF-052 | El sistema debe cambiar el estado de la habitación a "Ocupada" al completar el check-in. | ALTA |
| RF-053 | El sistema debe permitir realizar el check-in directo (walk-in) sin reserva previa, creando automáticamente la reserva. | ALTA |
| RF-054 | El sistema debe validar que la habitación esté en estado "Disponible" antes de permitir el check-in. | ALTA |
| RF-055 | El sistema debe imprimir o mostrar una ficha de bienvenida al completar el check-in. | BAJA |

---

### 1.7 Módulo: Check-Out

| ID | Requisito | Prioridad |
|---|---|---|
| RF-060 | El sistema debe permitir realizar el check-out de una reserva en estado "En curso". | ALTA |
| RF-061 | Al iniciar el check-out, el sistema debe mostrar el resumen de todos los cargos: noches, servicios adicionales y pagos realizados. | ALTA |
| RF-062 | El sistema debe calcular el saldo pendiente de pago al momento del check-out. | ALTA |
| RF-063 | El sistema debe impedir completar el check-out si existe un saldo pendiente, salvo autorización del Administrador. | ALTA |
| RF-064 | Al completar el check-out, el sistema debe cambiar el estado de la habitación a "En limpieza" y la reserva a "Completada". | ALTA |
| RF-065 | El sistema debe generar y mostrar la factura final al completar el check-out. | ALTA |

---

### 1.8 Módulo: Pagos

| ID | Requisito | Prioridad |
|---|---|---|
| RF-070 | El sistema debe permitir registrar pagos parciales o totales asociados a una reserva. | ALTA |
| RF-071 | El sistema debe soportar los métodos de pago: Efectivo, Tarjeta de crédito, Tarjeta de débito, Transferencia bancaria. | ALTA |
| RF-072 | El sistema debe mostrar el historial de pagos de cada reserva con: fecha, monto, método y usuario que registró el pago. | ALTA |
| RF-073 | El sistema debe permitir registrar un depósito o anticipo al momento de la reserva o del check-in. | ALTA |
| RF-074 | El sistema debe generar una factura o comprobante de pago exportable en PDF. | ALTA |
| RF-075 | El sistema debe permitir anular un pago, registrando el motivo y el usuario que lo autorizó. | MEDIA |
| RF-076 | El sistema debe soportar el cobro en moneda local y permitir configurar la moneda del sistema. | MEDIA |

---

### 1.9 Módulo: Servicios Adicionales

| ID | Requisito | Prioridad |
|---|---|---|
| RF-080 | El sistema debe permitir al administrador configurar un catálogo de servicios adicionales con nombre, descripción y precio. | ALTA |
| RF-081 | El sistema debe permitir agregar servicios adicionales a una reserva activa (en curso). | ALTA |
| RF-082 | Cada cargo por servicio adicional debe quedar vinculado a la reserva y reflejarse en la factura final. | ALTA |
| RF-083 | El sistema debe permitir registrar la fecha, hora y cantidad de cada servicio adicional consumido. | ALTA |
| RF-084 | El sistema debe permitir al Recepcionista y al Cajero registrar servicios adicionales. | ALTA |

---

### 1.10 Módulo: Dashboard

| ID | Requisito | Prioridad |
|---|---|---|
| RF-090 | El dashboard debe mostrar el porcentaje de ocupación actual del hotel. | ALTA |
| RF-091 | El dashboard debe mostrar el número de check-ins y check-outs programados para el día. | ALTA |
| RF-092 | El dashboard debe mostrar el listado de habitaciones con su estado actual (disponible, ocupada, en limpieza, fuera de servicio). | ALTA |
| RF-093 | El dashboard debe mostrar el ingreso total del día (suma de pagos registrados en la fecha actual). | ALTA |
| RF-094 | El dashboard debe mostrar las reservas que tienen check-in programado para hoy y no han sido procesadas. | ALTA |
| RF-095 | El dashboard debe mostrar alertas de reservas próximas a vencer sin check-in (no-show). | MEDIA |
| RF-096 | El dashboard debe actualizarse automáticamente sin necesidad de recargar la página. | MEDIA |

---

### 1.11 Módulo: Reportes

| ID | Requisito | Prioridad |
|---|---|---|
| RF-100 | El sistema debe generar un reporte de ocupación por rango de fechas, mostrando: tasa de ocupación diaria, noches vendidas y habitaciones disponibles. | ALTA |
| RF-101 | El sistema debe generar un reporte de ingresos por rango de fechas, desglosado por tipo de ingreso (habitaciones y servicios adicionales). | ALTA |
| RF-102 | El sistema debe generar un reporte de huéspedes con historial de estadías en un rango de fechas. | ALTA |
| RF-103 | El sistema debe generar un reporte de reservas canceladas con motivo de cancelación. | MEDIA |
| RF-104 | Todos los reportes deben poder exportarse en formato PDF y Excel/CSV. | ALTA |
| RF-105 | El sistema debe generar un log de auditoría exportable con las acciones sensibles realizadas por cada usuario. | MEDIA |

---

## 2. Requisitos No Funcionales

### 2.1 Rendimiento

| ID | Requisito | Prioridad |
|---|---|---|
| RNF-001 | El tiempo de respuesta de las operaciones más frecuentes (consulta de disponibilidad, carga del dashboard) no debe superar los 2 segundos bajo carga normal. | ALTA |
| RNF-002 | El sistema debe soportar al menos 20 usuarios concurrentes sin degradación visible del rendimiento. | ALTA |
| RNF-003 | La generación de reportes complejos no debe superar los 10 segundos. | MEDIA |

### 2.2 Seguridad

| ID | Requisito | Prioridad |
|---|---|---|
| RNF-010 | Las contraseñas deben almacenarse con hash usando un algoritmo seguro (bcrypt o Argon2). | ALTA |
| RNF-011 | Toda la comunicación entre el cliente y el servidor debe estar cifrada mediante HTTPS/TLS. | ALTA |
| RNF-012 | El sistema debe implementar protección contra ataques CSRF en todos los formularios. | ALTA |
| RNF-013 | El sistema debe implementar protección contra inyección SQL mediante el uso de consultas parametrizadas o un ORM. | ALTA |
| RNF-014 | Los tokens de sesión deben ser invalidados al cerrar sesión. | ALTA |
| RNF-015 | El sistema debe registrar en auditoría toda operación sensible: creación/modificación/eliminación de reservas, pagos, usuarios y configuración. | ALTA |

### 2.3 Usabilidad

| ID | Requisito | Prioridad |
|---|---|---|
| RNF-020 | La interfaz de usuario debe estar completamente en español. | ALTA |
| RNF-021 | El sistema debe ser responsivo y funcionar correctamente en pantallas de escritorio (resolución mínima 1280×768). | ALTA |
| RNF-022 | Las operaciones de check-in y check-out deben poder completarse en no más de 5 pasos en pantalla. | ALTA |
| RNF-023 | El sistema debe mostrar mensajes de error descriptivos en lenguaje comprensible para el usuario final. | MEDIA |

### 2.4 Disponibilidad y Confiabilidad

| ID | Requisito | Prioridad |
|---|---|---|
| RNF-030 | El sistema debe tener una disponibilidad mínima del 99% en horario de operación del hotel (6:00 a.m. – 12:00 a.m.). | ALTA |
| RNF-031 | El sistema debe realizar respaldos automáticos de la base de datos al menos una vez al día. | ALTA |
| RNF-032 | El sistema debe mantener la integridad de los datos ante fallos inesperados del servidor (uso de transacciones). | ALTA |

### 2.5 Mantenibilidad y Escalabilidad

| ID | Requisito | Prioridad |
|---|---|---|
| RNF-040 | El código debe seguir una arquitectura modular que permita agregar nuevos módulos sin modificar los existentes. | ALTA |
| RNF-041 | El sistema debe contar con documentación técnica de la API y del modelo de datos. | MEDIA |
| RNF-042 | El sistema debe estar preparado para escalar a un hotel de hasta 500 habitaciones sin cambios arquitectónicos mayores. | MEDIA |

---

## 3. Requisitos de Datos

| ID | Requisito | Prioridad |
|---|---|---|
| RD-001 | El sistema debe conservar el historial de reservas completadas y canceladas de forma permanente (no se eliminan físicamente). | ALTA |
| RD-002 | Los registros de pago y facturación no pueden eliminarse; solo pueden anularse con trazabilidad. | ALTA |
| RD-003 | El log de auditoría debe conservarse por un mínimo de 2 años. | MEDIA |
| RD-004 | Los perfiles de huéspedes deben poder desactivarse pero no eliminarse mientras tengan historial de reservas. | ALTA |

---

## 4. Restricciones Técnicas

- **Backend:** API RESTful con autenticación mediante JWT (JSON Web Tokens).
- **Base de datos:** Relacional (PostgreSQL o MySQL); uso obligatorio de transacciones para operaciones financieras.
- **Frontend:** Aplicación web de página única (SPA) con soporte para los últimos 2 versiones de Chrome, Firefox y Edge.
- **Exportación:** Los reportes deben generarse del lado del servidor; el cliente solo descarga el archivo resultante.
- **Zona horaria:** El sistema debe operar en la zona horaria configurada para el hotel; todas las fechas se almacenan en UTC.

---

*Documento anterior: [Visión del Proyecto](./vision.md)*  
*Siguiente documento: [Modelo de Dominio →](./domain-model.md)*
