---
layout: post
title:  "Python 기초 - codecademy"
date:   2016-08-09
category: "python"
tags: [python]
---

codecademy의 python쪽 내용들을 정리해놓은 문서!

## Variables and data types

### welcome

~~~python
print "Hello world"
~~~

### variables and boolean

~~~python
myVar = 5
myBool = True # or False
myFloat = 1.23

print myVar
~~~
***
## Whitespace & statements

python에서 white space는 structure code를 위해 쓰인다.

`IndentationError: unindent does not match any outer indentation level` : 한 file에서는 tab과 space를 혼용해서는 안된다.

~~~python
def spam():
    eggs = 12
    return eggs

print spam()
~~~

***

## Comments

### Single line comments

~~~python
myVar = 4 # 이것은 주석이다
~~~

### Multi line comments

~~~python
"""이것은 모두 주석이다.
이것도 주석이다.
"""
myVar = 1
~~~

***

## Math operation

### Math

~~~python
addition = 72 + 23
subtraction = 108 - 204
multiplication = 108 * 0.5
division = 108 / 9
~~~

### Exponentiation & Modulo

~~~python
eggs = 10 ** 2
print eggs

spam = 3 % 2
print eggs
~~~
***
## Strings

### Strings

~~~python
name = "Ryan"
age  = "19"
food = "cheese"

escape = 'There\'s a snake in my boot!'

~~~

### Access by index
~~~python
"""
string "PYTHON" 은 6개의 문자를 가지고있다.

+---+---+---+---+---+---+
| P | Y | T | H | O | N |
+---+---+---+---+---+---+
  0   1   2   3   4   5
"""
fifth_letter = "MONTY"[4]

print fifth_letter
~~~

### String methods

#### len( string )
~~~python
parrot = "Norwegian Blue"
print len(parrot)
~~~

#### string.lower()
~~~python
parrot = "Norwegian Blue"
print parrot.lower()
~~~

#### string.upper()
~~~python
parrot = "norwegian blue"
print parrot.upper()
~~~

### toString str(var)

~~~python
pi = 3.14
print str(pi)
~~~

***
## Printing

~~~python
print "Monty Python"
###
the_machine_goes = "Ping!"

print the_machine_goes
###
print "Spam " + "and " + "eggs" # string concatenation
###
pi = 3.14
print "The value of pi is around " + str(pi)
~~~

### String formatting with %

~~~python
string_1 = "Camelot"
string_2 = "place"

print "Let's not go to %s. 'Tis a silly %s." % (string_1, string_2)
~~~

~~~python
name = raw_input("What is your name?")
quest = raw_input("What is your quest?")
color = raw_input("What is your favorite color?")

print "Ah, so your name is %s, your quest is %s, " \
"and your favorite color is %s." % (name, quest, color)
~~~

***

## Date and time

~~~python
from datetime import datetime
date = datetime.now()
print date

type(date) is datetime # True
type(date) is int      # False
type(date) is str      # False
~~~

### Extracting information

~~~python
from datetime import datetime

now = datetime.now()
print now.year
print now.month
print now.day
~~~

~~~python
from datetime import datetime
now = datetime.now()

print '%s/%s/%s' % (now.year, now.month, now.day)
~~~

~~~python
from datetime import datetime
now = datetime.now()

print '%s:%s:%s' % (now.hour, now.minute, now.second)
~~~
***
## Conditionals & Control Flow

### Comparators

| Name | Description |
|:-----:|:-----------|
| == | Equal to      |
| != | Not equal to  |
| <  | Less than     |
| <= | Less than or equal to |
| >  | Greater than  |
| >= | Greater than or equal to |

### And Or Not
~~~python
false = True and False
true  = True or False
notTrue = not True
~~~

1. not is evaluated first;
2. and is evaluated next;
3. or is evaluated last.

걍 괄호를 쓰는게 낫지...

***

## If else

~~~python
answer = "Left"

if answer == "Left":
    print "The answer is Left"
elif answer == "Right":
    print "The answer is Right"
else:
    print "The answer is %s" % answer
~~~

~~~python
def greater_less_equal_5(num):
    if num > 5:
        return 1
    elif num < 5:          
        return -1
    else:
        return 0

print greater_less_equal_5(4)
print greater_less_equal_5(5)
print greater_less_equal_5(6)
~~~

***

## Testing...
~~~python
original = raw_input("Enter a word:")
~~~

`string.isalpha()` : string이 alphabet으로 이루어져있으면 True, 아니면 False return.


~~~python
# word를 받아서 맨 앞의 글자를 뒤로 보내고 ay를 붙인다.
pyg = 'ay'

original = raw_input('Enter a word:')

if len(original) > 0 and original.isalpha():
    word = original.lower()
    first = word[0]
    new_word = word[1:len(word)] + first + pyg
    print new_word
else:
    print 'empty'
~~~

***

## Functions

~~~python
def tax(bill):
    """Adds 8% tax to a restaurant bill."""
    bill *= 1.08
    print "With tax: %f" % bill
    return bill

def tip(bill):
    """Adds 15% tip to a restaurant bill."""
    bill *= 1.15
    print "With tip: %f" % bill
    return bill

meal_cost = 100
meal_with_tax = tax(meal_cost)
meal_with_tip = tip(meal_with_tax)
~~~

**return하는 값이 다른 type이라도 가능하다.**
~~~python
def cube(number):
    return number**3

def by_three(number):
    if number % 3 == 0:
        return cube(number)
    else:
        return False
~~~

***
## Importing modules

~~~python
import math

print math.sqrt(25)
~~~
~~~python
from math import sqrt

print sqrt(25)
~~~

### View contents of the module
~~~python
import math            # Imports the math module
everything = dir(math) # Sets everything to a list of things from math
print everything       # Prints 'em all!
~~~

***

## Built-in Functions

### abs(), min(), max()
~~~python
def biggest_number( *args ): #argument의 list...
    print max( args )
    return max( args )

def smallest_number( *args ):
    print min( args )
    return min( args )

def distance_from_zero( arg ):
    print abs( arg )
    return abs( arg )


biggest_number( -10,-5,5,10 )
smallest_number( -10,-5,5,10 )
distance_from_zero( -10 )
~~~

### type()
~~~python
print type(53)
print type(1.23)
print type("Hello")

type(53) is int      # True
type(53) is str      # False
~~~

***

## Exam

~~~python
def hotel_cost(nights):
    return 140 * nights

def plane_ride_cost(city):
    if city == "Charlotte":
        return 183
    elif city == "Tampa":
        return 220
    elif city == "Pittsburgh":
        return 222
    else:
        return 475

def rental_car_cost(days):
    cost = 40 * days;

    if days >= 7:
        cost -= 50
    elif days>=3:
        cost -=20
    return cost

def trip_cost( city,
               days,
               spending_money ):
    return rental_car_cost(days) +
           hotel_cost(days) +
           plane_ride_cost(city) +
           spending_money

print trip_cost( "Los Angeles", 5, 600 )
~~~

## List

List는 Array와 비슷한 datatype이다. 다만 element들이 다른 datatype이어도 괜찮다.

### Length of list
~~~python
zoo_animals = ["pangolin", "cassowary", "sloth", "tiger" ];
# One animal is missing!

if len(zoo_animals) > 3:
	print "The first animal at the zoo is the " + zoo_animals[0]
	print "The second animal at the zoo is the " + zoo_animals[1]
	print "The third animal at the zoo is the " + zoo_animals[2]
	print "The fourth animal at the zoo is the " + zoo_animals[3]
~~~

### List append

빈 List 만들기 : `empty_list = []`

~~~python
suitcase = []
suitcase.append("sunglasses")
~~~

### List slicing

`list[a:b]` 는 a <= x < b인 x set을 return한다.

~~~python
suitcase = ["sunglasses", "hat", "passport", "laptop", "suit", "shoes"]

first  = suitcase[0:2]  # The first and second items (index zero and one)
middle = suitcase[2:4]  # Third and fourth items (index two and three)
last   = suitcase[4:6]  # The last two items (index four and five)
~~~

`string[0:3]`도 마찬가지이다..

### List index

~~~python
animals = ["aardvark", "badger", "duck", "emu", "fennec fox"]
duck_index = animals.index("duck")  
print duck_index
~~~

### List insert

~~~python
animals = ["aardvark", "badger", "duck", "emu", "fennec fox"]
animals.insert(2,"cobra")
print animals
~~~

### List remove

~~~python
backpack = ['xylophone', 'dagger', 'tent', 'bread loaf']
backpack.remove('dagger')
print backpack
~~~
### Looping with list

~~~python
my_list = [1,9,3,8,5,7]

for number in my_list:
    # Your code here
    print 2*number
~~~

### Sort list

~~~python
start_list = [5, 3, 1, 2, 4]
start_list.sort()

print start_list
~~~

***

## Dictionary

Dictionary는 key와 value의 쌍으로 이루어져있다.

`residents = {'Puffin' : 104, 'Sloth' : 105, 'Burmese Python' : 106}`와 같이 정의한다.
key에 해당하는 value를 얻어오고 싶으면 `residents['Puffin']`으로 얻어올 수 있다.

### Empty dictionary and adding an item
~~~python
menu = {} # Empty dictionary
menu['Chicken Alfredo'] = 14.50 # Adding new key-value pair
~~~

### Delete and update items with dictionary

~~~python
# key - animal_name : value - location
zoo_animals = { 'Unicorn' : 'Cotton Candy House',
'Sloth' : 'Rainforest Exhibit',
'Bengal Tiger' : 'Jungle House',
'Atlantic Puffin' : 'Arctic Exhibit',
'Rockhopper Penguin' : 'Arctic Exhibit'}
# A dictionary (or list) declaration may break across multiple lines

# Removing the items.
del zoo_animals['Unicorn']
del zoo_animals['Sloth']
del zoo_animals['Bengal Tiger']
# update the item.
zoo_animals['Rockhopper Penguin'] = 'Nowhere'

print zoo_animals
~~~

***

## Exam

~~~python
inventory = {
    'gold' : 500,
    'pouch' : ['flint', 'twine', 'gemstone'], # Assigned a new list to 'pouch' key
    'backpack' : ['xylophone','dagger', 'bedroll','bread loaf']
}

# Adding a key 'burlap bag' and assigning a list to it
inventory['burlap bag'] = ['apple', 'small ruby', 'three-toed sloth']

# Sorting the list found under the key 'pouch'
inventory['pouch'].sort()

# Your code here
inventory['pocket'] = ['seashell', 'strange berry', 'lint']

inventory['backpack'].sort()
inventory['backpack'].remove('dagger');
inventory['gold'] += 50
~~~

## Class

```python
class Fruit(object):  
    """A class that makes various tasty fruits."""
    def __init__(self, name, color, flavor, poisonous): # constructor와 같은 듯...
        self.name = name
        self.color = color
        self.flavor = flavor
        self.poisonous = poisonous

    def description(self):                              # method들을 이렇게 정의할 수 있다.
        print "I'm a %s %s and I taste %s." % (self.color, self.name, self.flavor)

    def is_edible(self):
        if not self.poisonous:
            print "Yep! I'm edible."
        else:
            print "Don't eat me! I am super poisonous."

lemon = Fruit("lemon", "yellow", "sour", False)

lemon.description()
lemon.is_edible()
```

results :
```
I'm a yellow lemon and I taste sour.
Yep! I'm edible.
None
```

### Class Syntax

```python
class NewClass(object): # object를 inherit한다.
    # Class magic here
```
convention에 따라 class name은 대문자로 시작한다.

### Class methods

#### __init__()

**__init__()** 함수는 정의한 class를 만들 때 initialize하기 위해 쓰인다.
또한 모든 method는 항상 **self라는 argument**를 동반한다. self는 내가 만들 object를 refer한다.

```python
class Animal(object):
    def __init__( self ):
        pass
```

또한 그 뒤에 다른 argument를 넣을 수 있다.

```python
class Animal(object):
    def __init__( self, name ):
        self.name = name
```

### class member variable 접근

```python
class Animal(object):
    def __init__( self, name ):
        self.name = name

zebra = Animal("Jeffrey")   # 이런 식으로 Instance를 만들 수 있다.

print zebra.name            # 이런 식으로 member variable에 접근가능하다.
```


### Member variable 좀더...

```python
class Animal(object):
    """Makes cute animals."""
    is_alive = True             # 요것도 member variable이다!
    def __init__(self, name, age):
        self.name = name
        self.age = age

zebra = Animal("Jeffrey", 2)
giraffe = Animal("Bruce", 1)
panda = Animal("Chad", 7)

print zebra.name, zebra.age, zebra.is_alive
print giraffe.name, giraffe.age, giraffe.is_alive
print panda.name, panda.age, panda.is_alive
```

results :
```
Jeffrey 2 True
Bruce 1 True
Chad 7 True
```

```python
class ShoppingCart(object):
    """Creates shopping cart objects
    for users of our fine website."""
    items_in_cart = {}
    def __init__(self, customer_name):
        self.customer_name = customer_name

    def add_item(self, product, price):
        """Add product to the cart."""
        if not product in self.items_in_cart:
            self.items_in_cart[product] = price
            print product + " added."
        else:
            print product + " is already in the cart."

    def remove_item(self, product):
        """Remove product from the cart."""
        if product in self.items_in_cart:
            del self.items_in_cart[product]
            print product + " removed."
        else:
            print product + " is not in the cart."

my_cart = ShoppingCart( "durotan" )
my_cart.add_item( "sazabi", 80000 )
```