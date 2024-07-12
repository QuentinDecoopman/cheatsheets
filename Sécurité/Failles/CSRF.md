# CSRF

### **Qu'est-ce que la falsification de requête inter-sites (CSRF) ?**

La falsification de requête inter-sites (CSRF) est une vulnérabilité web qui permet à un hacker malveillant de tromper la victime afin qu'elle soumette une requête permettant à l'attaquant d'effectuer des actions modifiant l'état au nom de la victime. La falsification de requête inter-sites est également appelée XSRF, sea surf, session riding, ou attaque à un clic.

- **Gravité :** grave dans des circonstances rares
- **Prévalence :** souvent découverte
- **Portée :** applications web avec authentification
- **Impact technique :** déclenchement d'actions non autorisées par l'attaquant
- **Conséquences potentielles graves :** dépendent des capacités de l'application
- **Correction rapide :** utilisation de jetons anti-CSRF

### **Comment fonctionne la falsification de requête inter-sites ?**

La plupart des applications web nécessitent une authentification et certains utilisateurs authentifiés peuvent effectuer des actions très sensibles. L'authentification dans les applications web est souvent basée sur des sessions utilisateur. Après l'authentification, votre navigateur stocke un cookie de session sur votre ordinateur et l'envoie avec chaque requête que vous faites à cette application web.

Lorsque vous utilisez une application, de nombreuses requêtes HTTP envoyées depuis votre navigateur vers l'application sont le résultat de vos actions explicites, par exemple lorsque vous saisissez une URL dans la barre d'adresse ou cliquez sur un lien. Cependant, d'autres requêtes HTTP sont envoyées implicitement par votre navigateur lorsqu'il traite du code inclus sur la page web en cours. Par exemple, si la page inclut une image, celle-ci sera récupérée par une requête HTTP distincte.

Ces requêtes implicites peuvent également être dirigées vers des domaines sans lien avec la page que vous consultez. Par exemple, une image affichée sur testinvicti.com peut en réalité provenir de example.com. L'élément crucial dans de tels cas est que les requêtes vers les deux emplacements proviennent du même navigateur, de sorte que votre méthode d'authentification actuelle (qu'il s'agisse d'un cookie de session ou d'une autre méthode) s'applique aux deux emplacements. Ainsi, si votre navigateur ouvre testinvicti.com et récupère une image depuis example.com, créant ainsi une session utilisateur sur example.com, l'application web de example.com vous considérera comme un utilisateur authentifié (même si vous avez ouvert initialement testinvicti.com, pas example.com).

Ces deux comportements combinés peuvent être exploités pour mener des attaques de falsification de requête inter-sites de la manière suivante :

1. La victime est authentifiée dans l'application web cible (comme example.com).
2. L'attaquant utilise l'ingénierie sociale pour inciter la victime à visiter un site web malveillant (par exemple, testinvicti.com).
3. La page web malveillante inclut du code (comme une balise image) qui amène le navigateur de la victime à envoyer une requête implicite à la cible (comme example.com).
4. La requête malveillante amène la cible à effectuer des actions non intentionnelles par l'utilisateur. Les conséquences varieront selon l'application.

### **Types de vulnérabilités de falsification de requête inter-sites**


Les vulnérabilités CSRF peuvent être basées sur des requêtes GET ou POST.

- Dans le cas de CSRF basé sur des requêtes GET, l'attaquant peut simplement utiliser une balise image (ou toute autre balise permettant des requêtes inter-sites) sur une page malveillante :

```html
`<img src="http://example.com/bank.php/?action=transfer&target=attacker_account">`
```

Lorsque l'utilisateur visite la page avec la balise image ci-dessus (par exemple, après avoir cliqué sur un lien malveillant), le navigateur de l'utilisateur tente d'ouvrir l'image mais fait plutôt une requête GET vers le site ciblé, effectuant ainsi une action malveillante tout en étant connecté au compte de l'utilisateur. Supposons que l'utilisateur soit authentifié sur example.com, l'application web ne pourra pas distinguer une demande légitime de l'utilisateur d'une demande malveillante, car elles sont toutes deux envoyées depuis le même navigateur.

- Dans le cas de CSRF basé sur des requêtes POST, l'attaquant doit travailler un peu plus. La manière la plus simple de mener une telle attaque est de forcer le navigateur de l'utilisateur à soumettre automatiquement un formulaire à l'aide de JavaScript :

```html
<body onload="document.csrf.submit()"> 
<form action="http://example.com/bank.php" method="POST" name="csrf">     
	<input type="hidden" name="action" value="transfer">     
	<input type="hidden" name="target" value="attacker_account"> 
</form>
```


L'argument onload de la balise "body" amènera le navigateur à soumettre le formulaire dès que l'utilisateur ouvre la page malveillante.

### **Exemple d'une attaque de falsification de requête inter-sites**

Le développeur d'une application métier financière crée une fonction permettant aux utilisateurs de définir l'adresse e-mail à utiliser pour recevoir quotidiennement des rapports financiers depuis l'application. Pour définir ou modifier l'adresse e-mail, un utilisateur authentifié doit remplir un formulaire HTML sur la page [http://example.com/set_email.php](http://example.com/set_email.php) :

```html
<form action="/set_email.php" method="post" id="set_email">
<label for="email">Entrez l'adresse e-mail pour recevoir les rapports :</label>     <input type="email" id="email" name="email">     
<button type="submit" form="submit" value="submit">Définir l'e-mail</button>     </form>
```


L'attaquant crée une page malveillante [http://example.attacker/exploit.html](http://example.attacker/exploit.html) avec le contenu suivant :

```html
`<body onload=document.email.submit()>
	<form action="http://example.com/set_email.php" method="post" name="email">         <input type="hidden" id="email" value="attacker@example.attacker">     
	</form> 
</body>`
```

Ensuite, l'attaquant crée un e-mail de phishing et l'envoie à un utilisateur de l'application financière, incitant l'utilisateur à visiter [http://example.attacker/set_email.html](http://example.attacker/set_email.html). En supposant que l'utilisateur soit déjà connecté à l'application sur example.com, l'application recevra la requête falsifiée et modifiera l'adresse e-mail de rapport pour attacker@example.attacker. En conséquence, l'attaquant recevra quotidiennement des rapports sensibles sur les opérations financières de l'entreprise.

### **Conséquences potentielles d'une attaque de falsification de requête inter-sites**

Les vulnérabilités de falsification de requête inter-sites sont considérées comme d'une gravité moyenne pour plusieurs raisons :

- Dans ce type d'attaque, l'attaquant ne reçoit jamais de réponse HTTP et ne peut donc pas utiliser cette technique pour lire ou accéder directement à des informations sensibles. Ils n'ont même pas accès à la valeur du cookie de session qui est envoyé avec la requête malveillante.
- L'attaque est limitée par les fonctionnalités de l'application web, ou plus précisément par ce que l'application permet à l'utilisateur actuel de faire à l'aide d'une requête modifiant l'état. Par exemple, si vous avez un système de billetterie et que l'utilisateur actuel ne peut que créer et résoudre des problèmes, le plus qu'un attaquant puisse accomplir avec CSRF est de vider la file d'attente des tickets. Par exemple, ils ne pourront pas obtenir les identifiants de l'administrateur.
- Ce type d'attaque est le plus efficace lorsqu'il vise une personne spécifique ou un petit groupe de personnes ayant des privilèges élevés. Contrairement aux scripts intersites (XSS), il n'a souvent pas de sens d'envoyer une charge utile CSRF malveillante à un grand nombre de victimes aléatoires. La CSRF est généralement soigneusement préparée pour exploiter un utilisateur spécifique dans l'entreprise, comme le PDG, l'administrateur ou un employé du département financier.

### **Exemples de vulnérabilités connues de type Cross-Site Request Forgery (CSRF)**

En raison de la nature du CSRF, aucun incident majeur n'est connu pour avoir été causé par des attaques CSRF réussies. Cependant, par le passé, plusieurs applications web populaires ont été découvertes vulnérables au CSRF et auraient pu être exploitées dans le cadre d'attaques ciblées :

- **Netflix** : En 2006, lorsque Netflix était encore un service de location de DVD, une vulnérabilité CSRF a été découverte, permettant à un attaquant de modifier les identifiants et de prendre complètement le contrôle d'un compte.
- **ING** : En 2008, des chercheurs ont découvert des vulnérabilités CSRF sur ingdirect.com qui auraient permis à un attaquant d'ouvrir des comptes bancaires au nom de la victime et de transférer des fonds depuis son compte.
- **WordPress** : En 2020, des chercheurs ont identifié des vulnérabilités CSRF dans 25 plugins WordPress populaires.

Ces exemples ne représentent qu'une partie des cas recensés, et bien que les conséquences exactes de ces vulnérabilités ne soient pas largement connues, il est possible qu'elles aient été utilisées pour des attaques ciblées qui n'ont simplement pas été médiatisées.

### **Comment détecter les vulnérabilités de type Cross-Site Request Forgery (CSRF) ?**

La meilleure façon de détecter les vulnérabilités CSRF varie selon qu'elles sont déjà connues ou inconnues.

- Si vous n'utilisez que des applications web commerciales ou open source sans développer vos propres applications web, il peut suffire d'identifier précisément la version de l'application que vous utilisez. Si cette version est sujette au CSRF, vous pouvez supposer que votre site web est vulnérable. Vous pouvez identifier la version manuellement ou utiliser un outil de sécurité approprié, tel qu'une solution d'analyse de composition logicielle (SCA).

- Si vous développez vos propres applications web ou si vous souhaitez avoir la possibilité de détecter des vulnérabilités CSRF inconnues (zero-days) dans des applications connues, vous devez être capable d'exploiter avec succès la vulnérabilité CSRF pour en être certain. Cela nécessite soit de réaliser des tests de pénétration manuels avec l'aide de chercheurs en sécurité, soit d'utiliser un outil de test de sécurité (scanner) capable d'automatiser l'exploitation des vulnérabilités web. Des exemples d'outils incluent Invicti et Acunetix par Invicti.


### **Comment prévenir les vulnérabilités de type Cross-Site Request Forgery (CSRF) dans les applications web ?**

La méthode principale pour se protéger contre les attaques CSRF est de permettre à l'application web de différencier les requêtes légitimes (effectuées au nom de l'application) des requêtes potentiellement malveillantes (envoyées par l'application sous influence externe). Les deux techniques suivantes sont les plus efficaces et les plus largement utilisées :

- **Jetons Anti-CSRF** : Cette technique repose sur l'envoi d'un jeton spécial avec chaque requête légitime et sur la validation systématique de ce jeton à la réception des requêtes. Ce jeton, parfois appelé jeton synchronisateur, est généré côté serveur et les attaquants ne peuvent pas connaître sa valeur correcte, connue uniquement de l'application web et du navigateur. Les tentatives d'attaques CSRF ne possédant pas ce jeton valide permettent à l'application de les ignorer comme invalides, de les journaliser comme tentatives d'attaque, voire de déclencher une alerte.

- **Cookies SameSite** : Une autre méthode efficace consiste à différencier les requêtes légitimes des potentiellement dangereuses en examinant l'origine de la requête. Les navigateurs modernes supportent l'attribut SameSite pour les cookies, ce qui permet de définir des politiques comme Lax, Strict ou None pour contrôler leur comportement dans les contextes d'utilisation.


D'autres techniques de protection existent, mais les jetons anti-CSRF et les cookies SameSite sont considérés comme les meilleurs moyens de prévention. Il est recommandé de les utiliser ensemble pour une sécurité optimale contre les attaques CSRF.

### **Comment atténuer les attaques de type Cross-Site Request Forgery (CSRF) ?**

La meilleure façon d'atténuer les attaques CSRF est de s'assurer que tous les utilisateurs utilisent des navigateurs web modernes et complètement mis à jour qui supportent les cookies SameSite. Cela permet aux applications web d'utiliser les cookies SameSite comme principale protection contre CSRF sans nécessiter d'autres mesures de sécurité dans le code des applications web.

Il est possible d'imposer cette mitigation en restreignant l'accès aux applications web aux navigateurs qui ne supportent pas les cookies SameSite, mais cette approche peut affecter négativement les utilisateurs (par exemple, en bloquant tous les utilisateurs d'Internet Explorer) et devrait donc être introduite avec prudence.
