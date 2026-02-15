# 🔍 Sistema de Búsqueda de Productos - Full Stack

> "El que busca, encuentra... pero solo si tú lo programas."

## 📋 Descripción del Proyecto

Este proyecto implementa un sistema completo de búsqueda de productos con filtrado en tiempo real, case-insensitive, para resolver el problema de navegación en 50+ páginas.

### Características:
- ✅ Búsqueda en tiempo real con debounce
- ✅ Case-insensitive (busca "Cotton" con "cotton")
- ✅ Busca en Nombre y Descripción simultáneamente
- ✅ Protegido contra SQL Injection
- ✅ Respuesta amigable cuando no hay resultados
- ✅ Spinner de carga durante búsqueda
- ✅ Interfaz responsive y moderna

---

## 🛠️ Estructura del Proyecto

```
/backend-api
├── src/
│   ├── index.js (Server Express)
│   ├── config/
│   │   └── db.js (Configuración PostgreSQL)
│   ├── controllers/
│   │   ├── productosController.js (NUEVO - Búsqueda)
│   │   ├── externalController.js (Carga masiva)
│   │   └── categoriaController.js
│   └── routes/
│       ├── productoRoute.js (ACTUALIZADO)
│       └── categoriaRoute.js
├── package.json
├── .env (Variables de entorno)
└── pnpm-lock.yaml

/frontend
└── index.html (Interfaz búsqueda - NUEVO)
```

---

## 🚀 Configuración e Instalación

### Backend

1. **Instala dependencias:**
   ```bash
   cd backend-api
   pnpm install
   ```

2. **Configura variables de entorno (.env):**
   ```
   PORT=4000
   DB_HOST=localhost
   DB_USER=postgres
   DB_PASS=tu_contraseña
   DB_NAME=tu_base_datos
   DB_PORT=5432
   ```

3. **Inicia el servidor:**
   ```bash
   pnpm run dev    # Modo desarrollo con nodemon
   # O
   npm start       # Modo producción
   ```

   El servidor correrá en: `http://localhost:4000`

### Frontend

1. **Abre el archivo en el navegador:**
   - Simplemente abre `frontend/index.html` en tu navegador
   - O usa un servidor local: `python -m http.server` en la carpeta `frontend/`

---

## 📡 Endpoints API

### GET /api/productos
Obtiene todos los productos disponibles.

**Response:**
```json
{
  "cantidad": 20,
  "productos": [
    {
      "id": 1,
      "nombre": "White Gold Plated Princess",
      "descripcion": "Classic Made in China...",
      "categoria_id": 5,
      "precio": 9.99,
      "stock": 15
    }
  ]
}
```

### GET /api/productos/search?q=termino
Busca productos por nombre o descripción (case-insensitive).

**Parámetros:**
- `q` (requerido): Término de búsqueda

**Response (con resultados):**
```json
{
  "cantidad": 3,
  "productos": [
    {
      "id": 5,
      "nombre": "Cotton T-Shirt",
      "descripcion": "100% Cotton fabric...",
      "categoria_id": 1,
      "precio": 19.99,
      "stock": 42
    }
  ]
}
```

**Response (sin resultados):**
```json
{
  "cantidad": 0,
  "productos": []
}
```

**Error (sin parámetro q):**
```json
{
  "error": "El parámetro de búsqueda \"q\" es requerido"
}
```

---

## 🧪 Pruebas con cURL / Postman

### Obtener todos los productos:
```bash
curl -X GET "http://localhost:4000/api/productos"
```

### Buscar por término:
```bash
curl -X GET "http://localhost:4000/api/productos/search?q=cotton"
```

### Buscar en descripción:
```bash
curl -X GET "http://localhost:4000/api/productos/search?q=tecnology"
```

### Sin parámetro (devuelve error 400):
```bash
curl -X GET "http://localhost:4000/api/productos/search"
```

---

## 🔒 Seguridad

### SQL Injection Prevention
- Utilizamos **consultas parametrizadas** con `$1`
- Los parámetros se pasan separados de la query
- PostgreSQL previene inyección automáticamente

**Ejemplo seguro:**
```javascript
const query = `
    SELECT * FROM productos
    WHERE nombre ILIKE $1 OR descripcion ILIKE $1
`;
const searchTerm = `%${q}%`;
await pool.query(query, [searchTerm]);  // Seguro
```

### Validación de Entrada
- Se valida que el parámetro `q` no esté vacío
- Se devuelve error 400 Bad Request si falta

---

## 🎨 Frontend Features

### Tabla de Productos
Muestra 5 columnas en orden:
1. **NOMBRE** - Nombre del producto
2. **DESCRIPCIÓN** - Primeros 100 caracteres
3. **CATEGORÍA** - ID de categoría
4. **PRECIO** - Formato USD
5. **STOCK** - Cantidad disponible

### Búsqueda en Tiempo Real
- Debounce de 300ms (evita demasiadas request)
- Input placeholder con ejemplo
- Spinner "Buscando..." mientras se procesa

### Mensajes Amigables
- **Cargando**: Muestra spinner mientras obtiene datos
- **No hay resultados**: "No encontramos productos que coincidan con 'xyz'"
- **Error**: Mensaje claro si hay problemas de conexión

### Botón "Limpiar búsqueda"
- Vuelve a mostrar todos los productos
- Se deshabilita cuando está limpio

---

## 📱 Responsive Design
- ✅ Desktop completo (1200px+)
- ✅ Tablets (768px - 1199px)
- ✅ Mobile (< 768px)
- ✅ Panel overflow en tablas grandes

---

## 🐛 Troubleshooting

**"Error al cargar productos"**
- Verifica que el servidor esté corriendo: `http://localhost:4000`
- Revisa que CORS esté habilitado en Express

**"El parámetro de búsqueda es requerido"**
- Esto es correcto - el backend requiere el parámetro `q`
- La interfaz siempre lo envía

**Búsqueda lenta**
- El debounce espera 300ms después de escribir
- Esto es intencional para no sobrecargar el servidor

**CORS error en consola**
- El backend tiene CORS habilitado
- Si persiste, verifica que el servidor está en `localhost:4000`

---

## 📸 Capturas de Pantalla Entregables

1. **Postman/cURL**: GET /search?q=tec
   - Muestra los resultados de búsqueda en JSON

2. **HTML Tabla Completa**: 
   - Todos los productos listados en la tabla

3. **HTML Sin Resultados**:
   - Mensaje amigable cuando no hay coincidencias

---

## 🔧 Comandos Útiles

```bash
# Backend
cd backend-api
pnpm install          # Instalar dependencias
pnpm run dev         # Ejecutar con nodemon (auto-reload)
npm start            # Ejecutar normal

# Tests manuales
curl http://localhost:4000/api/productos
curl "http://localhost:4000/api/productos/search?q=shirt"
```

---

## 📚 Stack Tecnológico

- **Backend**: Node.js + Express
- **Base de Datos**: PostgreSQL
- **Frontend**: HTML5 + Vanilla JavaScript + CSS3
- **Package Manager**: pnpm

---

## 📝 Licencia

ISC

---

**Creado con ❤️ para resolver la búsqueda de productos**
