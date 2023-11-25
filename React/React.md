# React

- [Index](/Readme.md)

## Installation

```bash
npx create-react-app nom-du-projet
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

```jsx
function ComposantAvecEvenement() {
  const handleClick = () => {
    // Code à exécuter lors du clic
  };

  return <button onClick={handleClick}>Cliquez-moi</button>;
}
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
function ComposantAvecEtatEtProps({ titre }) {
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
