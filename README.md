# Commercial Automotive CRM 🚗💼

Este es un sistema de Gestión de Relaciones con Clientes (CRM) diseñado específicamente para optimizar el pipeline de ventas en el sector automotriz. La aplicación permite gestionar el ciclo de vida completo de una oportunidad comercial, desde el contacto inicial hasta el cierre del negocio, asignación de ejecutivos, bitácora de interacciones y un sistema avanzado de recuperación de datos (Soft Delete).

---

## 🚀 Stack Tecnológico

El proyecto está construido bajo una arquitectura desacoplada utilizando herramientas modernas y tipado estricto de extremo a extremo:

* **Backend:** NestJS (Framework modular progresivo de Node.js) & TypeScript.
* **Frontend:** Next.js (App Router), React & Tailwind CSS.
* **Base de Datos:** PostgreSQL.
* **Gestión de Dependencias:** pnpm.
* **Infraestructura:** Docker & Docker Compose (para consistencia en entornos de desarrollo).

---

## 🏛️ Arquitectura y Lógica de Negocio

### Backend (NestJS)
Diseñado bajo los principios de **Arquitectura Modular**. Cada dominio (*Users, Customers, Opportunities, Interactions*) está encapsulado en su propio módulo, garantizando alta cohesión y bajo acoplamiento:
* **Controladores:** Exposición de endpoints RESTful limpios y validados mediante `class-validator`.
* **Servicios:** Capa de lógica de negocio pura aislada de la infraestructura.
* **Persistencia:** Repositorios optimizados mediante queries relacionales y paginación nativa controlada por metadatos (`limit`, `offset`, `totalCount`, `lastPage`).
* **Seguridad:** Autenticación robusta implementada mediante JSON Web Tokens (JWT) con guardianes (`Guards`) para el control de acceso basado en roles (`admin` / `user`).

### Frontend (Next.js & React)
Implementado bajo un enfoque de **Dashboard SPA (Single Page Application)** dinámico:
* **Custom Hooks & Control de Flujo:** Consumo asíncrono optimizado mediante envolturas `useCallback` y control de desmontaje (`isMounted`) para mitigar fugas de memoria y *cascading renders* innecesarios recomendados por el equipo core de React.
* **Optimización UI/UX:** Refrescos silenciosos de datos en segundo plano tras mutaciones exitosas (POST/PATCH/DELETE) para evitar parpadeos visuales molestos, combinados con estados de carga basados en *Skeletons* de alta fidelidad.

---

## 📊 Modelo de Datos (Esquema de BD)

El diseño relacional asegura la integridad referencial y un histórico fidedigno de interacciones.

[AQUÍ: Inserta el embed o imagen exportada de tu diagrama de dbdiagram.io]
*(Ejemplo de formato: ![Database Schema](https://link-a-tu-imagen.com/schema.png))*

---

## 🔥 Funcionalidades Clave

### 1. Pipeline de Ventas Automotriz (Kanban / List)
Visualización clara del embudo de ventas filtrado por etapas del negocio. Clasificación automática por prioridades (Alta, Media, Baja) y cálculo localizado de montos financieros.

*[AQUÍ: Insertar Captura 1 - Vista General del Pipeline]*

### 2. Bitácora de Interacciones Enriquecida
Historial unificado y cronológico de cada punto de contacto con el cliente (Llamadas, WhatsApp, Emails, Reuniones Presenciales) enlazado directamente a las oportunidades activas.

*[AQUÍ: Insertar Captura 2 - Formulario y Bitácora de Interacciones]*

### 3. Sistema de Papelera y Recuperación Avanzada (Soft Delete)
Mecanismo de seguridad para datos sensibles. Los registros eliminados por ejecutivos van a una papelera intermedia donde un administrador puede restaurar la oportunidad/cliente o ejecutar un borrado definitivo de forma segura.

*[AQUÍ: Insertar Captura 3 - Papelera de Negocios y Roles de Admin]*

### 4. Búsqueda con Debounce y Paginación Server-Side
Filtrado predictivo reactivo en tiempo real que optimiza las peticiones al servidor retrasando la ejecución del fetch hasta que el usuario finaliza la escritura.

---

## ⚙️ Configuración del Entorno de Desarrollo

### Requisitos Previos
* Node.js (v18 o superior)
* pnpm (`npm i -g pnpm`)
* Docker & Docker Compose

### 1. Clonar el repositorio
```bash
git clone https://github.com/tu-usuario/tu-repo-crm.git
cd tu-repo-crm
```

### 2. Variables de Entorno
Configura los archivos `.env` tanto en la raíz del backend como del frontend guiándote por los archivos `.env.example` provistos en cada carpeta.

### 3. Levantar Infraestructura (Base de Datos)
```bash
docker-compose up -d
```

### 4. Ejecución del Proyecto

**Para el Backend:**
```bash
cd backend
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

Este proyecto es de uso público exclusivamente con fines de portafolio académico y demostración técnica profesional.