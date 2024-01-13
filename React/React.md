# Cheatsheet React

- [Index](/Readme.md)
- [Doc](https://fr.react.dev/)

## Installation

```bash
npx create-react-app nom-du-projet

npm create vite@latest
```

## Composant Fonctionnel :

```jsx
import React from "react";

function MonComposant(props) {
  return (
    <div>
      <h1>{props.titre}</h1>
      <p>{props.contenu}</p>
    </div>
  );
}
```

## Composant de Classe :

```jsx
import React, { Component } from "react";

class MonComposantClasse extends Component {
  render() {
    return (
      <div>
        <h1>{this.props.titre}</h1>
        <p>{this.props.contenu}</p>
      </div>
    );
  }
}
```

## Utilisation des Propriétés (Props) :

```jsx
<MonComposant titre="Titre" contenu="Contenu du composant" />
```

## État (State) :

```jsx
import React, { useState } from "react";

function ComposantAvecEtat() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Compteur: {count}</p>
      <button onClick={() => setCount(count + 1)}>Incrémenter</button>
    </div>
  );
}
```

## Gestion des Événements :

### Au clic :

```jsx
function ComposantAvecEvenement() {
  const handleClick = () => {
    // Code à exécuter lors du clic
  };

  return <button onClick={handleClick}>Cliquez-moi</button>;
}
```

### Au changement :

```jsx
function handleChange(event) {
  // Code à exécuter lors du changement
}

return <input type="text" onChange={handleChange} />;
```

## Rendu conditionnel :

```jsx
function ComposantConditionnel({ condition }) {
  return <div>{condition ? <p>Vrai</p> : <p>Faux</p>}</div>;
}
```

## Liste et Clés :

```jsx
function ListeDeComposants({ elements }) {
  return (
    <ul>
      {elements.map((element, index) => (
        <li key={index}>{element}</li>
      ))}
    </ul>
  );
}
```

## Utilisation de l'État et des Propriétés dans le même composant :

```jsx
function ComposantAvecEtatEtProps({ props }) {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(count + 1);
  };

  return (
    <div>
      <h1>{titre}</h1>
      <p>Compteur: {count}</p>
      <button onClick={handleClick}>Incrémenter</button>
    </div>
  );
}
```

## Formulaires :

```jsx
import React, { useState } from "react";

function Formulaire() {
  const [valeur, setValeur] = useState("");

  const handleChange = (e) => {
    setValeur(e.target.value);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    // Code à exécuter lors de la soumission du formulaire
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" value={valeur} onChange={handleChange} />
      <button type="submit">Envoyer</button>
    </form>
  );
}
```

## Requête fetch

### GET

```js
const fetchData = async () => {
  try {
    const response = await fetch("https://api.example.com/data");

    const result = await response.json();

    console.log("GET request result:", result);
  } catch (error) {
    console.error("Error fetching data:", error);
  }
};
```

### Post

```js
const fetchData = async () => {
  try {
    const response = await fetch("https://api.example.com/create", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },

      body: JSON.stringify({
        key: "value",
      }),
    });

    const result = await response.json();

    console.log("POST request result:", result);
  } catch (error) {
    console.error("Error making POST request:", error);
  }
};
```

### Put

```js
const fetchData = async () => {
  try {
    const response = await fetch("https://api.example.com/update", {
      method: "PUT",
      headers: {
        "Content-Type": "application/json",
      },

      body: JSON.stringify({
        key: "updatedValue",
      }),
    });
    const result = await response.json();

    console.log("PUT request result:", result);
  } catch (error) {
    console.error("Error making PUT request:", error);
  }
};
```

### Patch

```js
const fetchData = async () => {
  try {
    const response = await fetch("https://api.example.com/patch", {
      method: "PATCH",
      headers: {
        "Content-Type": "application/json",
      },

      body: JSON.stringify({
        key: "updatedValue",
      }),
    });
    const result = await response.json();

    console.log("PATCH request result:", result);
  } catch (error) {
    console.error("Error making PATCH request:", error);
  }
};
```

### Delete

```js
const fetchData = async () => {
  try {
    const response = await fetch("https://api.example.com/delete", {
      method: "DELETE",
    });

    const result = await response.data;

    console.log("DELETE request result:", result);
  } catch (error) {
    console.error("Error making DELETE request:", error);
  }
};
```

## Requête Axios

### GET

```js
const fetchData = async () => {
  try {
    const response = await axios.get("https://api.example.com/data");

    const result = await response.data;

    console.log("GET request result:", result);
  } catch (error) {
    console.error("Error fetching data:", error);
  }
};
```

### Post

```js
const fetchData = async () => {
  try {
    const response = await axios.post("https://api.example.com/create");

    const result = await response.data;

    console.log("POST request result:", result);
  } catch (error) {
    console.error("Error making POST request:", error);
  }
};
```

### Put

```js
const fetchData = async () => {
  try {
    const response = await axios.put("https://api.example.com/update");

    const result = await response.data;

    console.log("PUT request result:", result);
  } catch (error) {
    console.error("Error making PUT request:", error);
  }
};
```

### Patch

```js
const fetchData = async () => {
  try {
    const response = await axios.patch("https://api.example.com/patch");

    const result = await response.data;

    console.log("PATCH request result:", result);
  } catch (error) {
    console.error("Error making PATCH request:", error);
  }
};
```

### Delete

```js
const fetchData = async () => {
  try {
    const response = await axios.delete("https://api.example.com/delete");

    const result = await response.data;

    console.log("DELETE request result:", result);
  } catch (error) {
    console.error("Error making DELETE request:", error);
  }
};
```
