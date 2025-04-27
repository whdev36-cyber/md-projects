# Python List Manipulation Methods with Exercises

Lists are one of Python's most versatile and commonly used data structures. Here's a comprehensive guide to list manipulation methods along with practical exercises.

## Basic List Methods

### 1. `append()`
Adds an item to the end of the list.

```python
fruits = ['apple', 'banana']
fruits.append('orange')
print(fruits)  # Output: ['apple', 'banana', 'orange']
```

### 2. `extend()`
Adds all elements of an iterable to the end of the list.

```python
fruits = ['apple', 'banana']
fruits.extend(['orange', 'grape'])
print(fruits)  # Output: ['apple', 'banana', 'orange', 'grape']
```

### 3. `insert()`
Inserts an item at a given position.

```python
fruits = ['apple', 'banana']
fruits.insert(1, 'kiwi')
print(fruits)  # Output: ['apple', 'kiwi', 'banana']
```

### 4. `remove()`
Removes the first item from the list that matches the value.

```python
fruits = ['apple', 'banana', 'apple']
fruits.remove('apple')
print(fruits)  # Output: ['banana', 'apple']
```

### 5. `pop()`
Removes and returns the item at the given index (default is last).

```python
fruits = ['apple', 'banana', 'orange']
popped = fruits.pop(1)
print(popped)  # Output: 'banana'
print(fruits)  # Output: ['apple', 'orange']
```

### 6. `clear()`
Removes all items from the list.

```python
fruits = ['apple', 'banana']
fruits.clear()
print(fruits)  # Output: []
```

### 7. `index()`
Returns the index of the first item with the specified value.

```python
fruits = ['apple', 'banana', 'orange']
print(fruits.index('banana'))  # Output: 1
```

### 8. `count()`
Returns the number of times a value appears in the list.

```python
numbers = [1, 2, 3, 1, 2, 1]
print(numbers.count(1))  # Output: 3
```

### 9. `sort()`
Sorts the list in place.

```python
numbers = [3, 1, 4, 2]
numbers.sort()
print(numbers)  # Output: [1, 2, 3, 4]

# With reverse=True
numbers.sort(reverse=True)
print(numbers)  # Output: [4, 3, 2, 1]
```

### 10. `reverse()`
Reverses the elements of the list in place.

```python
fruits = ['apple', 'banana', 'orange']
fruits.reverse()
print(fruits)  # Output: ['orange', 'banana', 'apple']
```

### 11. `copy()`
Returns a shallow copy of the list.

```python
fruits = ['apple', 'banana']
fruits_copy = fruits.copy()
fruits_copy.append('orange')
print(fruits)      # Output: ['apple', 'banana']
print(fruits_copy) # Output: ['apple', 'banana', 'orange']
```

## List Slicing

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

print(numbers[2:5])   # Output: [2, 3, 4] (index 2 to 4)
print(numbers[:3])    # Output: [0, 1, 2] (start to index 2)
print(numbers[5:])    # Output: [5, 6, 7, 8, 9] (index 5 to end)
print(numbers[::2])   # Output: [0, 2, 4, 6, 8] (every 2nd item)
print(numbers[::-1])  # Output: [9, 8, 7, 6, 5, 4, 3, 2, 1, 0] (reverse)
```

## List Comprehensions

A concise way to create lists:

```python
# Squares of numbers 0-9
squares = [x**2 for x in range(10)]
print(squares)  # Output: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

# Even numbers
evens = [x for x in range(20) if x % 2 == 0]
print(evens)  # Output: [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

# Nested list comprehension
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flattened = [num for row in matrix for num in row]
print(flattened)  # Output: [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

## Exercises

### Beginner Exercises

1. **Create and Modify**
   - Create a list of your favorite fruits
   - Add two more fruits to the end
   - Insert one fruit at the beginning
   - Remove the second fruit in the list

2. **Count and Index**
   - Create a list with some duplicate values
   - Count how many times a specific value appears
   - Find the index of the first occurrence of that value

3. **Sorting**
   - Create a list of numbers in random order
   - Sort them in ascending order
   - Then sort them in descending order

### Intermediate Exercises

4. **List Comprehension**
   - Create a list of numbers from 1 to 20
   - Using list comprehension, create a new list with only even numbers
   - Create another list with squares of odd numbers

5. **Nested Lists**
   - Create a 3x3 matrix as a nested list
   - Flatten it to a single list using list comprehension
   - Transpose the matrix (rows become columns)

6. **List Methods**
   - Create two lists
   - Use extend() to combine them
   - Then use append() to add another list (notice the difference)
   - Use pop() to remove items from different positions

### Advanced Exercises

7. **Custom Sorting**
   - Create a list of strings (words of different lengths)
   - Sort them by length
   - Then sort them alphabetically regardless of case

8. **List Manipulation**
   - Create a list with 10 numbers
   - Remove all even numbers
   - Square the remaining numbers
   - Reverse the list

9. **List Operations**
   - Create two lists of numbers
   - Find numbers common to both lists (without using sets)
   - Find numbers in one list but not in the other
   - Combine the lists and remove duplicates

## Solutions

Here are solutions to selected exercises:

### Exercise 4 Solution (List Comprehension)
```python
numbers = list(range(1, 21))
evens = [x for x in numbers if x % 2 == 0]
odd_squares = [x**2 for x in numbers if x % 2 != 0]
```

### Exercise 5 Solution (Nested Lists)
```python
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
flattened = [num for row in matrix for num in row]
transposed = [[row[i] for row in matrix] for i in range(3)]
```

### Exercise 7 Solution (Custom Sorting)
```python
words = ['apple', 'Banana', 'cherry', 'date', 'Elderberry']
words.sort(key=lambda x: len(x))  # Sort by length
words.sort(key=lambda x: x.lower())  # Sort alphabetically case-insensitive
```

Practice these methods and exercises to become proficient with Python list manipulation!
