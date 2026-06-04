# 🏀 SISTEMA DE 3 ROLES - Documentación Técnica

## Visión General

Sistema de Reservas de Canchas Deportivas con soporte para **3 roles diferenciados**:
1. **CLIENTE (Customer)** - Reserva canchas
2. **DUEÑO (Owner)** - Administra sus canchas
3. **ADMIN (Admin)** - Gestiona el sistema completo

---

## 🔐 Arquitectura de Roles

### 1. CLIENTE/CUSTOMER
**Propósito**: Buscar y reservar canchas deportivas

**Funcionalidades**:
- 🔍 Buscar canchas
- 🗺️ Ver mapa de canchas
- 📅 Hacer reservas
- 👁️ Ver mis reservas activas
- 📝 Mi perfil
- 🔔 Notificaciones

**Rutas**:
```
/dashboard              → Panel principal
/my-bookings          → Mis reservas
/profile              → Mi perfil
/notifications        → Notificaciones
/map                  → Mapa de canchas
```

**Permisos**:
- ✅ Acceso a /dashboard
- ❌ No puede acceder a /courts (owner)
- ❌ No puede acceder a /admin (admin)

---

### 2. DUEÑO/OWNER
**Propósito**: Gestionar sus canchas y ver ingresos

**Funcionalidades**:
- 📊 Dashboard con estadísticas
- 💰 Ver ingresos por cancha
- 🎾 Crear, editar, eliminar canchas
- 👁️ Activar/Desactivar canchas
- 📈 Gráficos de ingresos
- 📋 Ver próximas reservas

**Rutas**:
```
/dashboard            → Dashboard ejecutivo ⭐
/courts               → Gestión de canchas (CRUD) ⭐
/courts/:id           → Detalles de cancha
```

**Permisos**:
- ✅ Acceso a /courts
- ✅ Acceso a /dashboard (owner)
- ❌ No puede acceder a /dashboard (customer)
- ❌ No puede acceder a /admin

**Funcionalidades Especiales**:

#### Dashboard del Owner
```
┌─────────────────────────────────────────────┐
│  HEADER: SportBook Owner                    │
├─────────────────────────────────────────────┤
│  Bienvenido, {Nombre} 👋                   │
├─────────────────────────────────────────────┤
│  ┌──────────┬──────────┬──────────┬─────────┐
│  │ Ingresos │ Reservas │ Canchas  │ Clientes│
│  │ $12,450  │    24    │    6     │  342    │
│  └──────────┴──────────┴──────────┴─────────┘
├─────────────────────────────────────────────┤
│  GRÁFICO DE INGRESOS (Lun-Dom)             │
│  ████ ████ ███  █████ █████ ████ ██        │
├─────────────────────────────────────────────┤
│  CANCHAS DESTACADAS (3 tarjetas)           │
│  - Nombre, ubicación, reservas, ingresos   │
├─────────────────────────────────────────────┤
│  PRÓXIMAS RESERVAS (Lista)                 │
│  - Cancha, cliente, hora                   │
├─────────────────────────────────────────────┤
│  ACCIONES RÁPIDAS (3 botones)              │
│  - Nueva Cancha                            │
│  - Gestionar Canchas                       │
│  - Configuración                           │
└─────────────────────────────────────────────┘
```

#### CRUD de Canchas
```
┌──────────────────────────────────────────┐
│  GESTIONAR CANCHAS                       │
│  [+ Nueva Cancha]                        │
├──────────────────────────────────────────┤
│  [Buscar] [Deporte: Todos]              │
├──────────────────────────────────────────┤
│  ┌──────────────┐ ┌──────────────┐     │
│  │ Cancha 1     │ │ Cancha 2     │     │
│  │ Activa       │ │ Inactiva     │     │
│  │ $50/hora     │ │ $35/hora     │     │
│  │              │ │              │     │
│  │[Editar]      │ │[Editar]      │     │
│  │[Activar]     │ │[Desactivar]  │     │
│  │[Eliminar]    │ │[Eliminar]    │     │
│  └──────────────┘ └──────────────┘     │
└──────────────────────────────────────────┘

MODAL DE CREAR/EDITAR:
┌────────────────────────────┐
│ Nueva Cancha               │
├────────────────────────────┤
│ Nombre: [_________________]│
│ Deporte: [Fútbol ▼]       │
│ Ubicación: [________________]
│ Precio: [___] /hora       │
│ Servicios: [______________]
│ Descripción: [____________]│
│                            │
│ [Cancelar] [Crear]        │
└────────────────────────────┘
```

---

### 3. ADMINISTRADOR/ADMIN
**Propósito**: Gestionar el sistema completo

**Funcionalidades**:
- 📊 Dashboard con métricas globales
- 👥 Gestión de usuarios
- 🏀 Gestión global de canchas
- 📅 Gestión de reservas
- 📈 Reportes
- 📋 Calendario
- ⚙️ Configuración
- 🎁 Promociones

**Rutas**:
```
/admin/dashboard      → Dashboard administrativo
/admin/courts         → Gestión global de canchas
/admin/bookings       → Gestión de reservas
/admin/users          → Gestión de usuarios
/admin/reports        → Reportes
/admin/calendar       → Calendario
/admin/settings       → Configuración
/admin/promotions     → Promociones
```

**Permisos**:
- ✅ Acceso a /admin/*
- ❌ No puede acceder a /dashboard (customer)
- ❌ No puede acceder a /courts (owner)

---

## 🔑 Gestión de Autenticación

### userStore.ts - Centro de Control
```typescript
// Interfaz de Usuario
interface User {
  name: string;
  email: string;
  phone?: string;
  role: "customer" | "owner" | "admin";
}

// Funciones principales
userStore.setUser(user)        // Guardar usuario
userStore.getUser()            // Obtener usuario
userStore.getUserRole()        // Obtener rol
userStore.clearUser()          // Limpiar sesión
userStore.logout()             // Logout completo
```

### localStorage Key
```javascript
localStorage.getItem("sportbook_user")
// Contiene: JSON del usuario con rol
```

---

## 🛡️ Protección de Rutas

### Componente ProtectedRoute
```typescript
function ProtectedRoute({
  children,
  requiredRole?: "customer" | "owner" | "admin"
}) {
  const user = userStore.getUser();
  
  // Sin autenticación → /login
  if (!user) return <Navigate to="/login" />;
  
  // Rol no coincide → Redirige al dashboard correcto
  if (requiredRole && user.role !== requiredRole) {
    if (user.role === "customer") return <Navigate to="/dashboard" />;
    if (user.role === "owner") return <Navigate to="/courts" />;
    if (user.role === "admin") return <Navigate to="/admin/dashboard" />;
  }
  
  return children;
}
```

### Uso en routes.tsx
```typescript
{
  path: "/courts",
  element: (
    <ProtectedRoute requiredRole="owner">
      <CourtsList />
    </ProtectedRoute>
  ),
}
```

---

## 🔄 Flujo de Autenticación

### Flujo de Registro
```
1. User visita /register
   ↓
2. Selecciona ROL (Customer/Owner/Admin)
   ↓
3. Completa formulario
   ├─ Nombre
   ├─ Email
   ├─ Teléfono
   ├─ Contraseña
   └─ Confirmación
   ↓
4. Validaciones
   ├─ Email válido
   ├─ Teléfono válido
   ├─ Contraseñas coinciden
   └─ Campos no vacíos
   ↓
5. Guardar en localStorage (userStore.setUser)
   ↓
6. Mostrar éxito y redirigir
   ↓
7. REDIRECCIÓN SEGÚN ROL:
   ├─ Customer → /dashboard
   ├─ Owner → /courts
   └─ Admin → /admin/dashboard
```

### Flujo de Login
```
1. User visita /login
   ↓
2. Opción A: Llenar email y contraseña
   Opción B: Click en botón de rol rápido
   ↓
3. Validaciones
   ├─ Email válido
   ├─ Contraseña correcta
   └─ Usuario existe
   ↓
4. Autenticación (mock MOCK_USERS)
   ↓
5. Guardar en localStorage
   ↓
6. REDIRECCIÓN SEGÚN ROL:
   ├─ Customer → /dashboard
   ├─ Owner → /courts
   └─ Admin → /admin/dashboard
```

### Credenciales de Prueba
```javascript
const MOCK_USERS = {
  customer: {
    email: "customer@example.com",
    password: "customer123",
    name: "Juan García",
    role: "customer"
  },
  owner: {
    email: "owner@example.com",
    password: "owner123",
    name: "María López",
    role: "owner"
  },
  admin: {
    email: "admin@example.com",
    password: "admin123",
    name: "Carlos Admin",
    role: "admin"
  }
};
```

---

## 📱 Interfaz del Owner Dashboard

### Componentes:
1. **Header** - Navegación y logout
2. **Bienvenida** - "Bienvenido, {Nombre}"
3. **Stat Cards** - 4 tarjetas de estadísticas
4. **Weekly Chart** - Gráfico de barras de ingresos
5. **Quick Actions** - 3 botones de acciones rápidas
6. **Recent Courts** - 3 tarjetas de canchas
7. **Upcoming Bookings** - Lista de próximas reservas

### Datos Simulados:
```javascript
const stats = [
  { title: "Ingresos Totales", value: "$12,450", ... },
  { title: "Reservas Activas", value: 24, ... },
  { title: "Canchas Activas", value: 6, ... },
  { title: "Clientes Totales", value: 342, ... }
];

const weeklyData = [
  { day: "Lunes", amount: 1800 },
  // ... más días
];
```

---

## 🎨 CRUD de Canchas - Estructura Técnica

### Interfaz Court
```typescript
interface Court {
  id: number;
  name: string;
  sport: string;
  location: string;
  price: number;
  amenities: string[];
  reservations: number;
  active: boolean;
  description?: string;
}
```

### Operaciones CRUD
```typescript
// CREATE
const newCourt: Court = {
  id: Math.max(...courts.map(c => c.id), 0) + 1,
  ...formData
};
setCourts([...courts, newCourt]);

// READ
const filteredCourts = courts.filter(court => {
  return matchesSearch && matchesSport;
});

// UPDATE
setCourts(courts.map(court =>
  court.id === editingId ? { ...court, ...updates } : court
));

// DELETE
setCourts(courts.filter(court => court.id !== id));

// TOGGLE
setCourts(courts.map(court =>
  court.id === id ? { ...court, active: !court.active } : court
));
```

---

## 🔐 Control de Acceso

### Matrix de Permisos

| Ruta | Customer | Owner | Admin |
|------|----------|-------|-------|
| /dashboard | ✅ | ❌ | ❌ |
| /courts | ❌ | ✅ | ❌ |
| /admin/dashboard | ❌ | ❌ | ✅ |
| /my-bookings | ✅ | ❌ | ❌ |
| /profile | ✅ | ❌ | ❌ |
| /login | ✅ | ✅ | ✅ |
| /register | ✅ | ✅ | ✅ |

---

## 🚀 Flujo de Uso - Owner

```
1. Owner visita /login
   ↓
2. Click botón naranja "Dueño: owner@example.com"
   ↓
3. Campo email y contraseña se completan
   ↓
4. Click "Iniciar Sesión"
   ↓
5. Se valida y se redirige a /courts
   ↓
6. Owner ve sus canchas (grid de 3 columnas)
   ↓
7. Owner puede:
   ├─ [+ Nueva Cancha] → Modal crear
   ├─ [Editar] → Modal editar con datos
   ├─ [Activar/Desactivar] → Toggle estado
   ├─ [Eliminar] → Confirmación y elimina
   ├─ Buscar por nombre/ubicación
   └─ Filtrar por deporte
   ↓
8. Desde /courts puede ir a /dashboard
   ↓
9. Dashboard muestra gráficos y estadísticas
   ↓
10. Click "Salir" → logout y /login
```

---

## 📊 Estados del Sistema

### Estados Posibles
```
User = null
  → Mostrar /login o /register

User.role = "customer"
  → Permitir acceso a /dashboard
  → Bloquear acceso a /courts y /admin

User.role = "owner"
  → Permitir acceso a /courts y /dashboard (owner)
  → Bloquear acceso a /dashboard (customer) y /admin

User.role = "admin"
  → Permitir acceso a /admin/*
  → Bloquear acceso a /dashboard y /courts (owner)
```

---

## 🛠️ Tecnología Utilizada

### Frontend
- **React 18** - Framework UI
- **TypeScript** - Type safety
- **React Router v6** - Enrutamiento
- **Tailwind CSS** - Estilos
- **shadcn/ui** - Componentes base
- **Recharts** - Gráficos
- **lucide-react** - Iconos

### State Management
- **localStorage** - Persistencia
- **useState** - Estado local
- **React Context** - (opcional para futuro)

### Datos
- **Mock Data** - Simulación
- **JSON** - Almacenamiento

---

## 📈 Escalabilidad Futura

### Fase 2: Backend Integration
```
- REST API o GraphQL
- JWT para autenticación
- Base de datos (PostgreSQL)
- Validación servidor
- Auditoría y logs
```

### Fase 3: Features Avanzados
```
- WebSockets para tiempo real
- Notificaciones push
- Pagos integrados
- Analytics avanzado
- Machine learning
```

---

## ✅ Checklist de Validación

- [x] 3 roles implementados
- [x] Rutas protegidas funcionales
- [x] RegisterPage con selector de rol
- [x] LoginPage con botones rápidos
- [x] OwnerDashboard con gráficos
- [x] CRUD de canchas funcional
- [x] Redirecciones automáticas
- [x] Logout funcional
- [x] Almacenamiento en localStorage
- [x] Validaciones de entrada
- [x] Mensajes de error/éxito
- [x] Responsive design
- [x] Documentación completa

---

## 🎯 Conclusión

El sistema está **100% funcional** con:
- ✅ Arquitectura clara de 3 roles
- ✅ Autenticación completa (mock)
- ✅ Rutas protegidas
- ✅ UI profesional
- ✅ CRUD operativo
- ✅ Documentación detallada

**Listo para fase de backend.**

