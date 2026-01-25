# Conventions & Bonnes Pratiques Python

## 📋 Conventions de Nommage (PEP 8)

### Variables et Fonctions

- **snake_case** pour les variables et fonctions

```python
user_name = "John"
def get_user_data():
    pass
```

### Constantes

- **UPPER_SNAKE_CASE** pour les constantes

```python
MAX_RETRY_COUNT = 3
API_BASE_URL = "https://api.example.com"
PI = 3.14159
```

### Classes

- **PascalCase** pour les classes

```python
class UserProfile:
    def __init__(self, name):
        self.name = name
```

### Méthodes privées et variables

- Préfixer avec `_` (convention) ou `__` (name mangling)

```python
class User:
    def __init__(self):
        self._private_var = "private"
        self.__very_private = "very private"

    def _private_method(self):
        pass
```

### Modules et packages

- **snake_case** pour les noms de fichiers

```
user_service.py
data_processor.py
```

---

## 🏗️ Structure et Organisation

### Ordre des imports (PEP 8)

```python
# 1. Imports de la bibliothèque standard
import os
import sys
from typing import List, Dict

# 2. Imports de bibliothèques tierces
import numpy as np
import pandas as pd
from flask import Flask, request

# 3. Imports locaux
from .models import User
from .utils import format_date
```

### Structure d'un module

```python
"""
Module docstring: description du module
"""

# Imports
import os

# Constantes
MAX_SIZE = 100

# Classes
class MyClass:
    pass

# Fonctions
def my_function():
    pass

# Point d'entrée
if __name__ == "__main__":
    main()
```

---

## ✅ Bonnes Pratiques Générales

### 1. Pythonic Code

```python
# ❌ Non pythonic
i = 0
while i < len(items):
    print(items[i])
    i += 1

# ✅ Pythonic
for item in items:
    print(item)

# ✅ Avec index si nécessaire
for i, item in enumerate(items):
    print(f"{i}: {item}")
```

### 2. List Comprehensions

```python
# ✅ Bon
squares = [x**2 for x in range(10)]
evens = [x for x in range(10) if x % 2 == 0]

# ❌ Trop complexe, utiliser une boucle normale
result = [
    x for sublist in matrix
    for x in sublist
    if x > 0 and x < 100
]
```

### 3. Context Managers

```python
# ✅ Toujours utiliser with pour les fichiers
with open('file.txt', 'r') as f:
    content = f.read()

# Custom context manager
from contextlib import contextmanager

@contextmanager
def database_connection():
    conn = create_connection()
    try:
        yield conn
    finally:
        conn.close()
```

### 4. Unpacking et Multiple Assignment

```python
# ✅ Bon
a, b = 1, 2
first, *rest = [1, 2, 3, 4]  # first=1, rest=[2,3,4]

# Swap
a, b = b, a
```

### 5. Default Arguments (attention aux mutables)

```python
# ❌ Dangereux
def add_item(item, items=[]):
    items.append(item)
    return items

# ✅ Bon
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

---

## 🎯 Fonctions

### Docstrings (PEP 257)

```python
def calculate_total(price: float, tax_rate: float) -> float:
    """
    Calcule le prix total avec taxes.

    Args:
        price: Prix de base
        tax_rate: Taux de taxe (ex: 0.2 pour 20%)

    Returns:
        Prix total avec taxes

    Raises:
        ValueError: Si le prix est négatif

    Example:
        >>> calculate_total(100, 0.2)
        120.0
    """
    if price < 0:
        raise ValueError("Price cannot be negative")
    return price * (1 + tax_rate)
```

### Type Hints (PEP 484)

```python
from typing import List, Dict, Optional, Union, Callable

def process_users(
    users: List[Dict[str, str]],
    filter_func: Optional[Callable[[Dict], bool]] = None
) -> List[str]:
    """Process users and return names."""
    if filter_func:
        users = [u for u in users if filter_func(u)]
    return [u['name'] for u in users]
```

### Args et Kwargs

```python
# *args pour arguments positionnels variables
def sum_all(*args: int) -> int:
    return sum(args)

# **kwargs pour arguments nommés variables
def create_user(**kwargs) -> dict:
    return {
        'name': kwargs.get('name', 'Unknown'),
        'email': kwargs.get('email'),
        'role': kwargs.get('role', 'user')
    }

# Combinaison
def complex_function(required, *args, key=None, **kwargs):
    pass
```

### Keyword-only Arguments

```python
# Force l'utilisation de noms pour certains arguments
def create_user(name, *, email, role='user'):
    return {'name': name, 'email': email, 'role': role}

# Doit utiliser: create_user('John', email='john@example.com')
# Erreur: create_user('John', 'john@example.com')
```

---

## 🏛️ Classes et POO

### Dataclasses (Python 3.7+)

```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class User:
    name: str
    email: str
    age: int = 0
    roles: List[str] = field(default_factory=list)

    def __post_init__(self):
        if self.age < 0:
            raise ValueError("Age cannot be negative")
```

### Properties

```python
class Temperature:
    def __init__(self, celsius):
        self._celsius = celsius

    @property
    def celsius(self):
        return self._celsius

    @celsius.setter
    def celsius(self, value):
        if value < -273.15:
            raise ValueError("Temperature below absolute zero")
        self._celsius = value

    @property
    def fahrenheit(self):
        return self._celsius * 9/5 + 32
```

### Magic Methods (Dunder Methods)

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Point({self.x}, {self.y})"

    def __str__(self):
        return f"({self.x}, {self.y})"

    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

    def __add__(self, other):
        return Point(self.x + other.x, self.y + other.y)

    def __len__(self):
        return 2

    def __getitem__(self, index):
        return [self.x, self.y][index]
```

### Héritage et Super

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        pass

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)
        self.breed = breed

    def speak(self):
        return f"{self.name} says Woof!"
```

---

## 🔄 Itérateurs et Générateurs

### Générateurs

```python
# ✅ Économe en mémoire
def fibonacci(n):
    a, b = 0, 1
    for _ in range(n):
        yield a
        a, b = b, a + b

# Utilisation
for num in fibonacci(10):
    print(num)

# Generator expression
squares = (x**2 for x in range(1000000))  # Paresseux
```

### Itérateurs personnalisés

```python
class Countdown:
    def __init__(self, start):
        self.current = start

    def __iter__(self):
        return self

    def __next__(self):
        if self.current <= 0:
            raise StopIteration
        self.current -= 1
        return self.current + 1
```

---

## 🛡️ Gestion des Erreurs

### Exceptions spécifiques

```python
# ✅ Bon - spécifique
try:
    with open('file.txt') as f:
        data = json.load(f)
except FileNotFoundError:
    print("File not found")
except json.JSONDecodeError:
    print("Invalid JSON")

# ❌ Mauvais - trop général
try:
    risky_operation()
except Exception:
    pass
```

### Exceptions personnalisées

```python
class ValidationError(Exception):
    """Raised when validation fails."""
    pass

class EmailValidationError(ValidationError):
    """Raised when email validation fails."""

    def __init__(self, email, message="Invalid email"):
        self.email = email
        self.message = message
        super().__init__(f"{message}: {email}")
```

### EAFP vs LBYL

```python
# EAFP (Easier to Ask Forgiveness than Permission) - Pythonic
try:
    value = data['key']
except KeyError:
    value = default

# LBYL (Look Before You Leap) - Moins pythonic
if 'key' in data:
    value = data['key']
else:
    value = default
```

---

## 📦 Modules et Packages

### Structure d'un package

```
mypackage/
├── __init__.py
├── module1.py
├── module2.py
└── subpackage/
    ├── __init__.py
    └── module3.py
```

### **init**.py

```python
# mypackage/__init__.py
from .module1 import function1
from .module2 import Class2

__all__ = ['function1', 'Class2']
__version__ = '1.0.0'
```

### Imports relatifs

```python
# Dans mypackage/subpackage/module3.py
from ..module1 import function1  # Parent package
from .module4 import function4   # Même package
```

---

## 🚀 Performance

### 1. Utiliser les bonnes structures de données

```python
# ✅ Set pour les recherches
items = set([1, 2, 3, 4, 5])
if 3 in items:  # O(1)
    pass

# ❌ List pour les recherches répétées
items = [1, 2, 3, 4, 5]
if 3 in items:  # O(n)
    pass
```

### 2. Collections module

```python
from collections import defaultdict, Counter, deque, namedtuple

# defaultdict
word_count = defaultdict(int)
word_count['word'] += 1

# Counter
counts = Counter(['a', 'b', 'a', 'c', 'a'])
# Counter({'a': 3, 'b': 1, 'c': 1})

# deque pour les queues
queue = deque([1, 2, 3])
queue.append(4)
queue.popleft()

# namedtuple
Point = namedtuple('Point', ['x', 'y'])
p = Point(1, 2)
```

### 3. Éviter les copies inutiles

```python
# ✅ Générateur pour les grandes séquences
def process_large_file(filename):
    with open(filename) as f:
        for line in f:  # Ne charge pas tout en mémoire
            yield process_line(line)

# ❌ Charge tout en mémoire
def process_large_file(filename):
    with open(filename) as f:
        lines = f.readlines()  # Mauvais pour gros fichiers
        return [process_line(line) for line in lines]
```

---

## 🎨 Patterns Python

### Singleton

```python
class Singleton:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
```

### Decorator Pattern

```python
from functools import wraps
import time

def timing_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.2f}s")
        return result
    return wrapper

@timing_decorator
def slow_function():
    time.sleep(1)
```

### Factory Pattern

```python
class ShapeFactory:
    @staticmethod
    def create_shape(shape_type):
        if shape_type == "circle":
            return Circle()
        elif shape_type == "square":
            return Square()
        else:
            raise ValueError(f"Unknown shape: {shape_type}")
```

---

## 🔍 Outils et Linting

### Outils recommandés

- **black**: Formatage automatique
- **flake8**: Linting
- **mypy**: Vérification de types
- **pylint**: Analyse de code
- **isort**: Tri des imports

### Configuration (pyproject.toml)

```toml
[tool.black]
line-length = 88
target-version = ['py39']

[tool.isort]
profile = "black"
line_length = 88

[tool.mypy]
python_version = "3.9"
strict = true
warn_return_any = true
warn_unused_configs = true
```

---

## 🧪 Tests

### Unittest

```python
import unittest

class TestCalculator(unittest.TestCase):
    def setUp(self):
        self.calc = Calculator()

    def test_add(self):
        self.assertEqual(self.calc.add(2, 3), 5)

    def test_divide_by_zero(self):
        with self.assertRaises(ZeroDivisionError):
            self.calc.divide(1, 0)

    def tearDown(self):
        pass

if __name__ == '__main__':
    unittest.main()
```

### Pytest (recommandé)

```python
import pytest

def test_add():
    assert add(2, 3) == 5

def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError):
        divide(1, 0)

@pytest.fixture
def user():
    return User("John", "john@example.com")

def test_user_name(user):
    assert user.name == "John"
```

---

## 🔒 Sécurité

### 1. Éviter eval et exec

```python
# ❌ Dangereux
eval(user_input)

# ✅ Utiliser des alternatives
import ast
ast.literal_eval("[1, 2, 3]")  # Sûr pour les littéraux
```

### 2. Secrets et configuration

```python
# ✅ Bon
import os
from dotenv import load_dotenv

load_dotenv()
API_KEY = os.getenv('API_KEY')

# ❌ Mauvais
API_KEY = "hardcoded_key_123"
```

### 3. Input validation

```python
def process_age(age_str: str) -> int:
    try:
        age = int(age_str)
        if not 0 <= age <= 150:
            raise ValueError("Age out of range")
        return age
    except ValueError as e:
        raise ValueError(f"Invalid age: {e}")
```

---

## 📝 Formatage (PEP 8)

### Longueur de ligne

- Maximum 79 caractères (code)
- Maximum 72 caractères (docstrings/commentaires)

### Espacement

```python
# ✅ Bon
def function(arg1, arg2):
    return arg1 + arg2

x = 1
y = 2
long_variable = 3

# Opérateurs
result = (a + b) * (c - d)

# ❌ Mauvais
def function (arg1,arg2):
    return arg1+arg2
```

### Imports

```python
# ✅ Bon - un par ligne
import os
import sys

from typing import List, Dict

# ❌ Mauvais
import os, sys
```

---

## 📚 Ressources

- [PEP 8 - Style Guide](https://pep8.org/)
- [PEP 20 - The Zen of Python](https://www.python.org/dev/peps/pep-0020/)
- [Python Documentation](https://docs.python.org/)
- [Real Python](https://realpython.com/)
- [Effective Python](https://effectivepython.com/)
