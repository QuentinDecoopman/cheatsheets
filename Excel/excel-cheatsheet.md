# 📊 Excel Cheatsheet

## ⌨️ Raccourcis Clavier (Windows)

### Navigation
| Raccourci | Action |
|-----------|--------|
| `Ctrl + Début` | Aller à la cellule A1 |
| `Ctrl + Fin` | Aller à la dernière cellule utilisée |
| `Ctrl + ↓ / ↑` | Sauter au début/fin de la colonne |
| `Ctrl + → / ←` | Sauter au début/fin de la ligne |
| `Ctrl + PgSuiv / PgPréc` | Changer de feuille |
| `F5` | Atteindre une cellule spécifique |

### Sélection
| Raccourci | Action |
|-----------|--------|
| `Maj + Espace` | Sélectionner la ligne entière |
| `Ctrl + Espace` | Sélectionner la colonne entière |
| `Ctrl + A` | Tout sélectionner |
| `Ctrl + Maj + Fin` | Étendre la sélection jusqu'à la dernière cellule |
| `Ctrl + Maj + ↓ / →` | Sélectionner jusqu'au bout de la zone remplie |

### Édition
| Raccourci | Action |
|-----------|--------|
| `F2` | Modifier la cellule active |
| `F4` | Basculer référence absolue/relative ($) |
| `Alt + Entrée` | Saut de ligne dans une cellule |
| `Ctrl + D` | Recopier vers le bas |
| `Ctrl + R` | Recopier vers la droite |
| `Ctrl + ;` | Insérer la date du jour |
| `Ctrl + Maj + ;` | Insérer l'heure actuelle |
| `Ctrl + '` | Copier la valeur de la cellule du dessus |
| `Ctrl + Entrée` | Valider sans quitter la cellule |

### Mise en forme
| Raccourci | Action |
|-----------|--------|
| `Ctrl + 1` | Ouvrir Format de cellule |
| `Ctrl + B` | Mettre en gras |
| `Ctrl + I` | Mettre en italique |
| `Ctrl + U` | Souligner |
| `Ctrl + Maj + $` | Format monétaire |
| `Ctrl + Maj + %` | Format pourcentage |
| `Ctrl + Maj + ~` | Format nombre standard |

### Données & Filtres
| Raccourci | Action |
|-----------|--------|
| `Ctrl + Maj + L` | Activer/désactiver les filtres |
| `Alt + ↓` | Ouvrir le menu du filtre automatique |
| `Ctrl + T` | Créer un tableau structuré |
| `Maj + Alt + →` | Grouper lignes/colonnes |
| `Maj + Alt + ←` | Dissocier lignes/colonnes |

### Formules & Calcul
| Raccourci | Action |
|-----------|--------|
| `Alt + =` | Insérer SOMME automatiquement |
| `Maj + F3` | Ouvrir l'assistant fonction |
| `Ctrl + `` ` | Afficher/masquer les formules |
| `F9` | Recalculer toutes les feuilles |
| `Maj + F9` | Recalculer la feuille active |

### Recherche & Collage
| Raccourci | Action |
|-----------|--------|
| `Ctrl + F` | Rechercher |
| `Ctrl + H` | Rechercher et remplacer |
| `Ctrl + C / X / V` | Copier / Couper / Coller |
| `Ctrl + Alt + V` | Collage spécial |
| `Ctrl + Z / Y` | Annuler / Rétablir |

### Fichiers & Impression
| Raccourci | Action |
|-----------|--------|
| `Ctrl + N` | Nouveau classeur |
| `Ctrl + O` | Ouvrir un classeur |
| `Ctrl + S` | Enregistrer |
| `Ctrl + P` | Imprimer |
| `Ctrl + F4` | Fermer le classeur |

---

## 🧮 Formules Essentielles

### Mathématiques de base
```excel
=SOMME(A1:A10)           // Additionner une plage
=MOYENNE(A1:A10)         // Calculer la moyenne
=MIN(A1:A10)             // Valeur minimale
=MAX(A1:A10)             // Valeur maximale
=NB(A1:A10)              // Compter les nombres
=NBVAL(A1:A10)           // Compter les cellules non vides
=ARRONDI(A1; 2)          // Arrondir à 2 décimales
=ENT(A1)                 // Partie entière
=MOD(A1; B1)             // Reste de la division
```

### Texte
```excel
=MAJUSCULE(A1)           // Mettre en majuscules
=MINUSCULE(A1)           // Mettre en minuscules
=NOMPROPRE(A1)           // 1ère lettre en majuscule
=NBCAR(A1)               // Nombre de caractères
=GAUCHE(A1; 5)           // 5 premiers caractères
=DROITE(A1; 5)           // 5 derniers caractères
=STXT(A1; 2; 3)          // Extraire 3 caractères à partir du 2ème
=CHERCHE("texte"; A1)    // Position d'un texte (insensible à la casse)
=TROUVE("texte"; A1)     // Position d'un texte (sensible à la casse)
=REMPLACER(A1; 1; 3; "X")// Remplacer 3 caractères par "X"
=SUBSTITUE(A1; "a"; "b") // Remplacer tous les "a" par "b"
=CONCATENER(A1; " "; B1) // ou =A1 & " " & B1
=TEXTE(A1; "jj/mm/aaaa")// Formater une date
```

### Dates & Heures
```excel
=AUJOURDHUI()            // Date du jour
=MAINTENANT()            // Date et heure actuelles
=JOUR(A1)                // Extraire le jour
=MOIS(A1)                // Extraire le mois
=ANNEE(A1)               // Extraire l'année
=JOURSEM(A1)             // Jour de la semaine (1=dimanche)
=NO.SEMAINE(A1)          // Numéro de la semaine
=DATEDIF(A1; B1; "d")    // Différence en jours
=DATEDIF(A1; B1; "m")    // Différence en mois
=DATEDIF(A1; B1; "y")    // Différence en années
=SERIE.JOUR.OUVRE(A1; 10)// Date + 10 jours ouvrés
```

### Logique & Conditions
```excel
=SI(A1>10; "Oui"; "Non")                 // Condition simple
=SI(A1>10; SI(A1<20; "Moyen"; "Grand"); "Petit")  // SI imbriqué
=ET(A1>10; B1<20)                        // ET logique
=OU(A1>10; B1<20)                        // OU logique
=NON(A1>10)                              // NON logique
=SI.ENS(A1:A10; B1:B10; ">10"; C1:C10; "<20")  // SI avec plusieurs conditions
=SOMME.SI(A1:A10; ">10"; B1:B10)         // SOMME conditionnelle
=SOMME.SI.ENS(C1:C10; A1:A10; ">10"; B1:B10; "<20")  // SOMME multi-conditions
=NB.SI(A1:A10; ">10")                    // NB conditionnel
=NB.SI.ENS(A1:A10; ">10"; B1:B10; "<20") // NB multi-conditions
=MOYENNE.SI(A1:A10; ">10"; B1:B10)       // MOYENNE conditionnelle
```

### Recherche & Référence
```excel
=RECHERCHEV(valeur; table; colonne; FAUX)  // Recherche verticale (obsolète)
=RECHERCHEH(valeur; table; ligne; FAUX)    // Recherche horizontale
=INDEX(A1:C10; 3; 2)                       // Valeur à la ligne 3, colonne 2
=EQUIV(valeur; A1:A10; 0)                  // Position d'une valeur
=INDEX(A1:A10; EQUIV("valeur"; A1:A10; 0)) // RECHERCHEV moderne
=XLOOKUP(valeur; A1:A10; B1:B10)           // Recherche moderne (Excel 365)
=DECALER(A1; 3; 2)                         // Décaler de 3 lignes, 2 colonnes
=LIGNE(A1)                                 // Numéro de ligne
=COLONNE(A1)                               // Numéro de colonne
```

### Statistiques
```excel
=ECARTYPE(A1:A10)          // Écart-type (échantillon)
=ECARTYPE.P(A1:A10)        // Écart-type (population)
=VAR(A1:A10)               // Variance (échantillon)
=VAR.P(A1:A10)             // Variance (population)
=RANG(A1; A1:A10; 0)       // Rang d'une valeur
=MEDIANE(A1:A10)           // Médiane
=MODE(A1:A10)              // Mode (valeur la plus fréquente)
=GRANDE.VALEUR(A1:A10; 2)  // 2ème plus grande valeur
=PETITE.VALEUR(A1:A10; 2)  // 2ème plus petite valeur
```

### Financier
```excel
=VPM(taux; npm; va)        // Paiement d'un emprunt
=TAUX(npm; vpm; va)        // Taux d'intérêt
=NPM(taux; vpm; va)        // Nombre de périodes
=VA(taux; npm; vpm)        // Valeur actuelle
=VC(taux; npm; vpm)        // Valeur capitalisée
=TRI(flux)                 // Taux de rendement interne
=VAN(taux; flux)           // Valeur actuelle nette
```

---

## 📋 Astuces Pratiques

### Références de cellules
- **Relative** : `A1` (change quand on copie)
- **Absolue** : `$A$1` (ne change jamais)
- **Mixte** : `$A1` ou `A$1` (fixe colonne ou ligne)

### Validation des données
1. Sélectionner les cellules
2. `Données` → `Validation des données`
3. Choisir : Liste, Nombre, Date, etc.
4. Pour une liste déroulante : `Liste` + Source = `=$A$1:$A$10`

### Mise en forme conditionnelle
1. Sélectionner les cellules
2. `Accueil` → `Mise en forme conditionnelle`
3. Règles : Barres de données, Échelles de couleurs, Icônes
4. Formule personnalisée : `=A1>MOYENNE($A$1:$A$10)`

### Tableaux structurés
- `Ctrl + T` pour créer un tableau
- Avantages : filtres automatiques, formules qui s'étendent, style professionnel
- Références : `=SOMME(Tableau1[Colonne1])`

### Protéger une feuille
1. `Révision` → `Protéger la feuille`
2. Choisir un mot de passe
3. Sélectionner les actions autorisées

---

## 🎯 Top 10 des formules les plus utiles

1. **SOMME** : `=SOMME(A1:A10)`
2. **SI** : `=SI(A1>10; "Oui"; "Non")`
3. **RECHERCHEV / XLOOKUP** : `=XLOOKUP(valeur; A:A; B:B)`
4. **SOMME.SI.ENS** : `=SOMME.SI.ENS(C:C; A:A; "critère1"; B:B; "critère2")`
5. **DATE** : `=DATE(2026; 12; 31)`
6. **CONCATENER / &** : `=A1 & " " & B1`
7. **GAUCHE / DROITE** : `=GAUCHE(A1; 5)`
8. **ARRONDI** : `=ARRONDI(A1; 2)`
9. **NB.SI** : `=NB.SI(A:A; ">10")`
10. **TEXTE** : `=TEXTE(A1; "jj/mm/aaaa")`

---

## 🔧 Dépannage rapide

| Erreur | Signification | Solution |
|--------|---------------|----------|
| `#DIV/0!` | Division par zéro | Vérifier le dénominateur |
| `#N/A` | Valeur non disponible | Vérifier RECHERCHEV / données |
| `#REF!` | Référence invalide | Cellule supprimée ou déplacée |
| `#VALUE!` | Mauvais type d'argument | Nombre au lieu de texte ou inversement |
| `#NAME?` | Nom de fonction inconnu | Faute de frappe dans la formule |
| `#NUM!` | Problème numérique | Nombre trop grand ou calcul impossible |
| `#####` | Colonne trop étroite | Élargir la colonne |
