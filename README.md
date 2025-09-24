# 📖 Backend - Gestión Eclesial

Este proyecto es un **sistema de administración eclesial** desarrollado en **Flask** con base de datos **MySQL**, que incluye autenticación con **JWT**, control de usuarios, gestión de habitantes y registro de sacramentos.

---

## 🚀 Tecnologías

- **Python 3.10+**
- **Flask**
- **Flask-JWT-Extended**
- **Flask-CORS**
- **MySQL**
- **dotenv**
- **Werkzeug** (para seguridad y utilidades)

---

## 📂 Estructura de Carpetas

```
Backend/
│── app.py                 # Punto de entrada principal
│── config.py              # Configuraciones (dev, prod, testing)
│── database/
│    ├── __init__.py
│    └── db_mysql.py       # Conexión y helpers MySQL
│── routes/
│    ├── __init__.py
│    ├── auth.py           # Rutas de autenticación (login, registro, perfil)
│    ├── usuarios.py       # CRUD de usuarios
│    ├── habitantes.py     # CRUD de habitantes + asociación con sacramentos
│    └── sacramentos.py    # CRUD y asignación de sacramentos
│── utils/
│    ├── logger.py         # Configuración de logs
│    └── security.py       # Funciones auxiliares de seguridad
│── migrations/            # Scripts SQL para inicializar BD
│── .env                   # Variables de entorno
```

---

## ⚙️ Variables de Entorno (.env)

```env
FLASK_ENV=
PORT=
DEBUG=
# DB
DB_HOST=
DB_USER=
DB_PASSWORD=
DB_NAME=

# JWT
JWT_SECRET_KEY=
SECRET_KEY=
```

---

## 🗄️ Base de Datos

### Tabla `usuarios`
```sql
CREATE TABLE usuarios (
    IdUsuario INT AUTO_INCREMENT PRIMARY KEY,
    Nombre VARCHAR(100) NOT NULL,
    Apellido VARCHAR(100) NOT NULL,
    IdTipoDocumento INT NOT NULL,
    NumeroDocumento VARCHAR(50) UNIQUE NOT NULL,
    PasswordHash VARCHAR(255) NOT NULL,
    Rol ENUM('Administrador','Secretaria','Sacerdote') DEFAULT 'Secretaria',
    Activo TINYINT DEFAULT 1,
    FechaRegistro DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### Tabla `habitantes`
```sql
CREATE TABLE habitantes (
    IdHabitante INT AUTO_INCREMENT PRIMARY KEY,
    Nombre VARCHAR(100) NOT NULL,
    Apellido VARCHAR(100) NOT NULL,
    IdTipoDocumento INT NOT NULL,
    NumeroDocumento VARCHAR(50) UNIQUE NOT NULL,
    FechaNacimiento DATE NOT NULL,
    Hijos INT DEFAULT 0,
    Sexo ENUM('Masculino','Femenino') NOT NULL,
    CorreoElectronico VARCHAR(150),
    Telefono VARCHAR(50),
    Direccion VARCHAR(255),
    DiscapacidadParaAsistir VARCHAR(255) DEFAULT 'Ninguna',
    Activo TINYINT DEFAULT 1,
    FechaRegistro DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### Tabla `tiposacramentos`
```sql
CREATE TABLE tiposacramentos (
    IdSacramento INT AUTO_INCREMENT PRIMARY KEY,
    Descripcion VARCHAR(100) NOT NULL,
    Costo DECIMAL(10,2) DEFAULT 0
);
```

### Tabla `habitante_sacramento` (relación N:M)
```sql
CREATE TABLE habitante_sacramento (
    IdHabitante INT NOT NULL,
    IdSacramento INT NOT NULL,
    FechaSacramento DATE NULL,
    PRIMARY KEY (IdHabitante, IdSacramento),
    FOREIGN KEY (IdHabitante) REFERENCES habitantes(IdHabitante),
    FOREIGN KEY (IdSacramento) REFERENCES tiposacramentos(IdSacramento)
);
```

---

## 🔑 Autenticación

- Autenticación con **JWT**
- Login devuelve `token` + `user`
- Middlewares `@jwt_required()` protegen rutas privadas
- Rutas manejan errores de token (expirado, inválido, sin token)

---

## 📌 Endpoints Disponibles

### 🔐 Auth (`/api/auth`)
- `POST /login` → Iniciar sesión
- `POST /register` → Registrar usuario
- `GET /profile` → Ver perfil autenticado

### 👤 Usuarios (`/api/usuarios`)
- `GET /usuarios` → Listar
- `GET /usuarios/<id>` → Ver por ID
- `POST /usuarios` → Crear
- `PUT /usuarios/<id>` → Actualizar
- `PATCH /usuarios/<id>/desactivar` → Desactivar

### 🏠 Habitantes (`/api/habitantes`)
- `GET /habitantes` → Listar
- `GET /habitantes/<id>` → Ver por ID
- `POST /habitantes` → Crear
- `PUT /habitantes/<id>` → Actualizar
- `PATCH /habitantes/<id>/desactivar` → Desactivar
- Incluye lista de sacramentos asociados

### ✝️ Sacramentos (`/api/sacramentos`)
- `GET /sacramentos` → Listar tipos de sacramentos
- `POST /sacramentos` → Crear nuevo tipo
- `POST /sacramentos/asignar` → Asignar sacramento a habitante
- `DELETE /sacramentos/<habitante_id>/<sacramento_id>` → Quitar sacramento

---

## 📝 Logs

- Todos los errores y accesos quedan registrados en `logs/gestion.log`
- Configuración en `utils/logger.py`

---

## ▶️ Cómo Ejecutar

```bash
# Crear entorno
python -m venv venv
source venv/bin/activate   # Linux / Mac
venv\Scripts\activate      # Windows

# Instalar dependencias
pip install -r requirements.txt

# Levantar servidor
python app.py
```

El backend quedará disponible en:  
👉 `http://127.0.0.1:5000`

---

## ✅ Pendientes / Próximos pasos

- Validación avanzada de campos obligatorios (habitantes y usuarios)
- Endpoint de reportes (habitantes registrados por fechas, sacramentos administrados, etc.)
- Roles con permisos diferenciados (Admin, Sacerdote, Secretaria)
