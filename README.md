# 🍽️ Comidas del Mes

Aplicación de planificación de comidas con vista de calendario mensual interactivo.

## 📋 Descripción

Una app sencilla y bonita para planificar qué comerás cada día del mes. Visualiza todo en un calendario y gestiona tus comidas fácilmente.

## ✨ Características

- 📅 Calendario mensual interactivo
- 🍽️ Agregar, editar y eliminar comidas
- 💾 Guardado automático (LocalStorage)
- 🏷️ Categorías de comidas (Desayuno, Almuerzo, Cena)
- 🔄 Navegación entre meses
- 📱 Responsive (funciona en móvil y escritorio)

## 🚀 Cómo usar

### Instalación

1. Clona el repositorio
```bash
git clone https://github.com/marcoduarte1974-spec/Comidas-del-mes.git
cd Comidas-del-mes
```

2. Instala las dependencias
```bash
npm install
```

3. Ejecuta el servidor de desarrollo
```bash
npm run dev
```

4. Abre tu navegador en `http://localhost:5173`

### Desarrollo

```bash
npm run dev       # Inicia servidor de desarrollo
npm run build     # Crea versión de producción
npm run preview   # Ve la versión compilada
```

## 📁 Estructura del Proyecto

```
Comidas-del-mes/
├── src/
│   ├── components/
│   │   ├── Calendar.vue       # Componente del calendario
│   │   └── MealForm.vue       # Formulario para agregar comidas
│   ├── App.vue                # Componente principal
│   ├── main.js                # Punto de entrada
│   └── style.css              # Estilos globales
├── index.html                 # HTML principal
├── package.json               # Dependencias y scripts
├── vite.config.js             # Configuración Vite
└── README.md                  # Este archivo
```

## 🛠️ Tecnologías

- **Vue 3** - Framework JavaScript
- **Vite** - Build tool rápido
- **CSS3** - Estilos
- **LocalStorage** - Almacenamiento local

## 📝 Notas

- Los datos se guardan en el navegador (LocalStorage)
- No se conecta a un servidor backend
- Todo funciona offline

## 👨‍💻 Autor

marcoduarte1974-spec

## 📄 Licencia

MIT