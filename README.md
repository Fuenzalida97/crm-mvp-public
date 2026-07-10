# CRM COMERCIAL - MVP 🚗💼

Sistema de Gestión de Relaciones con Clientes (CRM) enfocado en optimizar el pipeline de ventas de pequeñas y medianas empresas. Permite gestionar el ciclo de vida completo de una oportunidad comercial, desde el contacto inicial hasta el cierre del negocio, asignación de ejecutivos, bitácora de interacciones, importación masiva de datos con respaldo en la nube, y un sistema avanzado de recuperación de datos (Soft Delete).

*La demo y los datos de ejemplo están tematizados en torno a una concesionaria automotriz, pero el modelo de datos es genérico y adaptable a otros rubros de venta B2B/B2C.*

---

## 🚀 Stack Tecnológico

El proyecto está construido bajo una arquitectura desacoplada utilizando herramientas modernas y tipado estricto de extremo a extremo:

* **Backend:** NestJS (Framework modular progresivo de Node.js) & TypeScript.
* **Frontend:** Next.js (App Router), React & Tailwind CSS.
* **Base de Datos:** PostgreSQL.
* **Almacenamiento en la Nube:** AWS S3 (`@aws-sdk/client-s3`, `@aws-sdk/s3-request-presigner`) para respaldo de archivos importados y descargas mediante URLs firmadas temporales.
* **Gestión de Dependencias:** pnpm.
* **Infraestructura:** Docker & Docker Compose (para consistencia en entornos de desarrollo).

---

## 🏛️ Arquitectura y Lógica de Negocio

### Backend (NestJS)
Diseñado bajo los principios de **Arquitectura Modular**. Cada dominio (*Users, Customers, Opportunities, Interactions, Import*) está encapsulado en su propio módulo, garantizando alta cohesión y bajo acoplamiento:
* **Controladores:** Exposición de endpoints RESTful limpios y validados mediante `class-validator`.
* **Servicios:** Capa de lógica de negocio pura aislada de la infraestructura.
* **Persistencia:** Repositorios optimizados mediante queries relacionales, filtros dinámicos (búsqueda, rango de fechas, rango de montos) y paginación nativa controlada por metadatos (`limit`, `offset`, `totalCount`, `lastPage`).
* **Consistencia Transaccional Multi-Sistema:** patrón de "zona de espera" en S3 (`pending/` → `finalize`) coordinado con transacciones de base de datos (`QueryRunner`). Un archivo importado y los registros que genera se confirman o revierten como una sola unidad atómica, incluso cuando hay dos sistemas de almacenamiento distintos involucrados (S3 + PostgreSQL).
* **Seguridad:** Autenticación robusta mediante JSON Web Tokens (JWT) con guardianes (`Guards`) para control de acceso basado en roles (`admin` / `user`), y URLs de descarga firmadas con expiración corta para archivos sensibles en S3.

### Frontend (Next.js & React)
Implementado bajo un enfoque de **Dashboard SPA (Single Page Application)** dinámico:
* **Custom Hooks & Control de Flujo:** Consumo asíncrono optimizado mediante envolturas `useCallback` y control de desmontaje (`isMounted`) para mitigar fugas de memoria y *cascading renders* innecesarios.
* **Filtros y Búsqueda Server-Side:** patrón de debounce reutilizado de forma consistente en todos los módulos administrativos (clientes, oportunidades, interacciones, equipo, historial de importaciones), evitando llamadas innecesarias al backend mientras el usuario escribe.
* **Optimización UI/UX:** Refrescos silenciosos de datos en segundo plano tras mutaciones exitosas (POST/PATCH/DELETE) para evitar parpadeos visuales, combinados con estados de carga basados en *Skeletons* de alta fidelidad.

---

## 📊 Modelo de Datos (Esquema de BD)

El diseño relacional asegura la integridad referencial y un histórico fidedigno de interacciones e importaciones.

![Database Schema](./docs/images/schema.png)

---

## 🔥 Funcionalidades Clave

### 1. Panel de Control (Dashboard) y KPIs en Tiempo Real
Visualización instantánea del estado comercial del negocio. Incluye tarjetas de métricas clave (clientes totales, seguimientos pendientes) y un desglose dinámico del valor monetario del pipeline según la etapa de negociación, formateado para la moneda local.

![Vista del Dashboard](./docs/images/dashboard.png)

### 2. Importación Masiva de Datos con Respaldo en la Nube
Carga de clientes y oportunidades desde archivos Excel (.xlsx) o CSV, con un flujo diseñado para minimizar errores en cargas masivas:
* Vista previa y mapeo manual de columnas del archivo hacia los campos del sistema.
* Validación fila por fila en el backend (formato, campos obligatorios, duplicados, referencias a clientes existentes), con mensajes de error específicos por número de fila.
* Confirmación **todo o nada**: si una sola fila falla, no se guarda ningún registro ni se mueve el archivo en S3.
* Historial de importaciones auditable (solo administradores): quién subió qué archivo, cuándo, cuántas filas, con descarga del archivo original vía URL firmada, búsqueda, filtro por tipo, rango de fechas y paginación.

![Vista de Importación](./docs/images/import-flow.png)
![Historial de Importaciones](./docs/images/import-history.png)

### 3. Pipeline de Ventas y Búsqueda Predictiva
Listado centralizado para la gestión de oportunidades, con indicadores visuales (badges) para clasificar rápidamente la prioridad (Alta, Media, Baja) y la etapa del negocio. Incorpora búsqueda por negocio o cliente, filtros por etapa, prioridad y rango de monto, y ordenamiento dinámico al hacer clic en las columnas.

![Vista de Ventas](./docs/images/opportunities.png)

### 4. Bitácora de Interacciones Enriquecida
Historial cronológico fundamental para el seguimiento de clientes. Permite registrar cada punto de contacto diferenciando el canal de comunicación (Email, Llamada, WhatsApp, etc.) mediante identificadores visuales, indicando la fecha, la nota descriptiva y qué miembro del equipo registró la actividad. Filtrable por tipo de contacto.

![Vista de Interacciones](./docs/images/interactions.png)

### 5. Gestión de Equipo y Control de Accesos (RBAC)
Módulo de administración para el control de la fuerza de ventas. Implementa seguridad basada en roles (Administrador vs. Usuario estándar), visualización y filtro del estado de las cuentas (Activo/Inactivo), búsqueda por nombre o email, y transferencia masiva de cartera (clientes, oportunidades e interacciones) entre ejecutivos, útil al dar de baja a un miembro del equipo.

![Vista de Gestión de Usuarios](./docs/images/users.png)

### 6. Papelera de Negocios y Recuperación (Soft Delete)
Mecanismo de seguridad para prevenir la pérdida accidental de datos sensibles. Los negocios eliminados no se borran de la base de datos inmediatamente, sino que pasan a una papelera donde un administrador puede auditar, restaurar la oportunidad con un clic, o ejecutar una eliminación definitiva.

![Vista de Papelera](./docs/images/trash.png)

---

## ⚙️ Configuración del Entorno de Desarrollo

### Requisitos Previos
* Node.js (v18 o superior)
* pnpm (`npm i -g pnpm`)
* Docker & Docker Compose
* Cuenta de AWS con un bucket S3 (necesario solo para la funcionalidad de importación de archivos; el resto del sistema funciona sin ella)

### 1. Clonar el repositorio
```bash
git clone https://github.com/tu-usuario/tu-repo-crm.git
cd tu-repo-crm
```

### 2. Variables de Entorno
Configura los archivos `.env` tanto en la raíz del backend como del frontend guiándote por los archivos `.env.example` provistos en cada carpeta. Para la funcionalidad de importación, el backend requiere credenciales de AWS (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_S3_BUCKET`, `AWS_S3_REGION`).

### 3. Levantar Infraestructura (Base de Datos)
```bash
docker-compose up -d
```

### 4. Ejecución del Proyecto

**Para el Backend:**
```bash
cd server
pnpm install
pnpm run start:dev
```

**Para el Frontend (Client):**
```bash
cd client
pnpm install
pnpm run dev
```

---

## 📄 Licencia

Este repositorio es una versión de demostración de uso público, con fines de portafolio y para evidenciar técnica profesional. El código fuente de producción se mantiene en un repositorio privado.
