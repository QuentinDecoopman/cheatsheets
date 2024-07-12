# Les injections SQL


#### Qu'est-ce que l'injection SQL ?

L'injection SQL (SQLi) est une vulnérabilité qui permet à un pirate informatique malveillant d'introduire (injecter) du code SQL indésirable dans des requêtes SQL exécutées par le logiciel.

**Gravité :** Très sévère  
**Prévalence :** Découverte fréquemment  
**Portée :** Peut apparaître dans les logiciels utilisant SQL  
**Impact technique :** Accès à la base de données ou aux informations système  
**Conséquences les plus graves :** Compromission totale du système  
**Solution rapide :** Utiliser des instructions préparées, également appelées requêtes paramétrées

#### Comment fonctionne l'injection SQL ?

Si une application utilise une base de données externe, elle doit créer des requêtes vers cette base de données et en récupérer les résultats. La plupart des applications utilisent des bases de données relationnelles qui prennent en charge SQL (Structured Query Language). SQL est un langage textuel conçu pour être simple et facile à comprendre pour les humains. Les bases de données populaires prenant en charge SQL incluent Oracle, Microsoft SQL Server (MSSQL), MySQL, PostgreSQL, et plus encore.

Les requêtes vers les bases de données sont rarement statiques – les informations que l'application doit obtenir de la base de données ou y stocker dépendent généralement des données fournies par l'utilisateur. L'entrée utilisateur est généralement sous la forme de texte simple, tout comme la syntaxe SQL elle-même, donc les développeurs créent souvent des requêtes en concaténant directement les données fournies par l'utilisateur avec les instructions SQL. Par exemple, `SELECT first_name, last_name FROM users WHERE user_id = 'id_fournie_par_l_utilisateur'` retourne le prénom et le nom de l'utilisateur basé sur l'ID fourni par l'utilisateur.

Si aucune validation des entrées n'est effectuée, un pirate malveillant peut utiliser des formulaires de saisie sur des pages web ou des requêtes HTTP directes pour fournir une charge utile contenant des instructions SQL. Si l'application concatène simplement ces données utilisateur avec des commandes statiques, l'attaquant peut souvent complètement changer la syntaxe et la façon dont la requête d'origine fonctionne. Ils peuvent utiliser des caractères spéciaux tels que des guillemets simples ou des points-virgules pour ajouter des commandes et/ou ignorer les commandes statiques. Le code malveillant résultant peut même permettre à l'attaquant d'exécuter des commandes telles que DROP (supprimer une table de base de données ou même toute la base de données). C'est ce qu'on appelle une injection SQL.

Les injections SQL peuvent se produire dans tout logiciel communiquant avec des bases de données SQL. Elles sont les plus répandues dans la sécurité des applications web, car les applications web utilisent très souvent des serveurs SQL en back-end. Cependant, elles peuvent également se produire dans d'autres types d'applications et de systèmes.

Les injections SQL sont considérées comme l'une des vulnérabilités les plus anciennes – elles ont été documentées pour la première fois en 1998. Elles sont classées comme CWE-89 : Neutralisation incorrecte des éléments spéciaux utilisés dans une commande SQL et sont incluses dans la catégorie A3:2021 du Top 10 de l'OWASP (Injection).

#### Un exemple simple d'injection SQL

Voyons ce qu'un attaquant peut faire avec l'exemple simple d'authentification suivant :


```sql
SELECT * FROM users WHERE user_id = 'id_fournie_par_l_utilisateur' AND password = 'mot_de_passe_fournie_par_l_utilisateur'
```

Cette simple instruction SELECT retourne toutes les données utilisateur pertinentes s'il y a un enregistrement d'ID et de mot de passe correspondant dans la base de données. Cela signifie que si l'utilisateur fournit un ID et un mot de passe valides, la requête peut retourner le prénom et le nom d'un utilisateur (selon le schéma de la table des utilisateurs). Si l'utilisateur fournit un ID et/ou un mot de passe invalide, la requête retourne un ensemble de données vide. Un développeur pourrait utiliser cette simple requête pour vérifier si l'utilisateur peut se connecter.

Un pirate malveillant pourrait fournir la valeur suivante pour id_fournie_par_l_utilisateur :

`admin'--`

En conséquence, la chaîne de requête envoyée à la base de données deviendra :


```sql
SELECT * FROM users WHERE user_id = 'admin'--' AND password = ''
```

Le guillemet simple termine l'affectation de user_id et le double tiret (--) fait en sorte que le reste de l'instruction SQL (c'est-à-dire la vérification du mot de passe) soit traité comme un commentaire. Par conséquent, l'application exécute la requête suivante :


```sql
SELECT * FROM users WHERE user_id = 'admin'
```
`

Si exécutée, cette requête représente une injection SQL réussie. Elle retourne toutes les données utilisateur pour admin, permettant potentiellement au pirate malveillant d'obtenir un accès non autorisé à l'application en tant qu'administrateur.

#### Types de vulnérabilités d'injection SQL

Il existe 5 techniques principales d'injection SQL. Chacune d'elles est décrite en détail dans une section séparée de ce guide :

- Injection SQL en bande (basée sur les erreurs et basée sur la sélection union)
- Injection SQL inférentielle/aveugle (basée sur les booléens et basée sur le temps)
- Injection SQL hors bande

#### Conséquences potentielles d'une attaque d'injection SQL

L'injection SQL est l'une des vulnérabilités les plus graves pour deux raisons. Tout d'abord, les bases de données accessibles par les applications web contiennent souvent des informations hautement sensibles de grande valeur pour les attaquants. Par conséquent, les attaquants sont très intéressés à obtenir ces données.

Deuxièmement, exploiter des injections SQL en combinaison avec d'autres vulnérabilités courantes peut avoir des conséquences dramatiques. Il est même possible d'obtenir un accès au système d'exploitation via une injection SQL, ouvrant la voie à une prise de contrôle complète du système.

Les conséquences typiques des injections SQL incluent :

- Accès aux données sensibles stockées dans la base de données, telles que les mots de passe et/ou les numéros de cartes de crédit
- Accès aux informations sur la base de données et le système d'exploitation pour aider à d'autres attaques
- Si l'attaquant est capable d'utiliser une élévation de privilèges pour obtenir un accès au système d'exploitation et ensuite un accès root, il peut effectuer l'un des types d'attaques suivants :
    - Ransomware ou autre malware : L'attaquant peut installer un agent ransomware sur la machine. Le malware utilisera ensuite d'autres méthodes pour se propager à d'autres actifs appartenant à la victime, potentiellement paralysant des réseaux d'entreprise entiers.
    - Minage de cryptomonnaie : Les attaquants installent souvent des mineurs de cryptomonnaie sur des machines compromises. Le minage consomme vos ressources informatiques pour fournir des fonds aux cybercriminels pour des activités plus malveillantes.
    - Accès à d'autres systèmes : Même si le système actuel est de faible importance, il peut faciliter la cible de systèmes de haute priorité pour l'attaquant.

#### Exemples de vulnérabilités d'injection SQL connues

- **2021 : GabLeaks** – la plateforme de médias sociaux d'extrême droite Gab a été attaquée via une injection SQL qui a permis aux attaquants d'obtenir 70 gigaoctets de données.
- **2020 : Sophos XG Firewall** – l'une des entreprises de sécurité les plus renommées au monde a trouvé une injection SQL dans leur produit parce que des hackers malveillants l'avaient utilisée pour attaquer leurs clients.
- **2019 : Service TVA bulgare** – suite à une attaque par injection SQL, les enregistrements contenant les données fiscales de 5 millions de citoyens sur une période de 10 ans, y compris les noms complets, les informations de revenus, les numéros d'identification personnelle (EGN), le statut d'emploi, les prestations sociales et l'assurance maladie, sont devenus accessibles via des forums clandestins.
- **2014 : 420 000 sites web attaqués par un gang russe** – en utilisant des botnets et des injections SQL courantes, les cybercriminels ont pu rassembler 1,2 milliard de données d'identification à partir d'environ 420 000 sites web et applications web publics.

#### Comment détecter les vulnérabilités d'injection SQL ?

La meilleure façon de détecter les vulnérabilités SQLi varie selon qu'elles sont déjà connues ou inconnues.

- Si vous n'utilisez que des logiciels commerciaux ou open-source et ne développez pas vos propres logiciels, il peut suffire d'identifier la version exacte du système ou de l'application que vous utilisez. Si la version identifiée est susceptible à une SQLi, vous pouvez supposer que votre logiciel est vulnérable. Vous pouvez identifier la version manuellement ou utiliser un outil de sécurité approprié, tel qu'une solution d'analyse de composition logicielle (SCA) pour les applications web ou un scanner de réseau pour les systèmes et applications en réseau.


- Si vous développez vos propres logiciels ou souhaitez être en mesure de potentiellement trouver des vulnérabilités SQLi inconnues (zero-days) dans des applications connues, vous devez être en mesure d'exploiter avec succès la vulnérabilité SQLi pour être certain qu'elle existe. Cela nécessite soit de réaliser des tests de pénétration manuels avec l'aide de chercheurs en sécurité, soit d'utiliser un outil de scanner de vulnérabilités capable d'utiliser l'automatisation pour exploiter les vulnérabilités web. Des exemples de tels outils sont Invicti et Acunetix by Invicti. Nous recommandons d'utiliser cette méthode même pour des vulnérabilités connues.

#### Comment prévenir les vulnérabilités d'injection SQL dans les applications web ?

La seule méthode entièrement efficace pour prévenir les vulnérabilités SQLi dans les applications web est d'utiliser des requêtes paramétrées (également appelées instructions préparées) pour accéder aux bases de données SQL. Les requêtes paramétrées sont disponibles dans presque tous les langages de programmation courants. Elles vous permettent d'éviter la concaténation de chaînes et de transmettre des paramètres en toute sécurité aux requêtes SQL. Si votre langage de programmation ne prend pas en charge les requêtes paramétrées mais que votre moteur de base de données prend en charge les procédures stockées, vous pouvez utiliser les procédures stockées avec des instructions préparées à la place.

Ne comptez pas uniquement sur d'autres méthodes de prévention telles que les listes blanches, les listes noires ou le filtrage/l'échappement des entrées. Les pirates malveillants peuvent trouver un moyen de contourner une telle désinfection. Avec la disponibilité généralisée des requêtes paramétrées dans les langages de programmation et les frameworks d'applications, il n'y a aucune excuse pour utiliser des méthodes personnalisées. Ces méthodes peuvent être un recours uniquement lorsque les requêtes paramétrées et les procédures stockées ne sont pas disponibles.

Des conseils spécifiques sur l'utilisation des requêtes paramétrées pour se protéger contre des types spécifiques de SQLi sont contenus dans les sections dédiées aux types de SQLi (injection SQL aveugle, injection SQL union, etc.).

#### Comment atténuer les attaques d'injection SQL ?

Les méthodes possibles pour atténuer les attaques SQLi varieront selon le type de logiciel vulnérable :

- Dans le cas de logiciels personnalisés, tels que les applications web, la seule façon de mitiger de manière permanente les SQLi est d'utiliser des requêtes paramétrées, ou des procédures stockées si les requêtes paramétrées ne sont pas disponibles. Vous ne devez utiliser d'autres mesures que si passer aux requêtes paramétrées nécessiterait un changement majeur dans la conception du logiciel, par exemple, si vous deviez migrer une application PHP héritée de l'API MySQL d'origine à PDO ou MySQLi.

- Dans le cas de SQLi connus dans des logiciels tiers, consultez les derniers avis de sécurité pour un correctif et mettez à jour vers une version non vulnérable (généralement la dernière version).
- Dans le cas de SQLi zero-day dans des logiciels tiers, vous ne pouvez compter que sur des règles temporaires de WAF (firewall d'application web) pour la mitigation. Cependant, cela ne fait que rendre les SQLi plus difficiles à exploiter et n'élimine pas la cause profonde du problème.

Considérez des mesures qui aideront à atténuer les dommages si une vulnérabilité SQLi existante est exploitée avec succès :

- **Développeurs :** Utilisez les privilèges les plus bas possibles pour l'accès à la base de données. Chaque fois que vous accédez à la base de données dans votre application, assurez-vous que vous utilisez un compte de base de données ayant les privilèges minimaux nécessaires pour cette opération.
- **Administrateurs de base de données :** Désactivez toutes les options qui entraînent le retour de messages d'erreur de base de données à l'utilisateur, par exemple dans les réponses HTTP. Activez temporairement ces options uniquement lors du débogage de votre code.
- **Administrateurs système :** Assurez-vous que les systèmes d'exploitation sous-jacents de l'application et du serveur de base de données sont sécurisés et à jour. Cela aidera à prévenir les attaques d'élévation de privilèges en cas d'existence d'une vulnérabilité d'injection SQL dans l'application.
