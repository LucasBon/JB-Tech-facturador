# Feature Specification: Sistema de Facturación AFIP (CAE Service)

**Feature Branch**: `001-afip-cae-service`  
**Created**: 2026-05-16  
**Status**: Draft  
**Input**: User description: "Consolidar un servicio intermedio que interactúe con los Web Services de AFIP (ej. WSFEv1) para simplificar la obtención del CAE y datos complementarios."  
**References**: Documentación oficial AFIP en `docs/manual_desarrollador_COMPG_v3_3.pdf`

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Autorización Manual de Factura (Priority: P1)

Como usuario administrativo, quiero cargar los datos de una factura manualmente en la WebApp para obtener el CAE de AFIP de forma inmediata.

**Why this priority**: Es la funcionalidad básica para que el sistema sea útil para usuarios finales y valide el flujo completo de extremo a extremo.

**Independent Test**: Se puede probar cargando datos válidos en un formulario y verificando que se recibe un CAE y se muestra en pantalla.

**Acceptance Scenarios**:

1. **Given** un usuario autenticado y con permisos para el cliente emisor, **When** completa los datos obligatorios y presiona "Emitir", **Then** el sistema devuelve el CAE y los datos de la factura procesada.
2. **Given** un usuario sin permisos para el cliente emisor, **When** intenta acceder al formulario de carga, **Then** el sistema deniega el acceso con un mensaje de error 403.

---

### User Story 2 - Integración vía API REST (Priority: P1)

Como desarrollador de un sistema externo, quiero enviar datos de facturación a la API REST para automatizar la obtención del CAE.

**Why this priority**: Permite la interoperabilidad con otros sistemas, que es el objetivo principal de este "servicio intermedio".

**Independent Test**: Enviar un POST al endpoint de autorización con un JSON válido y un token de autenticación válido, y recibir un 200 OK con el CAE.

**Acceptance Scenarios**:

1. **Given** un JSON válido y credenciales de API con permisos sobre el emisor, **When** se envía al endpoint de autorización, **Then** el sistema devuelve el CAE y los metadatos de AFIP.
2. **Given** un pedido de autorización, **When** el sistema detecta que no hay un Token de Acceso (TA) de AFIP válido para ese cliente, **Then** el backend gestiona automáticamente el Login CMS (WSAA) de forma transparente.

---

### User Story 3 - Resiliencia ante inestabilidad de AFIP (Priority: P2)

Como usuario del sistema, quiero que el servicio maneje las demoras o caídas de AFIP sin quedar bloqueado o perder datos.

**Why this priority**: La disponibilidad de AFIP es variable; el sistema debe garantizar resiliencia para no afectar la experiencia del usuario.

**Independent Test**: Simular un timeout en el llamado a AFIP y verificar que se aplican los reintentos y eventualmente el Circuit Breaker.

**Acceptance Scenarios**:

1. **Given** que AFIP no responde temporalmente, **When** se solicita un CAE, **Then** el sistema reintenta la operación según la política definida (3 reintentos con backoff exponencial).
2. **Given** que el Circuit Breaker está abierto, **When** se solicita un CAE, **Then** el sistema devuelve un error 503 informando la situación (en futuras iteraciones se evaluará el uso de CAEs precalculados para dar continuidad).

---

### User Story 4 - Onboarding de Clientes y Certificados (Priority: P1)

Como nuevo cliente del sistema, quiero darme de alta y autogestionar mis certificados de AFIP para comenzar a facturar de forma autónoma.

**Why this priority**: Permite el escalado del sistema a múltiples clientes sin intervención manual del administrador del sistema.

**Acceptance Scenarios**:

1. **Given** un usuario administrador del sistema, **When** crea un nuevo cliente, **Then** se genera un espacio de trabajo aislado para dicho contribuyente.
2. **Given** un cliente nuevo, **When** accede al módulo de onboarding, **Then** el sistema lo guía en la generación del CSR (Certificate Signing Request) y la posterior carga del certificado (.crt) emitido por AFIP.

---

### User Story 5 - Auditoría y Reportes (Priority: P2)

Como usuario administrador, quiero ver un reporte de todas las facturas emitidas y quién las solicitó para llevar un control de auditoría.

**Why this priority**: Cumplimiento normativo y control interno de operaciones.

**Acceptance Scenarios**:

1. **Given** un usuario con rol de Admin, **When** accede a la sección de reportes, **Then** visualiza una lista detallada de comprobantes incluyendo: fecha, emisor, receptor, importe, CAE y el usuario que realizó la solicitud.

---

### Edge Cases

- **Certificado Expirado**: El sistema debe detectar si el certificado digital (.crt) ha expirado y notificar antes de intentar el Login CMS.
- **Punto de Venta no habilitado**: Manejar el error específico de AFIP cuando el punto de venta enviado no corresponde al CUIT o no está habilitado para factura electrónica.
- **CUIT inválido**: Validar el algoritmo de CUIT (módulo 11) antes de enviar la solicitud a AFIP.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: El sistema DEBE exponer una API RESTful que reciba parámetros de negocio simplificados y abstraiga la complejidad de AFIP.
- **FR-002**: El sistema DEBE implementar la autenticación automática con AFIP (WSAA) por cada cliente (multi-tenant).
- **FR-003**: El sistema DEBE gestionar y cachear los Tickets de Acceso (TA) de forma aislada por cliente.
- **FR-004**: El sistema DEBE integrar un IdP (Identity Provider) como **Keycloak** para la gestión de usuarios, roles (Admin, Facturador) y permisos por cliente.
- **FR-005**: El sistema DEBE permitir el registro de múltiples clientes (contribuyentes) con aislamiento de datos.
- **FR-006**: El sistema DEBE proporcionar una interfaz de onboarding para la generación de CSR y carga de certificados .crt de AFIP.
- **FR-007**: El sistema DEBE registrar una auditoría completa de cada solicitud de comprobante (Timestamp, Usuario, Cliente, Payload, Respuesta AFIP).
- **FR-008**: El sistema DEBE implementar patrones de resiliencia: Circuit Breaker y Reintentos (3 intentos, backoff exponencial).
- **FR-009**: El sistema DEBE permitir la descarga de reportes temporales de facturación para validación y auditoría.

### Key Entities *(include if feature involves data)*

- **Cliente/Tenant**: Representa un contribuyente (CUIT) con su configuración y certificados.
- **Usuario**: Identidad autenticada vía Keycloak con roles asignados.
- **Comprobante**: Factura emitida con su auditoría asociada.
- **Certificado Digital**: Almacena la clave privada (.key) y pública (.crt) vinculada a un CUIT.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: El 100% de las solicitudes de API deben estar autenticadas y autorizadas mediante JWT emitidos por el IdP.
- **SC-002**: Reducción del tiempo de onboarding de un nuevo CUIT de días a minutos mediante el flujo de autogestión de certificados.
- **SC-003**: El sistema debe registrar el 100% de los intentos de facturación (exitosos y fallidos) en el log de auditoría.
- **SC-004**: Aislamiento total de datos: un usuario del Cliente A nunca debe poder visualizar ni emitir facturas para el Cliente B (salvo que tenga permisos explícitos en ambos).

## Assumptions

- Se utilizará Keycloak como solución estándar para autenticación y autorización.
- El sistema proveerá instructivos integrados basados en el manual `manual_desarrollador_COMPG_v3_3.pdf` para guiar al usuario en AFIP.
- La persistencia local de auditoría se realizará en una base de datos relacional (PostgreSQL sugerido).
- El backend en Java LTS implementará la lógica de multi-tenancy mediante esquemas o discriminadores de datos.
- No se requiere inicialmente la generación de PDF con QR (quedando como roadmap futuro).
