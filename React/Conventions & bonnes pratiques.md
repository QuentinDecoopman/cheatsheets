# Conventions & Bonnes Pratiques React

## 📋 Conventions de Nommage

### Composants

- **PascalCase** pour les composants

```jsx
function UserProfile() {
  return <div>Profile</div>;
}

const UserCard = () => {
  return <div>Card</div>;
};
```

### Fichiers

- **PascalCase** pour les fichiers de composants

```
UserProfile.jsx
UserCard.tsx
Button.jsx
```

### Props et State

- **camelCase** pour les props et variables

```jsx
function UserCard({ userName, isActive, onUserClick }) {
  const [isLoading, setIsLoading] = useState(false);
}
```

### Handlers

- Préfixer avec `handle` ou `on`

```jsx
function Button({ onClick }) {
  const handleClick = (e) => {
    // Logique locale
    onClick?.(e);
  };

  return <button onClick={handleClick}>Click</button>;
}
```

### Booléens

- Préfixer avec `is`, `has`, `should`, `can`

```jsx
const [isOpen, setIsOpen] = useState(false);
const [hasError, setHasError] = useState(false);
const shouldRender = isAuthenticated && hasPermission;
```

---

## 🏗️ Structure des Composants

### Ordre des éléments

```jsx
import React, { useState, useEffect } from "react";
import PropTypes from "prop-types";
import "./UserCard.css";

// 1. Définition du composant
function UserCard({ user, onEdit }) {
  // 2. Hooks d'état
  const [isEditing, setIsEditing] = useState(false);

  // 3. Hooks d'effet
  useEffect(() => {
    // Side effects
  }, []);

  // 4. Autres hooks personnalisés
  const { data, loading } = useFetchUser(user.id);

  // 5. Fonctions handlers
  const handleEdit = () => {
    setIsEditing(true);
    onEdit?.(user);
  };

  // 6. Fonctions utilitaires
  const formatName = (name) => name.toUpperCase();

  // 7. Conditions de rendu précoce
  if (loading) return <Spinner />;
  if (!user) return null;

  // 8. Rendu principal
  return (
    <div className="user-card">
      <h2>{formatName(user.name)}</h2>
      <button onClick={handleEdit}>Edit</button>
    </div>
  );
}

// 9. PropTypes
UserCard.propTypes = {
  user: PropTypes.shape({
    id: PropTypes.string.isRequired,
    name: PropTypes.string.isRequired,
  }).isRequired,
  onEdit: PropTypes.func,
};

// 10. Default props
UserCard.defaultProps = {
  onEdit: null,
};

// 11. Export
export default UserCard;
```

---

## ✅ Bonnes Pratiques

### 1. Composants Fonctionnels avec Hooks

```jsx
// ✅ Bon - composant fonctionnel
function UserList() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetchUsers().then(setUsers);
  }, []);

  return (
    <ul>
      {users.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}

// ❌ Éviter les class components (sauf si nécessaire)
class UserList extends React.Component {
  // ...
}
```

### 2. Destructuration des Props

```jsx
// ✅ Bon
function UserCard({ name, email, age }) {
  return <div>{name}</div>;
}

// ❌ Éviter
function UserCard(props) {
  return <div>{props.name}</div>;
}
```

### 3. Fragment au lieu de div

```jsx
// ✅ Bon
function UserInfo() {
  return (
    <>
      <h1>Title</h1>
      <p>Description</p>
    </>
  );
}

// ❌ Div inutile
function UserInfo() {
  return (
    <div>
      <h1>Title</h1>
      <p>Description</p>
    </div>
  );
}
```

### 4. Clés uniques dans les listes

```jsx
// ✅ Bon - ID unique
users.map((user) => <UserCard key={user.id} user={user} />);

// ❌ Éviter l'index
users.map((user, index) => <UserCard key={index} user={user} />);
```

### 5. Conditional Rendering

```jsx
// ✅ Bon
function UserCard({ user, isAdmin }) {
  return (
    <div>
      {isAdmin && <AdminPanel />}
      {user ? <Profile user={user} /> : <Login />}
      {loading ? <Spinner /> : <Content />}
    </div>
  );
}

// Ternaire complexe - extraire en variable
const content = isLoading ? (
  <Spinner />
) : hasError ? (
  <Error message={error} />
) : (
  <Content data={data} />
);

return <div>{content}</div>;
```

---

## 🎣 Hooks

### useState

```jsx
// ✅ Bon usage
const [count, setCount] = useState(0);
const [user, setUser] = useState(null);
const [form, setForm] = useState({ name: "", email: "" });

// Mise à jour avec fonction
setCount((prev) => prev + 1);
setForm((prev) => ({ ...prev, name: "John" }));

// ❌ Éviter les mutations
// user.name = 'John'; // Mauvais
setUser({ ...user, name: "John" }); // Bon
```

### useEffect

```jsx
// Exécution au montage
useEffect(() => {
  fetchData();
}, []);

// Avec dépendances
useEffect(() => {
  fetchUser(userId);
}, [userId]);

// Avec cleanup
useEffect(() => {
  const subscription = subscribe();

  return () => {
    subscription.unsubscribe();
  };
}, []);

// ❌ Éviter les dépendances manquantes
useEffect(() => {
  console.log(user.name); // user devrait être en dépendance
}, []); // Mauvais
```

### useCallback

```jsx
// ✅ Mémoriser les fonctions passées en props
const handleClick = useCallback(() => {
  doSomething(id);
}, [id]);

// Utiliser quand passé à un composant mémorisé
const MemoizedChild = React.memo(Child);
<MemoizedChild onClick={handleClick} />;
```

### useMemo

```jsx
// ✅ Calculs coûteux
const expensiveValue = useMemo(() => {
  return computeExpensiveValue(a, b);
}, [a, b]);

// ❌ Pas nécessaire pour calculs simples
const total = useMemo(() => a + b, [a, b]); // Trop simple
```

### useRef

```jsx
// Référence DOM
const inputRef = useRef(null);
useEffect(() => {
  inputRef.current?.focus();
}, []);
<input ref={inputRef} />;

// Valeur persistante (ne déclenche pas de re-render)
const countRef = useRef(0);
countRef.current += 1;
```

### Custom Hooks

```jsx
// ✅ Extraire la logique réutilisable
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;

    fetch(url)
      .then((res) => res.json())
      .then((data) => {
        if (!cancelled) {
          setData(data);
          setLoading(false);
        }
      })
      .catch((err) => {
        if (!cancelled) {
          setError(err);
          setLoading(false);
        }
      });

    return () => {
      cancelled = true;
    };
  }, [url]);

  return { data, loading, error };
}

// Utilisation
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);

  if (loading) return <Spinner />;
  if (error) return <Error />;
  return <div>{user.name}</div>;
}
```

---

## 🎨 Styling

### CSS Modules

```jsx
// UserCard.module.css
import styles from "./UserCard.module.css";

function UserCard() {
  return (
    <div className={styles.card}>
      <h2 className={styles.title}>Title</h2>
    </div>
  );
}
```

### Styled Components

```jsx
import styled from "styled-components";

const Card = styled.div`
  padding: 20px;
  background: ${(props) => (props.primary ? "blue" : "white")};
`;

const Title = styled.h2`
  font-size: 24px;
  color: ${(props) => props.theme.colors.primary};
`;

function UserCard({ isPrimary }) {
  return (
    <Card primary={isPrimary}>
      <Title>Title</Title>
    </Card>
  );
}
```

### Conditional Classes

```jsx
// Avec classnames library
import classNames from 'classnames';

<div className={classNames('card', {
  'card--active': isActive,
  'card--disabled': isDisabled
})} />

// Sans library
<div className={`card ${isActive ? 'card--active' : ''}`} />
```

---

## 🚀 Performance

### React.memo

```jsx
// ✅ Mémoriser les composants
const UserCard = React.memo(({ user }) => {
  return <div>{user.name}</div>;
});

// Avec fonction de comparaison personnalisée
const UserCard = React.memo(
  ({ user }) => <div>{user.name}</div>,
  (prevProps, nextProps) => prevProps.user.id === nextProps.user.id,
);
```

### Lazy Loading

```jsx
import { lazy, Suspense } from "react";

// ✅ Charger les composants à la demande
const HeavyComponent = lazy(() => import("./HeavyComponent"));

function App() {
  return (
    <Suspense fallback={<Spinner />}>
      <HeavyComponent />
    </Suspense>
  );
}
```

### Code Splitting par Route

```jsx
import { lazy, Suspense } from "react";
import { BrowserRouter, Routes, Route } from "react-router-dom";

const Home = lazy(() => import("./pages/Home"));
const About = lazy(() => import("./pages/About"));

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<Spinner />}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}
```

### Éviter les re-renders inutiles

```jsx
// ❌ Crée une nouvelle fonction à chaque render
<Button onClick={() => handleClick(id)} />

// ✅ Utiliser useCallback
const handleButtonClick = useCallback(() => {
  handleClick(id);
}, [id]);
<Button onClick={handleButtonClick} />

// ❌ Objet créé à chaque render
<Component style={{ margin: 10 }} />

// ✅ Extraire en constante
const style = { margin: 10 };
<Component style={style} />
```

---

## 🏛️ Architecture et Patterns

### Composition vs Props Drilling

```jsx
// ✅ Composition (préféré)
function Layout({ children, sidebar }) {
  return (
    <div>
      <aside>{sidebar}</aside>
      <main>{children}</main>
    </div>
  );
}

<Layout sidebar={<Sidebar />}>
  <Content />
</Layout>;

// Render props
function DataProvider({ render }) {
  const [data, setData] = useState([]);
  return render(data);
}

<DataProvider render={(data) => <List items={data} />} />;
```

### Container/Presentational Pattern

```jsx
// Presentational Component (UI uniquement)
function UserListUI({ users, onUserClick }) {
  return (
    <ul>
      {users.map((user) => (
        <li key={user.id} onClick={() => onUserClick(user)}>
          {user.name}
        </li>
      ))}
    </ul>
  );
}

// Container Component (logique)
function UserListContainer() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetchUsers().then(setUsers);
  }, []);

  const handleUserClick = (user) => {
    console.log("Clicked:", user);
  };

  return <UserListUI users={users} onUserClick={handleUserClick} />;
}
```

### Higher-Order Components (HOC)

```jsx
// ✅ HOC pour la logique réutilisable
function withAuth(Component) {
  return function AuthComponent(props) {
    const { isAuthenticated } = useAuth();

    if (!isAuthenticated) {
      return <Redirect to="/login" />;
    }

    return <Component {...props} />;
  };
}

const ProtectedPage = withAuth(Dashboard);
```

---

## 🔄 State Management

### Context API

```jsx
// Créer un contexte
const UserContext = createContext(null);

// Provider
function UserProvider({ children }) {
  const [user, setUser] = useState(null);

  const login = useCallback((userData) => {
    setUser(userData);
  }, []);

  const logout = useCallback(() => {
    setUser(null);
  }, []);

  const value = useMemo(
    () => ({
      user,
      login,
      logout,
    }),
    [user, login, logout],
  );

  return <UserContext.Provider value={value}>{children}</UserContext.Provider>;
}

// Hook personnalisé
function useUser() {
  const context = useContext(UserContext);
  if (!context) {
    throw new Error("useUser must be used within UserProvider");
  }
  return context;
}

// Utilisation
function Profile() {
  const { user, logout } = useUser();
  return <div>{user.name}</div>;
}
```

### useReducer pour état complexe

```jsx
const initialState = { count: 0, step: 1 };

function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { ...state, count: state.count + state.step };
    case "decrement":
      return { ...state, count: state.count - state.step };
    case "setStep":
      return { ...state, step: action.payload };
    case "reset":
      return initialState;
    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
    </div>
  );
}
```

---

## 🧪 Tests

### Testing Library

```jsx
import { render, screen, fireEvent, waitFor } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

describe("UserCard", () => {
  it("renders user name", () => {
    const user = { id: 1, name: "John" };
    render(<UserCard user={user} />);

    expect(screen.getByText("John")).toBeInTheDocument();
  });

  it("calls onEdit when button is clicked", async () => {
    const handleEdit = jest.fn();
    const user = { id: 1, name: "John" };

    render(<UserCard user={user} onEdit={handleEdit} />);

    const button = screen.getByRole("button", { name: /edit/i });
    await userEvent.click(button);

    expect(handleEdit).toHaveBeenCalledWith(user);
  });

  it("displays loading state", () => {
    render(<UserCard loading />);
    expect(screen.getByTestId("spinner")).toBeInTheDocument();
  });
});
```

---

## 🔒 Sécurité

### XSS Protection

```jsx
// ✅ React échappe automatiquement
<div>{userInput}</div>

// ❌ Dangereux - éviter
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// ✅ Si vraiment nécessaire, sanitize first
import DOMPurify from 'dompurify';
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(html) }} />
```

### Validation des Props

```jsx
import PropTypes from 'prop-types';

UserCard.propTypes = {
  user: PropTypes.shape({
    id: PropTypes.oneOfType([PropTypes.string, PropTypes.number]).isRequired,
    name: PropTypes.string.isRequired,
    email: PropTypes.string,
  }).isRequired,
  onEdit: PropTypes.func,
  isActive: PropTypes.bool,
};

// Ou avec TypeScript
interface UserCardProps {
  user: {
    id: string | number;
    name: string;
    email?: string;
  };
  onEdit?: (user: User) => void;
  isActive?: boolean;
}
```

---

## 📝 TypeScript avec React

### Typage des composants

```tsx
import { FC, ReactNode } from "react";

// Avec FC
const Button: FC<{ onClick: () => void }> = ({ onClick, children }) => {
  return <button onClick={onClick}>{children}</button>;
};

// Sans FC (préféré)
interface ButtonProps {
  onClick: () => void;
  children: ReactNode;
  variant?: "primary" | "secondary";
}

function Button({ onClick, children, variant = "primary" }: ButtonProps) {
  return <button onClick={onClick}>{children}</button>;
}
```

### Typage des Hooks

```tsx
const [user, setUser] = useState<User | null>(null);
const [count, setCount] = useState<number>(0);

// useRef
const inputRef = useRef<HTMLInputElement>(null);

// Custom hook
function useFetch<T>(url: string): { data: T | null; loading: boolean } {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch(url)
      .then((res) => res.json())
      .then((data: T) => {
        setData(data);
        setLoading(false);
      });
  }, [url]);

  return { data, loading };
}
```

---

## 📚 Ressources

- [React Documentation](https://react.dev/)
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)
- [Testing Library](https://testing-library.com/react)
- [React Patterns](https://reactpatterns.com/)
- [Bulletproof React](https://github.com/alan2207/bulletproof-react)
