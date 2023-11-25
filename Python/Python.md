# Cheatsheet Python

- [Index](/Readme.md)
- [Docs](https://docs.python.org/3/)

## Variables et Types de Données :

### Variables

```python
x = 5
y = "Hello"
z = 3.14
```

### Types de données

```python
entier = 10
chaine_de_caracteres = "Python"
flottant = 3.14
booleen = True
liste = [1, 2, 3]
dictionnaire = {"cle": "valeur"}
```

## Opérateurs :

### Opérateurs mathématiques

```python
a = 5 + 3
b = 10 - 2
c = 2 * 4
d = 16 / 2
e = 2 ** 3  # Puissance
```

### Opérateurs de comparaison

```python
x = 5
y = 10
resultat = x == y  # False
```

### Opérateurs logiques

```python
condition1 = True
condition2 = False
resultat_et = condition1 and condition2  # False
resultat_ou = condition1 or condition2   # True
resultat_non = not condition1            # False
```

## Structures de Contrôle :

### Condition if-else

```python
if x > 0:
    print("Positif")
else:
    print("Négatif")
```

### Boucle for

```python
for i in range(5):
    print(i)
```

### Boucle while

```python
count = 0
while count < 5:
    print(count)
    count += 1
```

## Fonctions :

### Déclaration de fonction

```python
def ma_fonction(parametre1, parametre2):
    resultat = parametre1 + parametre2
    return resultat
```

### Appel de fonction

```python
resultat = ma_fonction(3, 7)
print(resultat)
```

## Listes :

### Création d'une liste

```python
ma_liste = [1, 2, 3, 4, 5]
```

### Accéder aux éléments

```python
premier_element = ma_liste[0]
```

### Ajouter un élément

```python
ma_liste.append(6)
```

### Longueur de la liste

```python
longueur = len(ma_liste)
```

## Dictionnaires :

### Création d'un dictionnaire

```python
mon_dictionnaire = {"nom": "John", "age": 30, "ville": "Paris"}
```

### Accéder à une valeur

```
nom = mon_dictionnaire["nom"]
```

### Ajouter une paire clé-valeur

```python
mon_dictionnaire["profession"] = "Développeur"
```

## Entrée/Sortie (E/S) :

### Affichage

```python
print("Hello, World!")
```

### Entrée utilisateur

```python
nom_utilisateur = input("Entrez votre nom : ")
```

### Fichier : Lecture

```python
with open("mon_fichier.txt", "r") as fichier:
    contenu = fichier.read()
```

### Fichier : Écriture

```python
with open("mon_fichier.txt", "w") as fichier:
    fichier.write("Bonjour, Python!")
```

## Manipulation de Chaînes :

### Concaténation

```python
chaine1 = "Hello"
chaine2 = "World"
resultat = chaine1 + " " + chaine2
```

### Formatage

```python
prenom = "John"
age = 25
message = "Je m'appelle {} et j'ai {} ans.".format(prenom, age)
```

## Gestion d'Exceptions :

```python
try:

    resultat = x / 0
except ZeroDivisionError:
    print("Division par zéro!")
except Exception as e:
    print(f"Une erreur s'est produite : {e}")
finally:
    print("Cette partie sera exécutée quoi qu'il arrive.")
```
