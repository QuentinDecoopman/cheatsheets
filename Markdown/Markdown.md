# Cheatsheet Markdown

- [Index](/Readme.md)
- [Doc](https://www.markdownguide.org/)

## Titres

```
# Titre 1
## Titre 2
### Titre 3
#### Titre 4
##### Titre 5
###### Titre 6
```

## Texte en gras et en italique

```
**Texte en gras**
*Texte en italique*
```

## Listes

### Liste non ordonnée

```
- Élément 1
- Élément 2
  - Sous-élément 2.1
  - Sous-élément 2.2
```

### Liste ordonnée

```
1. Premier élément
2. Deuxième élément
   1. Sous-élément 2.1
   2. Sous-élément 2.2
```

## Liens

```
[Texte du lien](URL)
```

## Images

```
![Texte alternatif](URL_de_l_image)
```

## Citations

```
> Ceci est une citation.
```

## Code

```
Bloc de code
```langage
Code ici
```

Syntaxe colorée

```
```javascript
const exemple = 'Syntaxe colorée';
console.log(exemple);
```

## Lignes horizontales

```
---
```

## Tableaux

```
| En-tête 1 | En-tête 2 |
| --------- | --------- |
| Cellule 1 | Cellule 2 |
| Cellule 3 | Cellule 4 |
```

## Échappement de caractères

Pour échapper un caractère spécial, utilisez le caractère \ avant le caractère.

```
\*Ceci ne sera pas en italique\*
```

## Ancres (liens internes)

```
[Aller à la section suivante](#section-suivante)
```

## Notes de bas de page

```
Ceci est un exemple de note de bas de page[^1].

[^1]: Ceci est le texte de la note de bas de page.

```

## En-têtes personnalisées

```
::: danger
Ceci est un avertissement !
:::
