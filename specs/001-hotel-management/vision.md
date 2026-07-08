# Documento de Visión — Sistema de Gestión Hotelera

**Versión:** 1.0  
**Fecha:** 2026-07-08  
**Estado:** Borrador

---

## 1. Problema

Los hoteles de pequeño y mediano tamaño operan con procesos manuales o herramientas fragmentadas (hojas de cálculo, cuadernos físicos, sistemas desconectados) que generan los siguientes problemas:

- **Overbooking y conflictos de disponibilidad** al no contar con un inventario de habitaciones centralizado y en tiempo real.
- **Pérdida de información de huéspedes** por falta de un repositorio único, lo que impide ofrecer un servicio personalizado y dificulta el cumplimiento de normativas locales.
- **Procesos de check-in y check-out lentos** que generan filas, errores de facturación y mala experiencia al huésped.
- **Control de pagos deficiente**, con riesgo de cobros incorrectos, falta de trazabilidad y dificultad para conciliar ingresos.
- **Ausencia de visibilidad gerencial**: los directivos no tienen acceso a métricas operativas (ocupación, ingresos, tendencias) en tiempo real.
- **Gestión ineficiente de servicios adicionales** (lavandería, room service, spa, etc.) que se registran fuera del expediente de la reserva.
- **Falta de control de accesos**: cualquier empleado puede ver o modificar información sensible por no existir roles diferenciados.

---

## 2. Objetivo

Desarrollar un **Sistema de Gestión Hotelera (SGH) web**, modular y escalable, que digitalice y centralice las operaciones de un hotel, desde la disponibilidad de habitaciones hasta la generación de reportes gerenciales, garantizando:

- Reducción de errores operativos en la gestión de reservas y facturación.
- Experiencia ágil para el personal de recepción durante el check-in y check-out.
- Visibilidad en tiempo real del estado del hotel para la gerencia.
- Trazabilidad completa de pagos y servicios consumidos por cada huésped.
- Seguridad de la información mediante control de roles y permisos.

---

## 3. Alcance

### 3.1 Dentro del alcance

| Módulo | Descripción |
|---|---|
| **Gestión de Habitaciones** | Alta, baja y edición de habitaciones; tipos, precios, estados (disponible, ocupada, en limpieza, fuera de servicio). |
| **Gestión de Huéspedes** | Registro, búsqueda y mantenimiento del perfil del huésped; historial de estadías. |
| **Reservas** | Creación, modificación y cancelación de reservas; control de disponibilidad; confirmación automática. |
| **Check-In** | Registro de llegada, asignación de habitación, captura de documentos y depósito. |
| **Check-Out** | Cierre de estadía, consolidación de cargos, emisión de factura y liberación de habitación. |
| **Pagos** | Registro de pagos parciales y totales, múltiples métodos de pago, historial y conciliación. |
| **Servicios Adicionales** | Registro de consumos extras (room service, lavandería, spa, minibar, etc.) enlazados a la reserva. |
| **Usuarios y Roles** | Gestión de cuentas de usuario; roles predefinidos (Administrador, Recepcionista, Cajero, Gerente, Housekeeping). |
| **Dashboard** | Indicadores clave en tiempo real: ocupación, ingresos del día, check-ins/outs pendientes, habitaciones limpias. |
| **Reportes** | Reportes de ocupación, ingresos, histórico de huéspedes y auditoría exportables en PDF y Excel. |

### 3.2 Fuera del alcance (versión 1.0)

- Canal de reservas online para huéspedes (portal público o integración con OTAs como Booking.com o Expedia).
- Integración con sistemas POS de restaurante o bar del hotel.
- Gestión de mantenimiento preventivo de instalaciones.
- Aplicación móvil nativa para el personal.
- Integración con pasarelas de pago externas (Stripe, PayPal).
- Sistema de fidelización o puntos de recompensa.

---

## 4. Actores del Sistema

| Actor | Tipo | Descripción |
|---|---|---|
| **Administrador** | Usuario interno | Configura el sistema: habitaciones, tarifas, usuarios, roles y parámetros generales. Acceso total. |
| **Gerente** | Usuario interno | Consulta dashboards, genera reportes y supervisa indicadores de negocio. Sin acceso a configuración técnica. |
| **Recepcionista** | Usuario interno | Gestiona reservas, check-in, check-out y el perfil de los huéspedes. Principal usuario operativo. |
| **Cajero** | Usuario interno | Registra pagos, emite facturas y realiza cierre de caja. No puede modificar reservas. |
| **Housekeeping** | Usuario interno | Actualiza el estado de limpieza de las habitaciones. Acceso restringido al módulo de habitaciones. |
| **Huésped** | Actor externo | Persona que realiza una estadía. No tiene acceso directo al sistema en esta versión; sus datos son gestionados por el personal. |
| **Sistema Externo** | Actor externo | Futuros sistemas de integración (correo electrónico para confirmaciones automáticas, futuras OTAs). |

---

## 5. Beneficios Esperados

### Beneficios Operativos
- Eliminación del overbooking mediante un inventario de disponibilidad centralizado y en tiempo real.
- Reducción del tiempo promedio de check-in de ~8 minutos (proceso manual) a menos de 2 minutos.
- Cero pérdida de cargos por servicios adicionales al quedar enlazados directamente a la reserva.

### Beneficios Financieros
- Trazabilidad completa de ingresos con conciliación automática, reduciendo errores de facturación.
- Reportes de ocupación y RevPAR que facilitan decisiones de precios y estrategia comercial.
- Reducción de costos operativos al eliminar papelería y procesos redundantes.

### Beneficios para el Huésped
- Proceso de llegada y salida más rápido y sin errores.
- Facturación clara y detallada de todos los consumos de la estadía.

### Beneficios Gerenciales
- Dashboard con KPIs en tiempo real para tomar decisiones con datos actualizados.
- Histórico de huéspedes para análisis de tendencias y fidelización futura.
- Auditoría completa de acciones del personal para control interno.

---

## 6. Restricciones

### 6.1 Técnicas
- La aplicación debe funcionar como **sistema web** accesible desde navegadores modernos (Chrome, Firefox, Edge) sin necesidad de instalación en el cliente.
- El sistema debe soportar **acceso concurrente** de al menos 20 usuarios simultáneos sin degradación de rendimiento.
- La arquitectura debe ser **modular** para permitir agregar funcionalidades futuras (portal de huéspedes, integraciones) sin reescritura del núcleo.
- Los datos de huéspedes deben **persistir de forma segura**, con contraseñas almacenadas con hash y comunicaciones cifradas (HTTPS).

### 6.2 De Negocio
- El sistema debe adaptarse a las **normativas fiscales y de identificación** aplicables (registro de documento de identidad del huésped obligatorio).
- Los reportes financieros deben poder exportarse en formatos estándar (**PDF y Excel/CSV**) para uso contable.
- Toda acción sensible (modificación de pagos, cancelación de reservas, cambio de tarifas) debe quedar registrada en un **log de auditoría** con usuario, fecha y hora.

### 6.3 De Proyecto
- La versión 1.0 debe estar orientada a un **hotel de hasta 100 habitaciones**.
- El desarrollo se realizará siguiendo **Specification Driven Development (SDD)**: toda funcionalidad debe tener requisitos, diseño y criterios de aceptación definidos antes de la implementación.
- La interfaz de usuario debe estar en **español** como idioma principal.

---

## 7. Criterios de Éxito

El proyecto se considera exitoso cuando:

1. El personal de recepción puede completar un check-in completo en menos de 2 minutos.
2. El sistema muestra disponibilidad de habitaciones en tiempo real sin inconsistencias.
3. Un gerente puede generar un reporte de ocupación mensual en menos de 30 segundos.
4. Ningún usuario accede a módulos fuera de su rol asignado.
5. Todos los cargos de servicios adicionales se reflejan correctamente en la factura final.
6. El sistema opera sin pérdida de datos ante un reinicio no planificado del servidor.

---

*Siguiente documento: [Requisitos del Sistema →](./requirements.md)*
