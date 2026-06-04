# GUÍA RÁPIDA - Sistema de Reservas de Canchas Deportivas

## 🚀 Cómo Probar los 3 Roles (5 minutos)

### 1️⃣ **CLIENTE/USUARIO** (2 minutos)

#### Opción A: Registro Rápido
```
1. Ir a /register
2. Seleccionar rol: "Cliente"
3. Llenar formulario:
   - Nombre: Juan García
   - Email: juan@example.com
   - Teléfono: +34 912345678
   - Contraseña: password123
4. Click en "Registrar"
5. ✅ Redirige a → /dashboard (Cliente)
```

#### Opción B: Login Rápido (Recomendado)
```
1. Ir a /login
2. Click en botón azul: "Cliente: customer@example.com"
3. El formulario se completa automáticamente
4. Click en "Iniciar Sesión"
5. ✅ Redirige a → /dashboard (Cliente)

Credenciales:
- Email: customer@example.com
- Contraseña: customer123
```

---

### 2️⃣ **DUEÑO DE CANCHA** (2 minutos)

#### Login Rápido
```
1. Ir a /login
2. Click en botón naranja: "Dueño: owner@example.com"
3. El formulario se completa automáticamente
4. Click en "Iniciar Sesión"
5. ✅ Redirige a → /courts (Gestión de Canchas)

Credenciales:
- Email: owner@example.com
- Contraseña: owner123
```

#### Funcionalidades del Owner:
- **Dashboard ejecutivo** con:
  - 4 tarjetas de estadísticas (Ingresos, Reservas, Canchas, Clientes)
  - Gráfico de ingresos semanales
  - Próximas reservas
  - Acciones rápidas

- **CRUD de Canchas** (Gestionar Canchas):
  - ✅ **CREATE**: Botón "Nueva Cancha"
  - ✅ **READ**: Listado de todas las canchas
  - ✅ **UPDATE**: Botón "Editar"
  - ✅ **DELETE**: Botón "Eliminar" con confirmación
  - ✅ **TOGGLE**: Activar/Desactivar cancha

---

### 3️⃣ **ADMINISTRADOR** (1 minuto)

#### Login Rápido
```
1. Ir a /login
2. Click en botón rojo: "Admin: admin@example.com"
3. El formulario se completa automáticamente
4. Click en "Iniciar Sesión"
5. ✅ Redirige a → /admin/dashboard

Credenciales:
- Email: admin@example.com
- Contraseña: admin123
```

---

## 📊 Flujo de Redirección Automática

```
REGISTRO (/register)
    ↓
COMPLETAR PERFIL (/complete-profile)
    ↓
REDIRIGE SEGÚN ROL:
├─ Cliente → /dashboard (Panel de usuario)
├─ Dueño → /courts (Gestión de canchas)
└─ Admin → /admin/dashboard (Panel administrativo)
```

```
LOGIN (/login)
    ↓
VALIDA CREDENCIALES
    ↓
REDIRIGE SEGÚN ROL:
├─ Customer → /dashboard
├─ Owner → /courts
└─ Admin → /admin/dashboard
```

---

## 🔐 Protección de Rutas

Todas las rutas están protegidas con `<ProtectedRoute>`:

```
❌ Si intentas acceder a /courts siendo Cliente:
   → Te redirige a /dashboard (tu dashboard)

❌ Si intentas acceder a /admin/dashboard siendo Owner:
   → Te redirige a /courts (tu panel)

❌ Si intentas acceder sin loguear:
   → Te redirige a /login
```

---

## 📁 Estructura de Carpetas por Rol

```
src/app/pages/
├── user/
│   ├── UserDashboard.tsx      → Panel del cliente
│   ├── MyBookings.tsx          → Mis reservas
│   ├── UserProfile.tsx         → Mi perfil
│   ├── Notifications.tsx       → Notificaciones
│   └── CourtsMap.tsx           → Mapa de canchas
├── owner/
│   ├── OwnerDashboard.tsx      → Dashboard profesional ⭐
│   ├── CourtsList.tsx          → CRUD de canchas ⭐
│   ├── CourtDetail.tsx         → Detalles de cancha
│   └── BookingFlow.tsx         → Flujo de reservas
└── admin/
    ├── AdminDashboard.tsx      → Métricas globales
    ├── AdminCourts.tsx         → Gestión global
    ├── AdminBookings.tsx       → Reservas del sistema
    ├── AdminUsers.tsx          → Usuarios del sistema
    ├── AdminReports.tsx        → Reportes
    ├── AdminCalendar.tsx       → Calendario
    ├── AdminSettings.tsx       → Configuración
    └── AdminPromotions.tsx     → Promociones
```

---

## 🛠️ Tech Stack

- **Framework**: React + TypeScript
- **Router**: React Router v6
- **Estilos**: Tailwind CSS
- **UI Components**: shadcn/ui
- **Gráficos**: Recharts (para Owner Dashboard)
- **State**: localStorage (userStore)
- **Icons**: lucide-react

---

## ✨ Funcionalidades Destacadas

### Owner Dashboard ⭐
- Estadísticas en tiempo real
- Gráfico de ingresos semanales
- Lista de canchas con ingresos
- Próximas reservas
- Acciones rápidas

### CRUD de Canchas ⭐
```
- Crear: Modal con formulario completo
- Editar: Edita datos y servicios
- Eliminar: Con confirmación
- Activar/Desactivar: Cambio de estado
- Buscar: Por nombre o ubicación
- Filtrar: Por deporte
```

---

## 🎯 Próximos Pasos (TODO)

- [ ] Integrar API backend
- [ ] Autenticación real (JWT)
- [ ] Persistencia en base de datos
- [ ] Sistema de pagos
- [ ] Notificaciones en tiempo real
- [ ] Mapa interactivo
- [ ] Chat de soporte

---

## 📞 Soporte

Para más detalles, ver:
- [README.md](../README.md)
- [SISTEMA-3-ROLES.md](../SISTEMA-3-ROLES.md)
- [CORRECCIONES-APLICADAS.md](../CORRECCIONES-APLICADAS.md)

