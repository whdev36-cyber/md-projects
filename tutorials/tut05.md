# Python Dictionary Manipulation Methods with Exercises

Dictionaries are one of Python's most powerful and versatile data structures. Here's a comprehensive guide to dictionary manipulation methods along with practical exercises.

## Basic Dictionary Methods

### 1. Creating Dictionaries
```python
# Empty dictionary
empty_dict = {}

# Dictionary with initial values
person = {'name': 'Alice', 'age': 25, 'city': 'New York'}

# Using dict() constructor
person = dict(name='Alice', age=25, city='New York')
```

### 2. Accessing Elements
```python
# Access by key
name = person['name']

# Using get() - returns None if key doesn't exist
age = person.get('age')
occupation = person.get('occupation')  # Returns None
occupation = person.get('occupation', 'unemployed')  # Returns 'unemployed' if key doesn't exist
```

### 3. Adding/Updating Elements
```python
# Add new key-value pair
person['occupation'] = 'Engineer'

# Update existing key
person['age'] = 26

# Update multiple items with update()
person.update({'age': 27, 'email': 'alice@example.com'})
```

### 4. Removing Elements
```python
# Remove key and return its value
age = person.pop('age')

# Remove and return last inserted item (Python 3.7+)
last_item = person.popitem()

# Delete a key
del person['city']

# Clear all items
person.clear()
```

## Advanced Dictionary Methods

### 5. Dictionary Views
```python
# Get all keys
keys = person.keys()  # Returns a view object

# Get all values
values = person.values()

# Get all key-value pairs
items = person.items()
```

### 6. Dictionary Comprehensions
```python
# Create dictionary from list
numbers = [1, 2, 3]
squares = {x: x**2 for x in numbers}  # {1: 1, 2: 4, 3: 9}

# Filter dictionary
original = {'a': 1, 'b': 2, 'c': 3}
filtered = {k: v for k, v in original.items() if v > 1}
```

### 7. Merging Dictionaries (Python 3.5+)
```python
dict1 = {'a': 1, 'b': 2}
dict2 = {'b': 3, 'c': 4}

# Method 1
merged = {**dict1, **dict2}  # {'a': 1, 'b': 3, 'c': 4}

# Method 2 (Python 3.9+)
merged = dict1 | dict2
```

### 8. Default Dictionaries
```python
from collections import defaultdict

# Default value for missing keys
word_counts = defaultdict(int)
for word in ['apple', 'banana', 'apple']:
    word_counts[word] += 1
```

## Dictionary Exercises

### Exercise 1: Word Frequency Counter
Write a function that takes a string and returns a dictionary with words as keys and their counts as values.

```python
def word_frequency(text):
    words = text.split()
    frequency = {}
    for word in words:
        frequency[word] = frequency.get(word, 0) + 1
    return frequency

# Test
text = "apple banana orange apple banana apple"
print(word_frequency(text))
# Output: {'apple': 3, 'banana': 2, 'orange': 1}
```

### Exercise 2: Dictionary Inverter
Create a function that inverts a dictionary (keys become values and values become keys). Handle duplicate values by storing keys in a list.

```python
def invert_dict(original):
    inverted = {}
    for key, value in original.items():
        if value in inverted:
            inverted[value].append(key)
        else:
            inverted[value] = [key]
    return inverted

# Test
original = {'a': 1, 'b': 2, 'c': 1}
print(invert_dict(original))
# Output: {1: ['a', 'c'], 2: ['b']}
```

### Exercise 3: Dictionary Merger
Write a function that merges two dictionaries. If a key exists in both, sum their values.

```python
def merge_dicts(dict1, dict2):
    merged = dict1.copy()
    for key, value in dict2.items():
        if key in merged:
            merged[key] += value
        else:
            merged[key] = value
    return merged

# Test
dict1 = {'a': 1, 'b': 2}
dict2 = {'b': 3, 'c': 4}
print(merge_dicts(dict1, dict2))
# Output: {'a': 1, 'b': 5, 'c': 4}
```

### Exercise 4: Nested Dictionary Updater
Create a function that updates a nested dictionary with another dictionary at a specified path.

```python
def update_nested_dict(d, keys, value_dict):
    for key in keys[:-1]:
        d = d.setdefault(key, {})
    d[keys[-1]] = value_dict
    return d

# Test
original = {'a': {'b': {'c': 1}}}
update_nested_dict(original, ['a', 'b', 'd'], {'e': 2})
print(original)
# Output: {'a': {'b': {'c': 1, 'd': {'e': 2}}}}
```

### Exercise 5: Dictionary Difference
Write a function that returns the difference between two dictionaries (keys and values that differ).

```python
def dict_difference(dict1, dict2):
    diff = {}
    all_keys = set(dict1.keys()) | set(dict2.keys())
    
    for key in all_keys:
        val1 = dict1.get(key)
        val2 = dict2.get(key)
        if val1 != val2:
            diff[key] = (val1, val2)
    
    return diff

# Test
dict1 = {'a': 1, 'b': 2, 'c': 3}
dict2 = {'a': 1, 'b': 4, 'd': 5}
print(dict_difference(dict1, dict2))
# Output: {'b': (2, 4), 'c': (3, None), 'd': (None, 5)}
```

These methods and exercises should give you a solid foundation for working with Python dictionaries. Practice them to become proficient in dictionary manipulation!
