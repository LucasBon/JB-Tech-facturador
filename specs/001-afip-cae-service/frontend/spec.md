# Feature Specification: Frontend Manual Billing Interface

**Feature Branch**: `001-afip-cae-service`  
**Created**: 2026-05-16  
**Status**: Draft  

## Scope & Objective
Diseñar e implementar una interfaz de usuario intuitiva para la carga manual de facturas electrónicas, minimizando errores de entrada y optimizando la experiencia del usuario administrativo.

## UI/UX Flow: Experiencia Global
La WebApp incorporará los siguientes flujos principales:

1. **Autenticación (Login)**:
   - Integración con Keycloak (Redirect o Formulario).
   - Manejo de sesión y tokens JWT.

2. **Dashboard y Selección de Cliente**:
   - Si un usuario tiene acceso a múltiples clientes (CUITs), se presenta un selector al inicio.
   - Resumen de estado de certificados y últimas facturas.

3. **Onboarding de Cliente (Wizard)**:
   - Paso 1: Datos del Contribuyente.
   - Paso 2: Generación de CSR (botón para descargar).
   - Paso 3: Guía interactiva para AFIP (vínculos al manual oficial).
   - Paso 4: Carga de Certificado .crt y validación.

4. **Carga de Factura (Manual)**:
   - Formulario dinámico con validaciones en tiempo real.
   - Restricciones según el cliente seleccionado.

5. **Auditoría y Reportes (Admin)**:
   - Vista de tabla con filtros por fecha y usuario.
   - Visualización de detalles de auditoría para cada comprobante.

## Validaciones Previas (Frontend)
- **Validación de CUIT**: Algoritmo de dígito verificador.
- **Autorización Local**: Ocultar secciones (ej. Auditoría) si el usuario no tiene el rol `ADMIN`.
- **Integridad de Datos**: Validar que el certificado cargado corresponda al CUIT configurado.

## Manejo de Estados
- **Contexto de Cliente**: Estado global que mantiene el cliente activo seleccionado.
- **Feedback de Resiliencia**: Si el backend informa que el Circuit Breaker está abierto, mostrar un banner de advertencia con el tiempo estimado de recuperación.

## Success Criteria
- **SC-F-001**: Un usuario experimentado puede cargar una factura de 3 ítems en menos de 45 segundos.
- **SC-F-002**: El 100% de los errores de validación local se muestran antes de realizar la llamada al backend.
- **SC-F-003**: La interfaz es totalmente responsive y usable en dispositivos móviles (Mobile-First).
