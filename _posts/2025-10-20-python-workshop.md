---
title: 'Python Tutorial'
tags: ["Tutorials","workshops", "python","Shellmates"]
categories: ["Tutorials","Python"]
image:
   path: https://github.com/0xy4c1n3/python-workshop-materials/blob/main/python.jpg?raw=true
---

----------

## **NOTE**
> **I wrote this article to serve as a resource for [Shellmates club](https://linktr.ee/shellmates)
members who want to learn Python. You’re free to share it and use it however you like, in fact, I encourage you to share it with anyone interested in learning.**
{: .prompt-info }

---
## **Introduction**
Python is the most important language to learn in cybersecurity. It’s simple, powerful, and can be used for automating tasks or building tools to make your life easier. Learning Python will give you a huge advantage as you start exploring the world of cybersecurity.

### **Python Overview**
Python is a high-level, general-purpose programming language known for its clean, readable syntax that feels close to plain English, which makes it one of the easiest languages to learn. Python handles most of the complex details behind the scenes, allowing you to focus on solving problems instead of worrying about how the computer works internally.

### **Why we use python**

1. **Easy yet powerful**
Python is simple enough for beginners to pick up quickly, yet powerful enough to build advanced tools, applications, and even AI systems. You can write useful programs with just a few lines of code, which makes learning and experimenting fun and rewarding.

2. **Can be used for almost everything**
Whether you’re automating tasks, analyzing data, building web apps, or writing cybersecurity scripts, Python has you covered. Its versatility makes it an essential tool for developers, researchers, and security professionals alike. Even if you wanna do a silly thing as such as downloading someone's instagram pfp, you can do it with python.

3. **A large and active community**
Python has one of the biggest and most supportive communities in the programming world. You’ll find endless tutorials and documentations to learn from. If you ever get stuck, chances are someone has already solved the same problem and shared the solution online. From my own experience, I never had a question in python and couldn't find an answer to online.

4. **There’s existing libraries for Everything**
One of the best things about Python is that whatever you want to do, there’s probably already a library for it. Need to analyze network traffic, automate a task, scrape data, or encrypt files? Someone has likely built a library that does exactly that, and shared it for free.

### **Advices to Get Better and Learn Fast**

1. **Find the Motivation:**
Before diving into Python, take a moment to understand **why** you want to learn it. Whether it’s to automate tasks, build security tools, or just improve your coding skills, having a clear goal keeps you consistent. As long as you’re a student in computer science, there will always be a reason and motivation to learn Python.

2. **Don’t Use LLMs While Learning:**
Large Language Models like ChatGPT can be incredibly helpful, but you shouldn't use them at all during your learning process. When you’re still a beginner, relying on them too much can stop you from thinking critically or solving problems on your own. Use them only once you’re comfortable with the basics, for debugging, explanations, or improving your code. For reference, before chatGPT came, people still mastered almost all tools we use nowdays, what's stopping you from doing so as well?

3. **Ask for Help:**
You’re not alone in your learning journey. The entire Shellmates technical team is there to support you. Whenever you get stuck or feel lost, don’t hesitate to ask questions. Everyone started somewhere, and asking for help is a strength, not a weakness.

4. **Give It Your Energy and Time:**
Nothing worth learning comes easily. Python doesn’t demand daily grinding, but it still does require steady effort and curiosity. Spend some time experimenting, solving small challenges, and building fun projects. That’s how real progress happens. Dedication always pays off.

5. **Seek developing the right mindest not mastering the language:**
Learning Python isn’t just about memorizing syntax or functions. It’s about developing the right mindset that values problem-solving and logical thinking. Once you understand how to think like a programmer, learning any language becomes much easier. Python is just the tool, the real power is how you think and get the most out of any input you have.

---

## **Your first python script**
Now that you understand why Python is such a valuable tool, it’s time to write your very first Python script.
If you are wondering what a script is, it’s simply a file that contains code written in a programming language, in this case Python, that tells the computer what to do step by step. You can think of it as a set of instructions the computer follows to perform a specific task.
Don’t worry if you’ve never coded before, Python is designed to be simple and readable, which makes it perfect for beginners.

1. **Open your code editor or terminal.**
You can use any text editor like VS Code, Sublime Text, or even the default Notepad. If you prefer working in the terminal, Python can run scripts directly from there.

2. **Create a new file named `hello.py`.**
This will be your first Python file. The `.py` extension tells the computer that it contains Python code. Naming the file `hello` doesn't mean we won't be able to run the python script, but it is better to add the `.py` at the end.

3. **Write this simple line inside the file:**
```python
print("Hello, Shellmates!")
```

4. **Run your script.**
Open a terminal in the same folder as your file and type:`python3 hello.py` You should see the message: `Hello, Shellmates!`


And that’s it! You’ve just written and executed your first Python program. It might look simple, but it is just to show you how to run a python script.

> **There are websites that let you run python code online without having python installed on your laptop, all they require is internet such as this one: [Online Python](https://www.onlinegdb.com/online_python_compiler), they will be enough for this tutorial but for advanced stuff you need to install python on your laptop**
{: .prompt-tip }

---

## **Variables and Data Types**

### **Variables**
Think of variables like a box used to store something. In programming, a variable is a name that refers to a value saved in your computer’s memory. Instead of working directly with the value every time, you can store it inside a variable and reuse it whenever you need.

In other languages, declaring a variable can be annoying, in Python, it is the easiest thing.
```python
variable = value
```
It is recommended to name variables with significant names in order to keep your python code clean. Don't name your variables with `var1` and `var2`, use a variable name that matches its use.

### **Data Types**
```python
name = "Yacine" # string: a sequence of characters enclosed in quotes
age = 20 # integer: a whole number without a decimal point
PI = 3.15 # float: a number that has a decimal part
is_student = True # boolean: represents a True or False value
```
In other languages, you would need to define the type of each, but Python automatically recognizes which type it is based on the value you assign.

#### **Types Casting**

Sometimes, you may want to **change the data type** of a variable, this is called _type casting_.
For example, you might want to turn a number into a string so it can be printed with text, or convert text input into a number to perform calculations.
Python makes this easy with built-in functions like `int()`, `float()`, `str()`, and `bool()`.

```python
age = 20
age_string = str(age) # Convert integer to string
PI = 3.14
pi_str = str(PI) # Convert float to string
num = int("15") # Convert string to integer, it will generate an error if the string doesn't correspond to a number
pi_int = int(PI) # Covert float to integer, this will remove everything after the coma
decimal = float("2.5") # Convert string to float
flag = bool(1) # Convert number to boolean (1 becomes True and 0 becomes False)
```

> **To check variable type use print(type(variable)) name**
{: .prompt-tip }

---

## **Handling Input and Output**

### **Output**
Printing a text on the screen in python is the most straightforward thing to do, all you have to do is:
```python
print("Type the message to be printed in here")
```
You can also print values of variables, and it is also very simple:
```python
name = "Yacine"
print(name)
```
We can also print multiple variables at once:
```python
name = "Yacine"
age = 20
is_student = True
print(name,age,is_student)
```

### **Input**

For taking an input, we just use the function `input("Your message prompt goes here")`:

```python
age = input("What is your age: ")
print(age)
```
the result here will be by default a String, so casting should be used to make age an integer
```python
age = input("What is your age: ")
age = int(age)
print(age)
```

---

## **Working with Operators**

In Python, **operators** are special symbols used to perform operations on variables and values.
They allow you to do everything from simple math to logic checks and even testing if something exists in a sequence.

---

### **1. Arithmetic Operators**

Arithmetic operators are used to perform basic mathematical operations.

```python
x = 10
y = 3

print(x + y) # Addition → 13
print(x - y) # Subtraction → 7
print(x * y) # Multiplication → 30
print(x / y) # Division → 3.333...
print(x // y) # Euclidean Division → 3
print(x % y) # Modulus → Remainder of division (1)
print(x ** y) # Exponentiation → x to the power of y (1000)
```

### **2. Comparison Operators**

Comparison operators are used to compare two values.
They always return a **boolean value** (`True` or `False`).

```python
a = 5
b = 10

print(a == b) # Equal to → False
print(a != b) # Not equal to → True
print(a > b) # Greater than → False
print(a < b) # Less than → True
print(a >= b) # Greater than or equal to → False
print(a <= b) # Less than or equal to → True
```

### **3. Logical Operators**

Logical operators combine multiple conditions and return a boolean result.

```python
x = 5
y = 10

print(x > 2 and y > 5) # True (both conditions are True)
print(x > 10 or y > 5) # True (at least one condition is True)
print(not(x > 2)) # False (reverses the result)
```

### **4. Membership Operators**

Membership operators test if a value exists in a sequence like a string, list, or tuple.

```python
name = "Yacine"

print("a" in name) # True → 'a' is in "Yacine"
print("z" not in name) # True → 'z' is not in "Yacine"
```

### **5. Assignment Operators**

Assignment operators are used to assign values to variables.
They can also perform an operation and update the variable in one line.

```python
x = 10 # Assign 10 to x
x += 5 # Same as x = x + 5 → 15
x -= 3 # Same as x = x - 3 → 12
x *= 2 # Same as x = x * 2 → 24
x /= 4 # Same as x = x / 4 → 6.0
x %= 5 # Same as x = x % 5 → 1.0
x **= 3 # Same as x = x ** 3 → 1.0 (since 1^3 = 1)
```
These operators are the building blocks of almost every Python program.
You’ll use them constantly, whether doing math, comparing values, combining conditions, or updating variables.

---

## **Control Flow**

Control flow allows your program to make decisions and repeat actions depending on certain conditions.
This is how your code becomes dynamic instead of running line by line without logic.

### **1. Conditional Statements**

Conditional statements let you execute specific blocks of code only when certain conditions are met.
In Python, this is done using `if`, `elif`, and `else`.

```python
mark = 12
if mark < 10 :
print("You failed")
else:
print("You passed")
```
**Explanation:**
Here `You failed` will only be printed when mark is less than 10, otherwise `You passed` will be printed in the screen.

```python
number = 15
if age < 0:
print("Number is negative")
elif number == 0:
print("It is 0")
else:
print("Number is postive")
```
**Explanation:**
- `if` checks the first condition.
- `elif` means "else if" and checks another condition if the previous one is False.
- `else` runs if none of the conditions above are True.

### **2. Loops**

Loops let you repeat a block of code multiple times, which is very useful when working with data or performing repetitive tasks.

#### **For Loop**
A `for` loop runs through a sequence such as a list, a string, or a range of numbers.

```python
for i in range(5):
print("Number:", i)
```

**Output:**
```
Number: 0
Number: 1
Number: 2
Number: 3
Number: 4
```

You can also loop through lists or strings:

```python
for letter in "Python":
print(letter)
```
**Output:**
```
P
y
t
h
o
n
```

#### **While Loop**
A `while` loop runs as long as a condition remains True.

```python
count = 0
while count < 5:
print("Count:", count)
count += 1 # Increase count by 1 each loop
```

### **3. Loop Control Statements**

Loop control statements give you more control over how loops behave.

```python
for i in range(10):
if i == 5:
break # Stop the loop completely when i is 5
if i % 2 == 0:
continue # Skip even numbers
print(i)
```

**Explanation:**
- `break` immediately stops the loop.
- `continue` skips the current iteration and moves to the next one.

---

## **Defining and Using Functions**

Functions are blocks of reusable code that perform a specific task.
They make your code cleaner, more organized, and easier to maintain.
Instead of writing the same code multiple times, you can define a function once and use it whenever you need.

### **1. Defining a Function**

You can define a function using the `def` keyword, followed by the function name and parentheses.

```python
def greet():
print("Hello, Shellmates!")
```

To **call** the function and make it run, simply use its name:

```python
greet()
```

**Output:**
```
Hello, Shellmates!
```
### **2. Arguments and Return Values**

Functions can take **arguments** (inputs) and **return** values (outputs).
This allows you to create more flexible and powerful functions.

```python
def add_numbers(a, b):
return a + b

result = add_numbers(5, 3)
print("The sum is:", result)
```

**Explanation:**
- `a` and `b` are **arguments** that receive values when the function is called.
- The `return` statement sends a value back to the place where the function was called.
- Without `return`, the function only performs actions but does not give a result.

### **3. Default Arguments**

You can give arguments **default values**, so they are optional when calling the function.
If no value is provided, the default one will be used.

```python
def greet(name="Shellmate"):
print("Hello,", name)

greet("Yacine") # Uses the provided argument
greet() # Uses the default value
```

**Output:**
```
Hello, Yacine
Hello, Shellmate
```

### **4. Scope of Variables**

The **scope** of a variable determines where it can be accessed in your code.
There are two main types of scope: **local** and **global**.

```python
x = 10 # Global variable

def show_number():
y = 5 # Local variable
print("Inside the function:", y)
print("Accessing global variable:", x)

show_number()

print("Outside the function:", x)
# print(y) # This would cause an error because y only exists inside the function
```

**Explanation:**
- Variables defined **inside** a function are **local** to that function.
- Variables defined **outside** any function are **global** and can be used anywhere in the program (unless shadowed by a local variable).

> **Functions help you structure your code into small, reusable pieces.
Once you get used to writing functions, your programs will become much cleaner and easier to debug.**
{: .prompt-info }

---

## **Python Collections**

In Python, collections are data structures that let you store and organize multiple values in a single variable.
The most common ones are **lists**, **tuples**, and **dictionaries**.
Each has a specific use depending on how you want to store and access your data.

### **1. Lists**

A **list** is an ordered and changeable collection.
You can store different types of data inside it, and you can modify its contents anytime.

```python
fruits = ["apple", "banana", "cherry"]

print(fruits[0]) # Access an element
fruits[1] = "orange" # Modify an element
fruits.append("mango") # Add a new element
fruits.pop(0) # Remove the first element
print(fruits)
```

**Output:**
```
['orange', 'cherry', 'mango']
```

**Common list methods:**
- `append(item)` → adds an item at the end
- `pop(index)` → removes an item at the given index (or last one if no index is given)
- `len(list)` → returns the number of elements in the list

**Iterating through a list:**
```python
for fruit in fruits:
print(fruit)
```

### **2. Tuples**

A **tuple** is like a list, but **it cannot be changed** after creation (it is immutable).
They are used when you want to make sure the data stays constant.

```python
colors = ("red", "green", "blue")

print(colors[0]) # Access an element
# colors[1] = "yellow" # This would cause an error because tuples are immutable
```

You can still loop through them:
```python
for color in colors:
print(color)
```

### **3. Dictionaries**

A **dictionary** stores data as **key-value pairs**.
You can think of it as a mini database where each key has a corresponding value.

```python
student = {
"name": "Yacine",
"age": 20,
"is_student": True
}

print(student["name"]) # Access value by key
student["age"] = 21 # Modify value
student["major"] = "CS" # Add a new key-value pair
student.pop("is_student") # Remove a key-value pair

print(student)
```

**Output:**
```
{'name': 'Yacine', 'age': 21, 'major': 'CS'}
```

**Common dictionary methods:**
- `keys()` → returns all keys
- `values()` → returns all values
- `items()` → returns all key-value pairs
- `pop(key)` → removes a specific key-value pair

**Iterating through a dictionary:**
```python
for key, value in student.items():
print(key, ":", value)
```

### **4. Summary**

| Collection Type | Ordered | Changeable | Allows Duplicates | Access Method |
|------------------|----------|-------------|-------------------|----------------|
| List | Yes | Yes | Yes | By index |
| Tuple | Yes | No | Yes | By index |
| Dictionary | No | Yes | No (unique keys) | By key |

> **Python collections are powerful tools that make data handling simple and flexible.
You’ll use them constantly when writing scripts, handling input, or storing structured information.**
{: .prompt-info }

---

## **Using Python Modules**

Python comes with a lot of **built-in modules** that contain pre-written code you can use to perform common tasks.
Modules help you avoid rewriting code and let you focus on solving your problem.

### **1. Importing Modules**

You can use the `import` statement to bring in a module and access its functions and constants.

```python
import math
import random
```

### **2. Using the `math` Module**

The `math` module provides mathematical functions and constants.

```python
import math

print(math.pi) # Access the value of π → 3.141592653589793
print(math.sqrt(16)) # Square root → 4.0
print(math.factorial(5)) # Factorial → 120
```

### **3. Using the `random` Module**

The `random` module helps you generate random numbers or select random items.

```python
import random

print(random.randint(1, 10)) # Random integer between 1 and 10
print(random.choice(["apple", "banana", "cherry"])) # Random item from a list
print(random.random()) # Random float between 0.0 and 1.0
```

### **4. Importing Specific Functions**

You don’t always need to import the entire module.
You can import just the functions you need:

```python
from math import sqrt, pi

print(sqrt(25)) # 5.0
print(pi) # 3.141592653589793
```


### **5. Aliasing Modules**

You can also give a module a shorter name using the `as` keyword. This is useful for long module names:

```python
import random as rnd
print(rnd.randint(1, 6)) # Random dice roll
```

> **Using modules lets you reuse code written by others, access advanced functionality, and keep your programs clean and efficient.
Once you learn this, you can explore hundreds of built-in modules or even install third-party ones to expand Python’s capabilities.**
{: .prompt-info }

---

## Thank you for reading.
----------
