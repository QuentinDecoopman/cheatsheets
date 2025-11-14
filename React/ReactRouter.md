# React router

## Installation

```bash
npm install react-router-dom

yarn add react-router-dom
```

## Fichier main

```js
const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <React.StrictMode>
    <RouterProvider router={router}>
      <App />
    </RouterProvider>
  </React.StrictMode>
);
```

## CreateBrowserRouter

```js
// Dans le fichier main ou un fichier router

import * as React from "react";
import * as ReactDOM from "react-dom";
import { createBrowserRouter } from "react-router-dom";

export const router = createBrowserRouter([
  {
    path: "/",
    element: <App />,
    errorElement: <ErrorPage />,
    children: [
      {
        index: true,
        element: <HomePage />,
      },
      {
        path: "about",
        element: <AboutPage />,
      },
    ],
  },
]);