# Différents types de rendus

Type de rendu|Avantages|Inconvénients|Exemples de pages|
|---|---|---|---|
|**Client-Side Rendering (CSR)**|Interaction rapide après le chargement initial, idéal pour les applications SPA.|Temps de chargement initial plus lent, mauvais SEO car le contenu n'est pas pré-rendu.|Page avec contenu interactif ou dynamique (ex : tableau de bord utilisateur).|
|**Server-Side Rendering (SSR)**|Contenu toujours à jour à chaque requête, bon pour le SEO et les réseaux sociaux grâce au contenu pré-rendu.|Charge serveur élevée, surtout avec beaucoup de trafic, temps de réponse plus lent comparé au SSG.|Pages nécessitant des données dynamiques en temps réel (ex : résultats de recherche).|
|**Static Site Generation (SSG)**|Performances élevées grâce au contenu généré à la construction, faible charge serveur et excellent SEO.|Nécessite une reconstruction complète pour mettre à jour le contenu.|Pages de blog ou documentation statique.|
|**Incremental Static Regeneration (ISR)**|Combinaison des avantages du SSG et SSR : mise à jour incrémentale du contenu statique, évolutif pour les sites à fort trafic.|Complexité accrue dans la gestion du cache et des revalidations, données légèrement obsolètes entre les intervalles de revalidation.|Pages avec contenu semi-dynamique (ex : articles avec likes ou commentaires).|
|**Server Components**|Permettent de séparer le code côté serveur et côté client, améliore la sécurité et la performance en évitant de transmettre des données sensibles au client.|Nécessite une compréhension approfondie de l'architecture côté serveur, peut augmenter la complexité du code.|Pages nécessitant une logique serveur sécurisée, comme des formulaires de connexion ou des transactions financières.|

1. **CSR** est idéal pour les applications nécessitant beaucoup d'interactivité côté client.

2. **SSR** est adapté aux pages dynamiques nécessitant un contenu frais à chaque requête.

3. **SSG** convient aux sites statiques où le contenu change rarement.

4. **ISR** est parfait pour les sites avec des mises à jour fréquentes mais non critiques en temps réel.

5. **Server Components** sont utiles pour sécuriser et optimiser les interactions côté serveur, en particulier pour les données sensibles.


Chaque méthode peut être combinée dans une application Next.js selon les besoins spécifiques des pages ou composants.
