# PHP

- [Index](/Readme.md)
- [Docs](https://www.php.net/docs.php)

## Structure d'une page PHP

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ma Page PHP</title>
    <!-- Ajoutez ici vos balises meta, liens vers des feuilles de style, etc. -->
</head>
<body>

    <?php
        // Contenu PHP de la page
        $message = "Bienvenue sur ma page PHP!";
        echo "<h1>$message</h1>";
    ?>

    <!-- Contenu HTML de la page -->
    <p>Ceci est un paragraphe HTML.</p>

    <?php
        // Autre contenu PHP
        $variable = 42;
        echo "La réponse ultime est $variable.";
    ?>

    <!-- Ajoutez ici d'autres éléments HTML, scripts, etc. -->

</body>
</html>

```

## Syntaxe de base :

```php
<?php
    // Votre code PHP ici
?>
```

## Variables :

```php
<?php
    $variable = "Valeur";
    $nombre = 10;
?>
```

## Constante :

```phpà
define("CONSTANTE", "Valeur");
```

````

## Types de données :

```php
    Chaînes de caractères : $string = "Hello";
    Entiers : $integer = 42;
    Flottants : $float = 3.14;
    Booléens : $bool = true;
````

## Opérateurs :

```php
    Addition : $sum = $a + $b;
    Soustraction : $difference = $a - $b;
    Multiplication : $product = $a * $b;
    Division : $quotient = $a / $b;
```

## Structures de contrôle :

### Conditions :

```php
<?php
    if ($condition) {
        // Code à exécuter si la condition est vraie
    } elseif ($autre_condition) {
        // Code à exécuter si une autre condition est vraie
    } else {
        // Code à exécuter si aucune condition n'est vraie
    }
?>
```

### Boucle for :

```php
<?php
    for ($i = 0; $i < 5; $i++) {
        // Code à répéter
    }
?>

```

### Boucle while :

```php
<?php
    $i = 0;
    while ($i < 5) {
        // Code à répéter
        $i++;
    }
?>
```

## Tableaux :

```php
<?php
    // Tableau indexé
    $fruits = array("Pomme", "Banane", "Orange");

    // Tableau associatif
    $personne = array("nom" => "Doe", "prénom" => "John", "âge" => 30);
?>
```

## Fonctions :

```php
<?php
    function maFonction($parametre1, $parametre2) {
        // Code de la fonction
        return $resultat;
    }

    // Appel de la fonction
    $output = maFonction($valeur1, $valeur2);
?>
```

## Manipulation de chaînes :

```php
<?php
    $str = "Hello, World!";
    echo strlen($str);         // Longueur de la chaîne
    echo strpos($str, "World"); // Position du mot "World"
    echo substr($str, 0, 5);    // Sous-chaîne de la position 0 à la position 5
    echo strtoupper($str);      // Convertir en majuscules
    echo strtolower($str);      // Convertir en minuscules
?>
```

## Inclusion de fichiers :

```php
<?php
    include 'monFichier.php';
    require 'autreFichier.php';
?>
```
