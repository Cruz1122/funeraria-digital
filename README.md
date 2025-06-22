# Sistema Funerario - Arquitectura de Microservicios

Este ecosistema de aplicaciones está conformado por un conjunto de **4 microservicios** y un **frontend** desacoplados en distintos repositorios. En conjunto, permiten la gestión integral de un sistema funerario moderno.

## Repositorios Principales

| Módulo            | Tecnología           | Descripción                                                                                 |
| ----------------- | -------------------- | ------------------------------------------------------------------------------------------- |
| Frontend          | Angular 15           | Interfaz de usuario modular, conectada a todos los microservicios.                          |
| Seguridad         | LoopBack 4 + MongoDB | Autenticación, autorización, roles, 2FA, gestión de usuarios y permisos.                    |
| Lógica de Negocio | LoopBack 4 + MySQL   | Gestión de clientes, beneficiarios, planes, pagos, servicios funerarios, y sistema de PQRS. |
| Notificaciones    | Flask + AWS SES/SNS  | Envío de correos y SMS. Integración con AWS para notificaciones.                            |
| Chat (prototipo)  | Express + Socket.IO  | Chat en tiempo real. Actualmente **no funcional ni seguro**. Solo con fines de referencia.  |

---

# 1. Frontend - [frontend-funeraria](https://github.com/Cruz1122/frontend-funeraria)

Aplicación Angular que centraliza la interacción del usuario con el sistema:

* **Diseño responsive** con Materialize CSS.
* **Autenticación con 2FA** y cifrado MD5 en el cliente.
* **Lazy loading** por módulos (seguridad, lógica, chat, reportes, ventas).
* **Chat en tiempo real** (Socket.IO).
* **reCAPTCHA v2**, paginación, carga de imágenes, gestión de planes, clientes y beneficiarios.

> 🔗 Conexiones esperadas: `http://localhost:3000` (lógica), `:3001` (seguridad), `:3002` (chat).

---

# 2. Microservicio de Seguridad - [ms-seguridad-funeraria.2024](https://github.com/cruz1122/ms-seguridad-funeraria.2024)

Responsable de:

* **Login con JWT** y validación 2FA por correo/SMS.
* **Registro de usuarios** con validación por email.
* **Roles y permisos (RBAC)** completos.
* **Auditoría** mediante `RegistroAcciones`.

**Modelo de datos** en MongoDB: Usuario, Rol, Permisos, Login, RegistroAcciones, RolxPermisos.

> 🔐 Requiere `.env` con: `SECRET_PASSWORD_JWT`, `CONNECTION_STRING_MONGODB`.

---

# 3. Microservicio de Lógica de Negocio - [ms-logica-negocio-funeraria](https://github.com/cruz1122/ms-logica-negocio-funeraria)

Encargado de la gestión central del negocio funerario:

* Clientes y Beneficiarios.
* Planes y Servicios funerarios.
* Facturación y Pagos.
* Subida de archivos.
* Sistema de PQRS con notificaciones.

**Tecnologías**: LoopBack 4 + MySQL.

**Configuraciones externas**:

* Notificaciones: `notificaciones.config.ts`
* Seguridad: `configuracion.seguridad.ts`

> ✉️ Integración con el microservicio de Notificaciones (correo/SMS).

---

# 4. Microservicio de Notificaciones - [ms-notificaciones-funeraria.2024](https://github.com/cruz1122/ms-notificaciones-funeraria.2024)

Responsable del envío de mensajes salientes:

* **Email (SES)** y **SMS (SNS)** usando `boto3`.
* Plantillas personalizadas HTML.
* Endpoints: `/email`, `/email-2fa`, `/sms`, `/sms-2fa`.

> 🚫 Requiere `.env` con `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_REGION`.

---

# 5. Microservicio de Chat \[Prototipo] - [ms-chat-funeraria.2024](https://github.com/cruz1122/ms-chat-funeraria.2024)

**⚠️ AVISO: Este microservicio es inseguro y no está listo para producción.**

Incluye:

* Salas de chat.
* Mensajes generales y privados.
* Persistencia en MySQL (insegura).
* Eventos: `join`, `message`, `privateMessage`, `loadMessages`, `blockUser`.

**Problemas graves:**

* SQL Injection.
* Estado volátil en RAM.
* Credenciales expuestas.
* No hay autenticación real de usuarios.

---

# Recomendaciones Generales

* Separar entornos de desarrollo y producción.
* Usar Docker y CI/CD para despliegues consistentes.
* Validar integración segura entre servicios (CORS, HTTPS, tokens).
* Reescribir microservicio de chat desde cero si se desea funcionalidad real.
* Documentar las rutas en Swagger/OpenAPI en cada microservicio.

---

# Despliegue Local (Recomendado para Desarrollo)

1. Clonar cada repositorio.
2. Instalar dependencias (`npm install`, `pip install`, etc.).
3. Configurar archivos `.env` según documentación.
4. Levantar servicios en el orden:

   1. Notificaciones
   2. Seguridad
   3. Lógica de Negocio
   4. Chat (si se prueba)
   5. Frontend

---

# Licencia

Este proyecto es de uso educativo y no está listo para entornos productivos sin validaciones adicionales.
