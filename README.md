# Facturador JB Tech

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/version-1.1.1-blue.svg)](.specify/memory/constitution.md)

Servicio intermedio de facturación electrónica diseñado para simplificar la interacción con los Web Services de **AFIP** (WSFEv1), facilitando la obtención del CAE y la gestión de comprobantes para múltiples clientes.

## 🎯 Objetivo del Proyecto

Consolidar una plataforma robusta y escalable que abstraiga la complejidad técnica de AFIP, permitiendo tanto la carga manual de facturas a través de una interfaz moderna como la integración automatizada mediante una API RESTful para sistemas de terceros.

## ✨ Características Principales

- **Multi-tenancy Real**: Aislamiento total de datos entre diferentes contribuyentes (CUITs) mediante esquemas compartidos y seguridad basada en roles.
- **Autenticación AFIP Automatizada**: Gestión inteligente del Login CMS (WSAA), generación de TRA y caché de Ticket de Acceso (TA) para optimizar el rendimiento.
- **Onboarding de Clientes**: Flujo guiado para la autogestión de certificados digitales (.key/.crt) y generación de CSR desde la plataforma.
- **Resiliencia de Grado Industrial**: Implementación de patrones **Circuit Breaker** y **Retries** con backoff exponencial para manejar la inestabilidad de los servicios externos.
- **Auditoría Exhaustiva**: Registro completo de cada operación de facturación con una retención mínima de 10 años para cumplimiento fiscal.
- **API RESTful**: Abstracción de parámetros de negocio complejos a estructuras JSON simples y potentes.

## 🛠️ Stack Tecnológico

| Componente | Tecnología |
| :--- | :--- |
| **Backend** | Java LTS (Spring Boot / Spring Security) |
| **Frontend** | React (Responsive Web Design / PWA) |
| **Autenticación** | Keycloak (Identity Provider / JWT) |
| **Base de Datos** | PostgreSQL |
| **Infraestructura** | Docker & Docker Compose |
| **Gestión de Spec** | Spec Kit |

## 🏗️ Arquitectura y Principios

El proyecto se rige por la **Constitución Facturador JB Tech**, asegurando consistencia y calidad:

- **Clean Architecture**: Clara separación de capas (Domain, Application, Infrastructure) para facilitar el mantenimiento y testing.
- **SOLID Principles**: Diseño orientado a objetos robusto y desacoplado.
- **TDD Mandatory**: Cobertura de tests >80% con énfasis en flujos críticos de usuario.
- **Mobile-First Excellence**: Interfaz optimizada para productividad en dispositivos móviles y eficiencia en escritorio.

## 📂 Estructura del Monorepo

```text
facturador/
├── backend/            # Lógica de negocio y servicios AFIP (Java)
├── frontend/           # Interfaz de usuario React
├── specs/              # Especificaciones funcionales y técnicas
├── docs/               # Manuales de AFIP y documentación adicional
├── .specify/           # Configuración del entorno de desarrollo guiado
└── docker-compose.yml  # Orquestación de servicios (App, DB, Keycloak)
```

## 🚀 Comenzando

### Prerrequisitos
- Docker y Docker Compose instalados.
- Java 17+ (LTS).
- Certificados de prueba de AFIP (para entorno de homologación).

### Configuración Rápida
1. Clonar el repositorio.
2. Configurar las variables de entorno en un archivo `.env` (ver `.env.example`).
3. Levantar la infraestructura:
   ```bash
   docker-compose up -d
   ```

## 📜 Gobernanza y Desarrollo

Este proyecto sigue la metodología **Git-flow**. Todas las contribuciones deben alinearse con los principios definidos en la [Constitución del Proyecto](.specify/memory/constitution.md).

- **Rama Principal**: `main` (Producción)
- **Rama de Desarrollo**: `develop`
- **Metodología de Spec**: Cada nueva funcionalidad debe contar con su especificación técnica en `/specs` antes de la implementación.

---
© 2026 JB Tech. Todos los derechos reservados.
