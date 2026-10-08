# AppLogin - Sistema Móvil de Autenticación en 3 Capas

Sistema móvil nativo cliente-servidor para el **registro e inicio de sesión de usuarios** mediante el **RUT chileno**, procesado a través de una arquitectura desacoplada en 3 capas (Android + PHP en Raspbian + AWS RDS MySQL).

---

##  Arquitectura del Sistema

El flujo de información entre las 3 capas se realiza de forma asíncrona:

```
[ App Android (Kotlin) ] ──(HTTP POST / Volley)──> [ VM Raspbian (Apache + PHP) ] ──(MySQL 3306)──> [ AWS RDS MySQL ]
```

### Roles de cada capa:
1. **Capa 1 (Frontend Móvil):** Aplicación Android nativa desarrollada en Kotlin con Material Design 3.
2. **Capa 2 (Backend / Servidor de Aplicación):** Servidor Apache + PHP en una Máquina Virtual con Raspbian Linux.
3. **Capa 3 (Persistencia de Datos):** Base de Datos Relacional MySQL/MariaDB alojada en Amazon Web Services (AWS RDS).

---

##  Características Principales

###  Frontend (Android - Kotlin)
- **Formateador de RUT en tiempo real:** Uso de `TextWatcher` que intercepta la entrada del usuario e inserta automáticamente los puntos y el guion (ejemplo: `12.345.678-K`).
- **Normalización de datos:** Al presionar registrar/ingresar, la función `obtenerRutParaBackend()` limpia únicamente los puntos (ej: `12345678-K`) antes de enviar la petición HTTP.
- **Diseño Responsivo:** Límite máximo de ancho (`max_width = 450dp`), `MaterialCardView` flotante y contenedor `ScrollView` con `fillViewport="true"` para soporte completo de teclado virtual.
- **Peticiones HTTP Asíncronas:** Uso de la librería **Volley** para peticiones POST no bloqueantes.
- **Visibilidad de Contraseña:** Toggle interactivo `password_toggle` para mostrar/ocultar la clave.

###  Backend (PHP en Raspbian)
- **Cifrado de Contraseñas:** Algoritmo **Bcrypt** con `password_hash()` y `password_verify()`. Las claves nunca se almacenan en texto plano.
- **Prevención de Inyección SQL:** Uso estricto de **Sentencias Preparadas** (`prepare` / `bind_param`) en todas las consultas a la base de datos.
- **Respuestas JSON:** Respuestas estandarizadas (`status`, `message`) retornadas con codificación UTF-8.

### ☁️ Base de Datos (AWS RDS MySQL)
- **Estructura de la Tabla `usuarios`:**
  - `id`: `INT AUTO_INCREMENT PRIMARY KEY`
  - `rut`: `VARCHAR(20) NOT NULL UNIQUE`
  - `password`: `VARCHAR(255) NOT NULL` (Hash Bcrypt de 60 caracteres)
  - `created_at`: `TIMESTAMP DEFAULT CURRENT_TIMESTAMP`

---

##  Configuración de Red

- **Modo Adaptador Puente (Bridged):** La Máquina Virtual de Raspbian pertenece a la misma subred local que el teléfono móvil.
- **IP Estática:** Asignación de IP fija en Raspbian (`/etc/dhcpcd.conf`) para mantener la persistencia de las URLs de consumo de la API.
- **Seguridad en AWS:** Regla de entrada en el VPC Security Group de AWS para permitir tráfico entrante por el puerto `3306`.

---

##  Tecnologías Utilizadas

- **Kotlin** & **ConstraintLayout / Material Design 3**
- **Volley HTTP Library** (Android)
- **PHP 8.x** & **Apache Web Server**
- **Raspbian Linux** (Máquina Virtual)
- **AWS RDS MySQL** (Amazon Web Services)
- **MySQL Workbench**

---

##  Proyecto y Licencia
Proyecto desarrollado para fines educativos y demostración técnica de arquitectura móvil distribuida en 3 capas.
