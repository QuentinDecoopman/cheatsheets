# Conventions & Bonnes Pratiques Tailwind CSS

## 📋 Configuration

### tailwind.config.js

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./src/**/*.{js,jsx,ts,tsx}", "./public/index.html"],

  theme: {
    // Extend - Ajoute aux valeurs par défaut
    extend: {
      colors: {
        brand: {
          50: "#f0f9ff",
          100: "#e0f2fe",
          500: "#0ea5e9",
          900: "#0c4a6e",
        },
        primary: "#3490dc",
        secondary: "#ffed4e",
      },

      fontFamily: {
        sans: ["Inter", "sans-serif"],
        heading: ["Montserrat", "sans-serif"],
      },

      spacing: {
        128: "32rem",
        144: "36rem",
      },

      borderRadius: {
        "4xl": "2rem",
      },

      boxShadow: {
        custom: "0 4px 6px -1px rgba(0, 0, 0, 0.1)",
      },

      keyframes: {
        "fade-in": {
          "0%": { opacity: "0" },
          "100%": { opacity: "1" },
        },
        "slide-in": {
          "0%": { transform: "translateX(-100%)" },
          "100%": { transform: "translateX(0)" },
        },
      },

      animation: {
        "fade-in": "fade-in 0.3s ease-out",
        "slide-in": "slide-in 0.3s ease-out",
      },
    },

    // Override - Remplace les valeurs par défaut
    // screens: {
    //   'tablet': '640px',
    //   'laptop': '1024px',
    //   'desktop': '1280px',
    // },
  },

  plugins: [
    require("@tailwindcss/forms"),
    require("@tailwindcss/typography"),
    require("@tailwindcss/aspect-ratio"),
    require("@tailwindcss/line-clamp"),
  ],

  // Dark mode
  darkMode: "class", // ou 'media'
};
```

---

## ✅ Conventions de Classes

### Ordre des classes (recommandé)

```jsx
// 1. Layout (display, position)
// 2. Box model (width, height, padding, margin)
// 3. Typography
// 4. Visual (background, border, shadow)
// 5. Misc (cursor, animation, etc.)

<div
  className="
  flex items-center justify-between
  w-full max-w-4xl p-6 mx-auto
  text-lg font-semibold
  bg-white border border-gray-200 rounded-lg shadow-md
  hover:shadow-lg transition-shadow
"
>
  Content
</div>
```

### Utiliser l'extension Prettier Plugin Tailwind

```bash
npm install -D prettier prettier-plugin-tailwindcss
```

```json
// .prettierrc
{
  "plugins": ["prettier-plugin-tailwindcss"]
}
```

---

## 🎨 Responsive Design

### Breakpoints

```jsx
// sm: 640px
// md: 768px
// lg: 1024px
// xl: 1280px
// 2xl: 1536px

<div className="
  w-full
  sm:w-1/2
  md:w-1/3
  lg:w-1/4
  xl:w-1/6
">
  Responsive width
</div>

// Mobile-first approach
<div className="
  text-sm      // mobile
  md:text-base // tablet+
  lg:text-lg   // desktop+
">
  Responsive text
</div>
```

---

## 🔄 States et Variants

### Hover, Focus, Active

```jsx
<button
  className="
  bg-blue-500 text-white px-4 py-2 rounded
  hover:bg-blue-600
  focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2
  active:bg-blue-700
  disabled:opacity-50 disabled:cursor-not-allowed
"
>
  Button
</button>
```

### Group et Peer

```jsx
// Group - parent hover affecte l'enfant
<div className="group cursor-pointer">
  <img className="group-hover:scale-110 transition-transform" />
  <h3 className="group-hover:text-blue-500">Title</h3>
</div>

// Peer - sibling state
<input type="checkbox" className="peer hidden" id="toggle" />
<label htmlFor="toggle" className="
  peer-checked:bg-blue-500
  peer-checked:text-white
">
  Toggle
</label>
```

---

## 🌙 Dark Mode

```jsx
// tailwind.config.js
module.exports = {
  darkMode: 'class', // ou 'media'
}

// Utilisation
<div className="
  bg-white text-gray-900
  dark:bg-gray-900 dark:text-white
">
  Content
</div>

// Toggle dark mode
function ThemeToggle() {
  const [isDark, setIsDark] = useState(false);

  useEffect(() => {
    if (isDark) {
      document.documentElement.classList.add('dark');
    } else {
      document.documentElement.classList.remove('dark');
    }
  }, [isDark]);

  return (
    <button onClick={() => setIsDark(!isDark)}>
      Toggle Dark Mode
    </button>
  );
}
```

---

## 🎯 Components avec @apply

### Dans un fichier CSS

```css
/* styles.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .btn {
    @apply px-4 py-2 rounded font-semibold transition-colors;
  }

  .btn-primary {
    @apply bg-blue-500 text-white hover:bg-blue-600;
  }

  .btn-secondary {
    @apply bg-gray-200 text-gray-800 hover:bg-gray-300;
  }

  .card {
    @apply bg-white rounded-lg shadow-md p-6;
  }
}

@layer utilities {
  .text-balance {
    text-wrap: balance;
  }
}
```

```jsx
// Utilisation
<button className="btn btn-primary">Primary</button>
<button className="btn btn-secondary">Secondary</button>
```

---

## 🧩 Composants React

### Composant Button réutilisable

```tsx
import { ButtonHTMLAttributes, forwardRef } from "react";
import { clsx } from "clsx";

type ButtonVariant = "primary" | "secondary" | "danger";
type ButtonSize = "sm" | "md" | "lg";

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: ButtonVariant;
  size?: ButtonSize;
}

const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  (
    { variant = "primary", size = "md", className, children, ...props },
    ref,
  ) => {
    const baseClasses =
      "font-semibold rounded transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2";

    const variantClasses = {
      primary: "bg-blue-500 text-white hover:bg-blue-600 focus:ring-blue-500",
      secondary:
        "bg-gray-200 text-gray-800 hover:bg-gray-300 focus:ring-gray-500",
      danger: "bg-red-500 text-white hover:bg-red-600 focus:ring-red-500",
    };

    const sizeClasses = {
      sm: "px-3 py-1.5 text-sm",
      md: "px-4 py-2 text-base",
      lg: "px-6 py-3 text-lg",
    };

    return (
      <button
        ref={ref}
        className={clsx(
          baseClasses,
          variantClasses[variant],
          sizeClasses[size],
          className,
        )}
        {...props}
      >
        {children}
      </button>
    );
  },
);

// Utilisation
<Button variant="primary" size="lg">
  Click me
</Button>;
```

### Utility clsx / classnames

```tsx
import clsx from 'clsx';

<div className={clsx(
  'base-classes',
  isActive && 'active-classes',
  isDisabled && 'disabled-classes',
  customClass
)} />

// Ou avec objet
<div className={clsx({
  'bg-blue-500': isPrimary,
  'bg-red-500': isDanger,
  'opacity-50': isDisabled,
})} />
```

---

## 🔧 Plugins Personnalisés

```javascript
// tailwind.config.js
const plugin = require("tailwindcss/plugin");

module.exports = {
  plugins: [
    plugin(function ({ addUtilities, addComponents, theme }) {
      // Utilities personnalisées
      addUtilities({
        ".scrollbar-hide": {
          "-ms-overflow-style": "none",
          "scrollbar-width": "none",
          "&::-webkit-scrollbar": {
            display: "none",
          },
        },
        ".text-shadow": {
          "text-shadow": "0 2px 4px rgba(0,0,0,0.10)",
        },
      });

      // Components personnalisés
      addComponents({
        ".container-custom": {
          maxWidth: theme("screens.xl"),
          marginLeft: "auto",
          marginRight: "auto",
          paddingLeft: theme("spacing.4"),
          paddingRight: theme("spacing.4"),
        },
      });
    }),
  ],
};
```

---

## 🚀 Optimisations

### Purge CSS (Production)

```javascript
// tailwind.config.js
module.exports = {
  content: ["./src/**/*.{js,jsx,ts,tsx}", "./public/index.html"],
  // Tailwind 3+ purge automatiquement
};
```

### Arbitrary Values

```jsx
// Valeurs arbitraires
<div className="top-[117px]">Custom value</div>
<div className="bg-[#1da1f2]">Custom color</div>
<div className="text-[14px]">Custom size</div>

// Avec modifiers
<div className="lg:top-[344px]">Responsive custom</div>
```

### Important Modifier

```jsx
// Forcer !important
<div className="!mt-0">Force margin-top to 0</div>
```

---

## 📝 Best Practices

### 1. Éviter les classes trop longues

```jsx
// ❌ Éviter
<div className="flex items-center justify-between w-full max-w-4xl p-6 mx-auto text-lg font-semibold bg-white border border-gray-200 rounded-lg shadow-md hover:shadow-lg transition-shadow">

// ✅ Bon - Extraire dans un composant ou @apply
const Card = ({ children }) => (
  <div className="card">
    {children}
  </div>
);
```

### 2. Utiliser les variantes de manière cohérente

```jsx
// ✅ Bon
<Button variant="primary">Primary</Button>
<Button variant="secondary">Secondary</Button>

// ❌ Éviter - mélanger approches
<button className="btn-primary">Primary</button>
<button className="bg-gray-200 text-gray-800">Secondary</button>
```

### 3. Grouper les classes liées

```jsx
// ✅ Bon - organisation logique
<div className="
  flex items-center gap-4
  p-6 mx-auto max-w-4xl
  bg-white rounded-lg shadow-md
  hover:shadow-lg transition-shadow
">
```

### 4. Préfixer les classes personnalisées

```css
/* ✅ Bon */
@layer components {
  .my-card { ... }
  .my-button { ... }
}

/* ❌ Éviter - conflit potentiel */
@layer components {
  .card { ... }
  .button { ... }
}
```

---

## 🎨 Exemples de Composants

### Card

```jsx
<div
  className="
  bg-white rounded-lg shadow-md overflow-hidden
  hover:shadow-xl transition-shadow
"
>
  <img src="image.jpg" alt="Card" className="w-full h-48 object-cover" />
  <div className="p-6">
    <h3 className="text-xl font-bold mb-2">Card Title</h3>
    <p className="text-gray-600 mb-4">Card description</p>
    <button
      className="
      bg-blue-500 text-white px-4 py-2 rounded
      hover:bg-blue-600 transition-colors
    "
    >
      Learn More
    </button>
  </div>
</div>
```

### Modal

```jsx
<div className="fixed inset-0 z-50 flex items-center justify-center">
  {/* Backdrop */}
  <div className="absolute inset-0 bg-black/50 backdrop-blur-sm" />

  {/* Modal */}
  <div
    className="
    relative z-10 bg-white rounded-lg shadow-2xl
    w-full max-w-md mx-4 p-6
    animate-fade-in
  "
  >
    <h2 className="text-2xl font-bold mb-4">Modal Title</h2>
    <p className="text-gray-600 mb-6">Modal content</p>
    <div className="flex justify-end gap-2">
      <button className="px-4 py-2 text-gray-600 hover:bg-gray-100 rounded">
        Cancel
      </button>
      <button className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
        Confirm
      </button>
    </div>
  </div>
</div>
```

### Form

```jsx
<form className="space-y-6 max-w-md mx-auto">
  <div>
    <label className="block text-sm font-medium text-gray-700 mb-2">
      Email
    </label>
    <input
      type="email"
      className="
        w-full px-4 py-2 border border-gray-300 rounded-lg
        focus:ring-2 focus:ring-blue-500 focus:border-transparent
        transition-all
      "
    />
  </div>

  <div>
    <label className="block text-sm font-medium text-gray-700 mb-2">
      Password
    </label>
    <input
      type="password"
      className="
        w-full px-4 py-2 border border-gray-300 rounded-lg
        focus:ring-2 focus:ring-blue-500 focus:border-transparent
      "
    />
  </div>

  <button
    className="
    w-full bg-blue-500 text-white py-2 rounded-lg font-semibold
    hover:bg-blue-600 transition-colors
  "
  >
    Submit
  </button>
</form>
```

---

## 📚 Ressources

- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [Tailwind UI](https://tailwindui.com/)
- [Headless UI](https://headlessui.com/)
- [Tailwind CSS IntelliSense](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss)
- [Tailwind Play](https://play.tailwindcss.com/)
