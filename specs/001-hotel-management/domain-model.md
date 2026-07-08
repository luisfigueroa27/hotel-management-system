# Modelo de Dominio — Sistema de Gestión Hotelera

**Versión:** 1.0  
**Fecha:** 2026-07-08  
**Estado:** Borrador  
**Referencia:** [Requisitos del Sistema](./requirements.md)

---

## 1. Introducción

El modelo de dominio describe las **entidades principales** del negocio, sus atributos, los **valores enumerados** que definen su estado, y las **relaciones** entre ellas. Este documento es la fuente de verdad para el diseño de la base de datos y la capa de dominio del backend.

---

## 2. Entidades del Dominio

### 2.1 Usuario (`User`)

Representa a cualquier miembro del personal que opera el sistema.

| Atributo | Tipo | Restricciones | Descripción |
|---|---|---|---|
| `id` | UUID | PK, único | Identificador interno del sistema |
| `nombre_completo` | String | Requerido, max 120 | Nombre y apellido |
| `email` | String | Requerido, único | Correo de acceso al sistema |
| `password_hash` | String | Requerido | Hash de la contraseña (bcrypt/Argon2) |
| `rol` | Enum `Rol` | Requerido | Rol asignado al usuario |
| `activo` | Boolean | Default: true | Indica si la cuenta está habilitada |
| `intentos_fallidos` | Integer | Default: 0 | Contador de intentos fallidos de login |
| `bloqueado_hasta` | DateTime | Nullable | Fecha hasta la que la cuenta está bloqueada |
| `ultimo_login` | DateTime | Nullable | Fecha y hora del último inicio de sesión exitoso |
| `creado_en` | DateTime | Auto | Fecha de creación del registro |
| `actualizado_en` | DateTime | Auto | Fecha de última modificación |

**Enum `Rol`:**
```
ADMINISTRADOR | GERENTE | RECEPCIONISTA | CAJERO | HOUSEKEEPING
```

---

### 2.2 Habitación (`Room`)

Unidad de alojamiento física que el hotel ofrece a los huéspedes.

| Atributo | Tipo | Restricciones | Descripción |
|---|---|---|---|
| `id` | UUID | PK, único | Identificador interno |
| `numero` | String | Requerido, único | Número o código de la habitación (ej: "101", "Suite-A") |
| `piso` | Integer | Requerido | Piso donde se ubica |
| `tipo` | Enum `TipoHabitacion` | Requerido | Categoría de la habitación |
| `capacidad_max` | Integer | Requerido, ≥ 1 | Número máximo de huéspedes |
| `precio_base` | Decimal | Requerido, > 0 | Precio por noche en moneda local |
| `estado` | Enum `EstadoHabitacion` | Requerido | Estado operativo actual |
| `descripcion` | Text | Nullable | Descripción libre |
| `activa` | Boolean | Default: true | Indica si la habitación está en servicio |
| `creado_en` | DateTime | Auto | Fecha de creación del registro |
| `actualizado_en` | DateTime | Auto | Fecha de última modificación |

**Enum `TipoHabitacion`:**
```
INDIVIDUAL | DOBLE | SUITE_JUNIOR | SUITE | FAMILIAR
```

**Enum `EstadoHabitacion`:**
```
DISPONIBLE | OCUPADA | EN_LIMPIEZA | FUERA_DE_SERVICIO
```

**Transiciones válidas de estado:**

```
DISPONIBLE ──────────────► OCUPADA         (al realizar check-in)
OCUPADA    ──────────────► EN_LIMPIEZA     (al completar check-out)
EN_LIMPIEZA ─────────────► DISPONIBLE      (al completar limpieza)
DISPONIBLE / OCUPADA ────► FUERA_DE_SERVICIO  (por Administrador)
FUERA_DE_SERVICIO ───────► DISPONIBLE      (por Administrador)
```

---

### 2.3 Huésped (`Guest`)

Persona física que realiza o ha realizado una estadía en el hotel.

| Atributo | Tipo | Restricciones | Descripción |
|---|---|---|---|
| `id` | UUID | PK, único | Identificador interno |
| `nombre` | String | Requerido, max 80 | Nombre(s) del huésped |
| `apellido` | String | Requerido, max 80 | Apellido(s) del huésped |
| `tipo_documento` | Enum `TipoDocumento` | Requerido | Tipo de identificación oficial |
| `numero_documento` | String | Requerido | Número del documento de identidad |
| `nacionalidad` | String | Requerido | País de origen o residencia |
| `fecha_nacimiento` | Date | Nullable | Fecha de nacimiento |
| `telefono` | String | Nullable | Número de contacto |
| `email` | String | Nullable | Correo electrónico de contacto |
| `direccion` | String | Nullable | Dirección de residencia |
| `es_vip` | Boolean | Default: false | Marcado como huésped VIP |
| `observaciones` | Text | Nullable | Notas especiales del huésped |
| `activo` | Boolean | Default: true | Indica si el perfil está activo |
| `creado_por` | UUID | FK → User | Usuario que registró el perfil |
| `creado_en` | DateTime | Auto | Fecha de creación |
| `actualizado_en` | DateTime | Auto | Fecha de última modificación |

> **Regla de negocio:** La combinación `(tipo_documento, numero_documento)` debe ser única en el sistema.

**Enum `TipoDocumento`:**
```
DNI | PASAPORTE | CEDULA | RUC | OTRO
```

---

### 2.4 Reserva (`Reservation`)

Acuerdo entre el hotel y un huésped para ocupar una habitación en un rango de fechas determinado.

| Atributo | Tipo | Restricciones | Descripción |
|---|---|---|---|
| `id` | UUID | PK, único | Identificador interno |
| `numero_confirmacion` | String | Único, auto-generado | Código legible (ej: `RES-20260708-001`) |
| `huesped_id` | UUID | FK → Guest, Requerido | Huésped titular de la reserva |
| `habitacion_id` | UUID | FK → Room, Requerido | Habitación asignada |
| `fecha_llegada` | Date | Requerido | Fecha de entrada planificada |
| `fecha_salida` | Date | Requerido, > fecha_llegada | Fecha de salida planificada |
| `num_personas` | Integer | Requerido, ≥ 1 | Número total de huéspedes |
| `estado` | Enum `EstadoReserva` | Requerido | Estado actual de la reserva |
| `costo_estimado` | Decimal | Calculado | Noches × precio_base (sin servicios) |
| `deposito` | Decimal | Default: 0 | Anticipo recibido al momento de reservar |
| `motivo_cancelacion` | Text | Nullable | Requerido si estado = CANCELADA |
| `observaciones` | Text | Nullable | Notas adicionales |
| `creado_por` | UUID | FK → User | Usuario que creó la reserva |
| `creado_en` | DateTime | Auto | Fecha de creación |
| `actualizado_en` | DateTime | Auto | Fecha de última modificación |

**Enum `EstadoReserva`:**
```
PENDIENTE | CONFIRMADA | EN_CURSO | COMPLETADA | CANCELADA
```

**Transiciones válidas de estado:**

```
PENDIENTE   ──► CONFIRMADA   (al confirmar la reserva)
PENDIENTE   ──► CANCELADA    (cancelación antes de confirmar)
CONFIRMADA  ──► EN_CURSO     (al realizar check-in)
CONFIRMADA  ──► CANCELADA    (cancelación antes del check-in)
EN_CURSO    ──► COMPLETADA   (al realizar check-out)
```

> **Regla de negocio:** No puede existir más de una reserva en estado `CONFIRMADA` o `EN_CURSO` para la misma habitación con rangos de fechas que se solapen.

---

### 2.5 Check-In (`CheckIn`)

Registro del evento de llegada real de un huésped al hotel.

| Atributo | Tipo | Restricciones | Descripción |
|---|---|---|---|
| `id` | UUID | PK, único | Identificador interno |
| `reserva_id` | UUID | FK → Reservation, único | Reserva a la que corresponde (1:1) |
| `hora_llegada` | DateTime | Requerido | Hora real de llegada |
| `num_acompanantes` | Integer | Default: 0 | Personas adicionales al titular |
| `deposito_recibido` | Decimal | Default: 0 | Monto del depósito cobrado en el check-in |
| `observaciones` | Text | Nullable | Notas registradas durante el check-in |
| `realizado_por` | UUID | FK → User | Recepcionista que procesó el check-in |
| `creado_en` | DateTime | Auto | Fecha y hora del registro |

---

### 2.6 Check-Out (`CheckOut`)

Registro del evento de salida del huésped y cierre de la estadía.

| Atributo | Tipo | Restricciones | Descripción |
|---|---|---|---|
| `id` | UUID | PK, único | Identificador interno |
| `reserva_id` | UUID | FK → Reservation, único | Reserva a la que corresponde (1:1) |
| `hora_salida` | DateTime | Requerido | Hora real de salida |
| `total_cargos` | Decimal | Calculado | Suma de noches + servicios adicionales |
| `total_pagado` | Decimal | Calculado | Suma de todos los pagos registrados |
| `saldo_final` | Decimal | Calculado | total_cargos - total_pagado |
| `factura_id` | UUID | FK → Invoice | Factura generada al hacer checkout |
| `observaciones` | Text | Nullable | Notas registradas durante el check-out |
| `realizado_por` | UUID | FK → User | Recepcionista/Cajero que procesó el check-out |
| `creado_en` | DateTime | Auto | Fecha y hora del registro |

---

### 2.7 Servicio Adicional (`AdditionalService`)

Catálogo de servicios extras que el hotel ofrece a los huéspedes.

| Atributo | Tipo | Restricciones | Descripción |
|---|---|---|---|
| `id` | UUID | PK, único | Identificador interno |
| `nombre` | String | Requerido, único | Nombre del servicio (ej: "Room Service") |
| `descripcion` | Text | Nullable | Descripción del servicio |
| `precio` | Decimal | Requerido, > 0 | Precio unitario |
| `categoria` | Enum `CategoriaServicio` | Requerido | Categoría del servicio |
| `activo` | Boolean | Default: true | Indica si el servicio está disponible |

**Enum `CategoriaServicio`:**
```
ALIMENTOS | BEBIDAS | LAVANDERIA | SPA | TRANSPORTE | OTROS
```

---

### 2.8 Cargo de Servicio (`ServiceCharge`)

Registro de un consumo de servicio adicional vinculado a una reserva activa.

| Atributo | Tipo | Restricciones | Descripción |
|---|---|---|---|
| `id` | UUID | PK, único | Identificador interno |
| `reserva_id` | UUID | FK → Reservation | Reserva a la que se carga |
| `servicio_id` | UUID | FK → AdditionalService | Servicio consumido |
| `cantidad` | Integer | Requerido, ≥ 1 | Unidades consumidas |
| `precio_unitario` | Decimal | Requerido | Precio al momento del consumo (snapshot) |
| `subtotal` | Decimal | Calculado | cantidad × precio_unitario |
| `fecha_hora` | DateTime | Requerido | Fecha y hora del consumo |
| `observaciones` | String | Nullable | Notas sobre el cargo |
| `registrado_por` | UUID | FK → User | Usuario que registró el cargo |

> **Regla de negocio:** Solo se pueden agregar cargos de servicio a reservas con estado `EN_CURSO`.

---

### 2.9 Pago (`Payment`)

Registro de un pago realizado por el huésped en cualquier punto de la estadía.

| Atributo | Tipo | Restricciones | Descripción |
|---|---|---|---|
| `id` | UUID | PK, único | Identificador interno |
| `reserva_id` | UUID | FK → Reservation | Reserva a la que corresponde el pago |
| `monto` | Decimal | Requerido, > 0 | Monto del pago |
| `metodo_pago` | Enum `MetodoPago` | Requerido | Forma de pago utilizada |
| `referencia` | String | Nullable | Número de transacción o voucher |
| `estado` | Enum `EstadoPago` | Default: REGISTRADO | Estado del pago |
| `anulado_motivo` | Text | Nullable | Requerido si estado = ANULADO |
| `anulado_por` | UUID | FK → User, Nullable | Usuario que anuló el pago |
| `registrado_por` | UUID | FK → User | Cajero que registró el pago |
| `fecha_pago` | DateTime | Requerido | Fecha y hora en que se realizó el pago |
| `creado_en` | DateTime | Auto | Fecha de creación del registro |

**Enum `MetodoPago`:**
```
EFECTIVO | TARJETA_CREDITO | TARJETA_DEBITO | TRANSFERENCIA
```

**Enum `EstadoPago`:**
```
REGISTRADO | ANULADO
```

> **Regla de negocio:** Un pago anulado no se elimina; su monto no se descuenta del saldo.

---

### 2.10 Factura (`Invoice`)

Documento fiscal generado al completar el check-out que consolida todos los cargos.

| Atributo | Tipo | Restricciones | Descripción |
|---|---|---|---|
| `id` | UUID | PK, único | Identificador interno |
| `numero_factura` | String | Único, auto-generado | Número correlativo (ej: `FAC-2026-000001`) |
| `reserva_id` | UUID | FK → Reservation | Reserva facturada |
| `huesped_id` | UUID | FK → Guest | Huésped al que se emite |
| `subtotal_habitacion` | Decimal | Calculado | Total por noches de estadía |
| `subtotal_servicios` | Decimal | Calculado | Total por servicios adicionales |
| `total` | Decimal | Calculado | subtotal_habitacion + subtotal_servicios |
| `total_pagado` | Decimal | Calculado | Suma de pagos válidos |
| `saldo_pendiente` | Decimal | Calculado | total - total_pagado |
| `fecha_emision` | DateTime | Requerido | Fecha y hora de emisión |
| `emitido_por` | UUID | FK → User | Usuario que generó la factura |

---

### 2.11 Log de Auditoría (`AuditLog`)

Registro inmutable de las acciones sensibles realizadas en el sistema.

| Atributo | Tipo | Restricciones | Descripción |
|---|---|---|---|
| `id` | UUID | PK, único | Identificador interno |
| `usuario_id` | UUID | FK → User | Usuario que realizó la acción |
| `accion` | String | Requerido | Código de la acción (ej: `RESERVA_CANCELADA`) |
| `entidad` | String | Requerido | Nombre de la entidad afectada (ej: `Reservation`) |
| `entidad_id` | UUID | Requerido | ID del registro afectado |
| `datos_anteriores` | JSON | Nullable | Estado del registro antes del cambio |
| `datos_nuevos` | JSON | Nullable | Estado del registro después del cambio |
| `ip_origen` | String | Nullable | Dirección IP del cliente |
| `fecha_hora` | DateTime | Requerido | Momento exacto de la acción |

> **Regla de negocio:** Los registros de auditoría son de solo inserción; no pueden modificarse ni eliminarse.

---

## 3. Diagrama de Relaciones (ERD — Notación textual)

```
User ──────────────────────────────────────────────────────────────────
  │ crea/modifica                                                       │
  ▼                                                                     ▼
Guest ──────────────────── (1) ──── Reservation ─────── (1) ──── CheckIn
                                         │                              
                                         │ (1)──────────────────── CheckOut
                                         │                              
                                         │ (1..N) ──────── ServiceCharge ── AdditionalService
                                         │                              
                                         │ (1..N) ──────── Payment
                                         │                              
                                         │ (1) ────────── Invoice
                                         │
Room ─────────────────────── (1) ────────┘

AuditLog ◄── registra todas las operaciones sensibles del sistema
```

**Cardinalidades:**

| Relación | Cardinalidad | Descripción |
|---|---|---|
| Guest → Reservation | 1 : N | Un huésped puede tener múltiples reservas |
| Room → Reservation | 1 : N | Una habitación puede tener múltiples reservas (en distintas fechas) |
| Reservation → CheckIn | 1 : 1 | Cada reserva tiene un único check-in |
| Reservation → CheckOut | 1 : 1 | Cada reserva tiene un único check-out |
| Reservation → ServiceCharge | 1 : N | Una reserva puede tener múltiples cargos de servicio |
| Reservation → Payment | 1 : N | Una reserva puede tener múltiples pagos |
| Reservation → Invoice | 1 : 1 | Cada reserva genera una única factura |
| AdditionalService → ServiceCharge | 1 : N | Un servicio puede estar en múltiples cargos |
| User → AuditLog | 1 : N | Un usuario genera múltiples entradas de auditoría |

---

## 4. Reglas de Negocio Centrales

| ID | Regla |
|---|---|
| RN-001 | No puede existir más de una reserva activa (`CONFIRMADA` o `EN_CURSO`) para la misma habitación en rangos de fechas solapados. |
| RN-002 | No se puede hacer check-in de una habitación que no esté en estado `DISPONIBLE`. |
| RN-003 | No se puede hacer check-out si el saldo pendiente es mayor a cero, salvo autorización expresa del Administrador. |
| RN-004 | Los cargos de servicio solo se pueden agregar a reservas en estado `EN_CURSO`. |
| RN-005 | La combinación `(tipo_documento, numero_documento)` de un huésped debe ser única en el sistema. |
| RN-006 | Un pago anulado conserva su registro; su monto no se computa en el saldo pagado. |
| RN-007 | Los registros de factura y auditoría son inmutables; no se eliminan ni se modifican. |
| RN-008 | El número de personas en una reserva no puede superar la capacidad máxima de la habitación. |
| RN-009 | Una habitación con estado `FUERA_DE_SERVICIO` no puede recibir nuevas reservas. |
| RN-010 | El `costo_estimado` de la reserva se calcula como: `noches × precio_base_habitacion` al momento de crear la reserva; el precio de la habitación puede cambiar sin afectar reservas ya confirmadas. |

---

## 5. Glosario del Dominio

| Término | Definición |
|---|---|
| **Habitación** | Unidad física de alojamiento del hotel identificada por su número. |
| **Huésped** | Persona registrada en el sistema como cliente del hotel, con o sin reserva activa. |
| **Reserva** | Compromiso formal de asignar una habitación a un huésped en un rango de fechas. |
| **Check-In** | Proceso de registro de llegada del huésped y activación de la estadía. |
| **Check-Out** | Proceso de cierre de estadía, conciliación de cargos y liberación de habitación. |
| **Estadía** | Período comprendido entre el check-in y el check-out de un huésped. |
| **Cargo** | Cualquier monto a cobrar asociado a una reserva: noches o servicios adicionales. |
| **Saldo pendiente** | Diferencia entre el total de cargos y el total de pagos válidos de una reserva. |
| **Walk-in** | Huésped que llega al hotel sin reserva previa y solicita habitación en el momento. |
| **No-show** | Reserva confirmada cuyo huésped no se presentó en la fecha de llegada acordada. |
| **RevPAR** | Ingreso por habitación disponible (Revenue Per Available Room); métrica clave de rendimiento hotelero. |

---

*Documento anterior: [Requisitos del Sistema](./requirements.md)*  
*Siguiente documento: [Casos de Uso →](./use-cases.md)*
