# Feature Specification: Backend AFIP Integration

**Feature Branch**: `001-afip-cae-service`  
**Created**: 2026-05-16  
**Status**: Draft  

## Scope & Objective
Definir la arquitectura y lógica del servicio backend para la interacción con los Web Services de AFIP, priorizando el desacoplamiento y la resiliencia.

## Architecture: Multi-Tenant & Security
Se utilizará Clean Architecture con soporte para Multi-tenancy.
- **Security**: Integración con **Keycloak** vía Spring Security (o similar). Validación de JWT en cada request.
- **Multi-tenancy**: Aislamiento de datos por `tenant_id` (CUIT). El contexto de seguridad debe proveer el `tenant_id` autorizado.
- **Audit Service**: Servicio transversal que registra cada operación de facturación en una tabla de auditoría.

## AFIP Authentication Module (WSAA)
Este submódulo gestiona el acceso de forma aislada por cliente.
- **Gestión de Certificados**: Almacenamiento seguro de claves privadas y certificados por cada CUIT.
- **Onboarding API**:
  - `POST /api/v1/onboarding/csr`: Genera un CSR para un CUIT dado.
  - `POST /api/v1/onboarding/certificate`: Recibe y valida el archivo .crt de AFIP.
- **Cache de TA**: Indexado por CUIT para permitir múltiples sesiones simultáneas con AFIP.

## API Endpoints (Internos/Externos)
### POST `/api/v1/facturas/autorizar`
**Auth**: Requiere Bearer Token (JWT). El usuario debe tener el rol `FACTURADOR` para el CUIT emisor.

### GET `/api/v1/auditoria/comprobantes`
**Auth**: Requiere Bearer Token (JWT) con rol `ADMIN`.
**Response**: Lista detallada de comprobantes emitidos, incluyendo el usuario solicitante.

## Resiliencia y Manejo de Errores
- **Circuit Breaker**: Por cada servicio externo de AFIP (WSAA, WSFE).
- **Fallback Strategy**: Ante Circuit Breaker abierto, el sistema debe informar la indisponibilidad (se evaluará el uso de CAEs precalculados en Fase 2).

## Success Criteria
- **SC-B-001**: El módulo WSAA obtiene el TA en menos de 2 segundos en condiciones normales.
- **SC-B-002**: Las excepciones de AFIP se capturan y transforman en mensajes de dominio legibles.
- **SC-B-003**: No se filtran trazas de error de SOAP/XML hacia el cliente final.
