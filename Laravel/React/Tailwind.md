# Instalación de Tailwind CSS en proyecto Vite + React

Esta guía explica cómo instalar Tailwind CSS 3 en un proyecto de React usando Vite.

## Pasos de instalación

### 1. Crear proyecto con Vite usando pnpm

```bash
# Instalar pnpm globalmente si no lo tienes
npm install -g pnpm

# Crear proyecto con Vite
pnpm create vite@latest mi-proyecto -- --template react

# Navegar al directorio del proyecto
cd mi-proyecto

# Instalar dependencias
pnpm install
```

### 2. Instalar Tailwind CSS y sus dependencias

```bash
pnpm add -D tailwindcss@3 postcss autoprefixer
```

### 3. Crear los archivos de configuración manualmente

Crea un archivo `tailwind.config.js` en la raíz del proyecto:

```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

Crea un archivo `postcss.config.js` en la raíz del proyecto:

```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

### 4. Añadir las directivas de Tailwind a tu CSS

Abre o crea el archivo `src/index.css` y añade:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### 5. Importar el CSS en el punto de entrada

Asegúrate de que el archivo CSS está importado en tu punto de entrada (normalmente `src/main.jsx`):

```javascript
import './index.css'
```

### 6. Iniciar el servidor de desarrollo

```bash
pnpm dev
```

## Solución de problemas

Si encuentras errores relacionados con versiones de Tailwind, especialmente si estás usando Tailwind 4, es recomendable volver a Tailwind 3 que tiene mejor compatibilidad con la mayoría de herramientas.

```bash
# Si tienes problemas con Tailwind 4, desinstálalo y vuelve a instalar la versión 3
pnpm remove tailwindcss
pnpm add -D tailwindcss@3 postcss autoprefixer
```
