## Closures

Le concept de closure est fondamental en Javascript. Il permet à une fonction enfant de conserver l’accès aux variables de la fonction parente. Et ce, même après que la fonction parente ait terminé son exécution.

Cette particularité unique joue un rôle crucial dans les patterns de conception comme les factories et les modules. En effet, elle offre une puissante manière de gérer le contenu privé et les données.

En outre, les closures sont au cœur de nomrbeux patterns de conception. En effet, en plus de permettre la création d’interfaces publiques, il cache tous les détails d’implémentation internes.

## Hoisting

C’est un comportement de JavaScript où les déclarations de variables et les déclarations de fonction sont déplacées au sommet de leur contexte d’éxécution avant l’éxécution du code. De manière pratique, cela donne :

Toutefois, il faut bien assimiler le concept avant de s’y essayer. Car, le hoisting peut entraîner des bugs et des confusions dans l’écriture du code.

## L’event Loop et le modèle de concurrence

Ces deux concepts sont indispensables pour comprendre comment JavaScript traite le code asynchrone. Étroitement liés, l’event loop et le modèle de concurrence permette à ce langage à thread unique de gérer plusieurs tâches efficacement.

### L’event Loop

C’est un processus qui permet à JavaScript de réaliser des opérations non bloquantes. Pour cela, il éxécute du code, collecte et traite des événements et en exécutant des les sous-tâches dans une boucle.

Bien que JavaScript soit un langage à thread unique, l’event loop gère de manière efficace les tâches asynchrone comme les requêtes réseau ou les temporisations.

### Le modèle de concurrence

Le modèle de concurrence décrit comment JavaScript traite les tâches synchrones en parallèle avec les tâches asynchrones. Ce modèle est indispensable au bon développement d’applications web réactives.

Pour bien fonctionner, le modèle de concurrence a besoin des Promises et Async/Await. Les Promises représentent une valeur qui peut être disponible à tout moment ou jamais. Quant à Aync/Await, il permet d’écrire du code asynchrone qui a l’air synchrone.

Ainsi, le code est plus lisible et facile à comprendre.

## Programmation Orientée Objet (POO) en JavaScript

La programmation orientée objet est un paradigme fondamental en développement logiciel. Et, JavaScript dispose de ses propres mécanismes pour l’implémenter.

Bien que ce ne soit pas un langage purement orienté objet, une compréhension approfondie de la POO en JavaScript est cruciale pour tout développeur senior.

### Prototypes et Héritage

JavaScript utiliste un système de prototypes pour faire hériter des propriétés et des méthodes à des objets. Chaque objet a une propriété interne appelée prototype. Ce prototype pointe lui-même sur un autre objet. Et ainsi de suite. Ce sont ces liens qui sont utilisés pour l’héritage.

Les prototypes sont au cœur de la Programmation Orientée Objet car ils permettent de réutiliser le code. Cette manoeuvre a pour effet direct de réduire la redondance.

Toutefois, il faudra faire attention à ajouter ou modifier des prototypes intégrés comme ‘Array.prototype’ ou ‘Object.prototype’. Car, cela peut conduire à des comportements inattendus ou des conflits dans le code.

### Classes en ES6 (150 mots)

Afin de simplifier la POO, JavaScript a introduit les classes en ES6. Ces classes fournissent une syntaxe plus claire et plus déclarative pour créer des objets et gérer l’héritage.

Les classes ES6 fusionnent le comporteent des fonctions constructeurs et des prototypes dans une forme plus simple à comprendre et à maintenir.

Enfin, pour créer l’héritage de la classe ‘Animal’, il suffit d’utiliser le mot-clé ‘extends’. Si l’on admet que l’on veut étendre la classe ‘Animal’ à une classe ‘Chien’, cela donne :

## Les concepts avancés

Une fois les bases de JavaScript solidement ancrées, il est temps de se tourner vers des concepts plus avancés qui distinguent vraiment les développeurs seniors.

### Promises, Async/Await

En JavaScript, les Promises sont des objets utilisés pour le traitement asynchrone. Elles sont particulièrement utiles pour gérer des séquences d’opérations asynchrones où il est question de chaîner plusieurs traitements. Les Promises peuvent avoir 3 états différents :

- Pending (En attente). C’est l’état initial des Promises, ni résolue, ni rejetée ;
- Fulfilled (Résolue). Ici, l’opération ansynchrone s’est terminée avec succès et la Promise possède une valeur ;
- Rejected (Rejetée). Dans cet état, l’opération asynchrone a échoué et la Promise a une raison d’échec.

Quant à l’Async/Await, c’est une syntaxe qui permet de travailler avec des Promises de manière plus lisible et confortable.Car, Async/Await permet d’écrire des opérations asynchrones en utilisant du code asynchrone. Cette manoeuvre rend le code plus lisible.

Dans un premier temps, Async marque une fonction comme asynchrone. Cela implique que la fonction renvoie à une Promise.

Dans un second temps, Await peut être utilisé au sein d’une fonction Async pour attendre la résolution de la Promise liée. Cette procédure met en pause l’exécution de la fonction asynchrone jusqu’à la résolution ou au rejet de la Promise.

## Patterns de conception

En JavaScript, les patterns de conception sont des modèles utilisés pour résoudre des problèmes récurrents. Ce sont des solutions standards permettant aux développeurs de communiquer en utilisant des noms de patterns connus. Cela leur permet également de simplifier la structure du code.

### Singleton

Ce pattern garantit qu’une classe n’a qu’une seule instance. Elle fournit un point d’accès global à cette instance.

### Factory

C’est un pattern de conception créatif. Il utilise une méthode de fabrication pour créer des objets sans spécifier la classe exacte de l’objet créé.

### Observer

Ce pattern sert à créer une souscription. Ici, un objet attend et réagit aux événements ou changement d’état d’un autre objet.

### Decorator

Grâce à ce pattern, il est possible d’ajouter des responsabilités supplémentaires à un objet sans en modifier sa structure interne. Le tout, de manière dynamique.

### Strategy

Ce pattern définit une famille d’algorithmes, encapsule chacun d’eux et les rend interchangeables. Le pattern Strategy permet à l’algorithme de varier indépendamment des clients qui l’utilisent.

## L’immutabilité

En JavaScript, l’immutabilité stipule qu’une fois une donnée créée, elle ne peut être modifiée. Ce principe rend le fonctionnement du programme plus prévisible. De plus, l’immutabilité prévient les effets de bord dans les fonctions.

L’immutabilité s’applique aussi aux tableaux.

## Les outils et l’écosystème

Au-delà de la maîtrise des concepts JavaScript eux-mêmes, tout développeur senior se doit de connaître les outils et l’écosystème qui entourent le langage.

Webpack et Babel sont deux pièces essentielles de la boîte à outils moderne, tandis que les frameworks et bibliothèques comme React, Angular et Vue.js ont profondément transformé les méthodologies de développement web.
