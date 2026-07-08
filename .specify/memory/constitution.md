# Hotel Management System Constitution

## Core Principles

### I. Specification First
Todo desarrollo debe comenzar con una especificación aprobada. Ninguna funcionalidad será implementada sin requisitos, historias de usuario y criterios de aceptación claramente definidos.

### II. Clean Architecture
El sistema debe mantener una arquitectura modular y desacoplada. El frontend y el backend estarán separados y cada módulo tendrá responsabilidades claramente definidas.

### III. Testing First (Non-Negotiable)
Toda funcionalidad deberá contar con pruebas automatizadas.

Requisitos mínimos:

- Pruebas unitarias para componentes, lógica de negocio y utilidades.
- Pruebas de integración para endpoints y acceso a base de datos.
- Uso obligatorio de Testcontainers para las pruebas de integración.
- Ningún cambio podrá integrarse si rompe las pruebas existentes.

### IV. Security by Default
La seguridad será considerada desde el inicio.

Se deberá implementar:

- Autenticación segura.
- Control de acceso basado en roles.
- Validación de entradas.
- Protección contra inyección SQL y XSS.
- Manejo seguro de credenciales mediante variables de entorno.

### V. Quality and Simplicity
El código deberá seguir los principios:

- SOLID
- DRY
- KISS
- Clean Code

Se priorizarán soluciones simples, mantenibles y escalables.

---

## Technology Standards

### Frontend

- React
- Vite
- TypeScript
- Tailwind CSS
- React Router
- TanStack Query
- React Hook Form
- Zod

### Backend

- Node.js
- Fastify
- Prisma ORM

### Database

- Supabase PostgreSQL

### Testing

- Vitest
- React Testing Library
- Supertest
- Testcontainers

### Deployment

Frontend:

- Vercel

Backend:

- Render

Database:

- Supabase

---

## Development Workflow

Toda funcionalidad deberá seguir el siguiente flujo:

1. Specification
2. Clarification
3. Planning
4. Task Generation
5. Implementation
6. Unit Testing
7. Integration Testing
8. Code Review
9. Deployment

Cada cambio deberá realizarse mediante ramas independientes utilizando Conventional Commits.

---

## Governance

Esta constitución tiene prioridad sobre cualquier decisión de implementación.

Toda modificación importante deberá actualizar la especificación correspondiente.

No se permitirá implementar funcionalidades que no estén descritas en la especificación aprobada.

Las Pull Requests deberán verificar:

- Cumplimiento de la especificación.
- Pruebas exitosas.
- Calidad del código.
- Cobertura mínima del 80% en pruebas unitarias.
- Pruebas de integración aprobadas.

---

**Version**: 1.0.0

**Ratified**: 2026-07-08

**Last Amended**: 2026-07-08