# Python String Manipulation Methods with Exercises

Python provides numerous built-in methods for string manipulation. Here's a comprehensive list with explanations and exercises for each category:

## 1. Case Conversion Methods

### Methods:
- `str.upper()` - converts to uppercase
- `str.lower()` - converts to lowercase
- `str.capitalize()` - first character uppercase, rest lowercase
- `str.title()` - first character of each word uppercase
- `str.swapcase()` - swaps cases of all characters

### Exercises:
```python
text = "python is AWESOME"
# 1. Convert to all uppercase
print(text.upper())  # PYTHON IS AWESOME

# 2. Convert to all lowercase
print(text.lower())  # python is awesome

# 3. Capitalize the first letter
print(text.capitalize())  # Python is awesome

# 4. Title case the string
print(text.title())  # Python Is Awesome

# 5. Swap the cases
print(text.swapcase())  # PYTHON IS awesome
```

## 2. Search and Find Methods

### Methods:
- `str.find(sub)` - returns lowest index of sub or -1
- `str.rfind(sub)` - returns highest index of sub or -1
- `str.index(sub)` - like find but raises ValueError if not found
- `str.rindex(sub)` - like rfind but raises ValueError
- `str.count(sub)` - counts occurrences of sub
- `str.startswith(prefix)` - checks if starts with prefix
- `str.endswith(suffix)` - checks if ends with suffix

### Exercises:
```python
s = "hello world, welcome to python programming"
# 1. Find index of 'world'
print(s.find('world'))  # 6

# 2. Find last occurrence of 'o'
print(s.rfind('o'))  # 34

# 3. Count 'o's in the string
print(s.count('o'))  # 6

# 4. Check if starts with 'hello'
print(s.startswith('hello'))  # True

# 5. Check if ends with 'ing'
print(s.endswith('ing'))  # True
```

## 3. String Validation Methods

### Methods:
- `str.isalpha()` - alphabetic characters only
- `str.isalnum()` - alphanumeric characters only
- `str.isdigit()` - digits only
- `str.isdecimal()` - decimal characters only
- `str.isnumeric()` - numeric characters only
- `str.isspace()` - whitespace only
- `str.islower()` - lowercase characters only
- `str.isupper()` - uppercase characters only
- `str.istitle()` - titlecase string

### Exercises:
```python
# 1. Check if all alphabetic
print("abc".isalpha())  # True
print("abc123".isalpha())  # False

# 2. Check if alphanumeric
print("abc123".isalnum())  # True
print("abc 123".isalnum())  # False

# 3. Check if all digits
print("123".isdigit())  # True
print("12.3".isdigit())  # False

# 4. Check if all lowercase
print("hello".islower())  # True
print("Hello".islower())  # False

# 5. Check if title case
print("This Is Title".istitle())  # True
print("This is not title".istitle())  # False
```

## 4. String Formatting Methods

### Methods:
- `str.strip()` - removes leading/trailing whitespace
- `str.lstrip()` - removes leading whitespace
- `str.rstrip()` - removes trailing whitespace
- `str.center(width)` - centers string in width
- `str.ljust(width)` - left-justifies string in width
- `str.rjust(width)` - right-justifies string in width
- `str.zfill(width)` - pads with zeros on left
- `str.expandtabs(tabsize)` - replaces tabs with spaces

### Exercises:
```python
s = "   hello   "
# 1. Strip whitespace
print(s.strip())  # "hello"

# 2. Center in 20 characters
print(s.center(20))  # "      hello       "

# 3. Left justify in 10 characters
print(s.ljust(10))  # "   hello   "

# 4. Right justify in 15 characters
print(s.rjust(15))  # "         hello   "

# 5. Pad with zeros to make length 8
print("42".zfill(8))  # "00000042"
```

## 5. Splitting and Joining Methods

### Methods:
- `str.split(sep)` - splits at sep (default whitespace)
- `str.rsplit(sep)` - splits from right
- `str.splitlines()` - splits at line breaks
- `str.join(iterable)` - joins strings in iterable with str as separator
- `str.partition(sep)` - splits into (before, sep, after)
- `str.rpartition(sep)` - splits from right

### Exercises:
```python
text = "apple,banana,orange"
# 1. Split by comma
print(text.split(','))  # ['apple', 'banana', 'orange']

# 2. Join with semicolon
print(';'.join(['apple', 'banana', 'orange']))  # "apple;banana;orange"

# 3. Partition at first comma
print(text.partition(','))  # ('apple', ',', 'banana,orange')

# 4. Split lines
multiline = "line1\nline2\r\nline3"
print(multiline.splitlines())  # ['line1', 'line2', 'line3']

# 5. Right split at first comma
print(text.rsplit(',', 1))  # ['apple,banana', 'orange']
```

## 6. Replacement Methods

### Methods:
- `str.replace(old, new)` - replaces all occurrences of old with new
- `str.translate(table)` - translates characters using mapping table
- `str.maketrans(x[, y[, z]])` - creates translation table

### Exercises:
```python
s = "Hello World"
# 1. Replace 'World' with 'Python'
print(s.replace("World", "Python"))  # "Hello Python"

# 2. Remove all 'l's
print(s.replace("l", ""))  # "Heo Word"

# 3. Translate characters
trans = str.maketrans("l", "1")
print(s.translate(trans))  # "He11o Wor1d"

# 4. Multiple replacements
trans = str.maketrans("lW", "1w")
print(s.translate(trans))  # "He11o world"

# 5. Replace first 2 occurrences only
print(s.replace("l", "1", 2))  # "He11o World"
```

## 7. Advanced Exercises

Now let's combine multiple methods in more complex exercises:

```python
# 1. Normalize a messy string
messy = "   ThIs Is A mEsSy sTrInG!   "
clean = messy.strip().lower().capitalize()
print(clean)  # "This is a messy string!"

# 2. Count vowels in a string
text = "Hello, how are you?"
vowels = "aeiou"
count = sum(1 for char in text.lower() if char in vowels)
print(count)  # 7

# 3. Reverse words in a string
sentence = "Python is fun"
reversed_words = ' '.join(sentence.split()[::-1])
print(reversed_words)  # "fun is Python"

# 4. Check if palindrome
word = "Racecar"
is_palindrome = word.lower() == word.lower()[::-1]
print(is_palindrome)  # True

# 5. Create acronym from phrase
phrase = "as soon as possible"
acronym = ''.join(word[0].upper() for word in phrase.split())
print(acronym)  # "ASAP"
```

These exercises cover most of Python's string manipulation capabilities. Practice with these to become proficient in handling strings in Python!
