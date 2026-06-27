# kwaw-Python Special (Dunder) Methods

Dunder methods (double underscore methods) let custom classes behave like Python's built-in types. Python calls them automatically when you use operators, keywords, or built-in functions on your objects.

Example: len(my_obj) internally calls my_obj.__len__()

Why They Matter

Make custom objects feel natural and Pythonic
Support operator overloading (+, -, ==, etc.)
Enable iteration, indexing, and callable behaviour
Improve readability of user-defined classes
Overview of Categories

Category	Methods
Initialization	__init__
String output	__str__, __repr__
Length	__len__
Comparison	__eq__
Arithmetic	__add__, __sub__, __mul__
Item access	__getitem__, __setitem__
Membership	__contains__
Callable	__call__
Iteration	__iter__, __next__
Boolean evaluation	__bool__
Cleanup	__del__
1. __init__ — The Initializer

Runs automatically when a new object is created. Use it to assign starting values to the object's attributes.


class Book:
    def __init__(self, title, author, price):
        self.title  = title
        self.author = author
        self.price  = price

b1 = Book('The Alchemist', 'Paulo Coelho', 29.99)
print('Title  :', b1.title)
print('Author :', b1.author)
print('Price  : GHS', b1.price)

     
2. __str__ and __repr__ — String Representations

__str__ gives a user-friendly description (used by print).
__repr__ gives a developer-friendly description used for debugging.

class Book:
    def __init__(self, title, author, price):
        self.title  = title
        self.author = author
        self.price  = price

    def __str__(self):
        return f'"{self.title}" by {self.author} — GHS {self.price:.2f}'

    def __repr__(self):
        return f"Book('{self.title}', '{self.author}', {self.price})"

b1 = Book('Atomic Habits', 'James Clear', 45.00)
print(str(b1))   # user-friendly
print(repr(b1))  # developer-friendly

     
3. __len__ — Defining Length

Called when len() is used on an object. Must return an integer.


class Bookshelf:
    def __init__(self, books):
        self.books = books

    def __len__(self):
        return len(self.books)

shelf = Bookshelf(['Dune', '1984', 'Sapiens', 'Thinking Fast and Slow'])
print('Books on shelf:', len(shelf))

     
4. __eq__ — Equality Comparison

Defines what == means between two objects of your class.


class Book:
    def __init__(self, title, isbn):
        self.title = title
        self.isbn  = isbn

    def __eq__(self, other):
        # Two books are the same edition if they share an ISBN
        return self.isbn == other.isbn

b1 = Book('Rich Dad Poor Dad',         '978-1612680194')
b2 = Book('Rich Dad Poor Dad (New Ed.)','978-1612680194')
b3 = Book('The Richest Man in Babylon', '978-0451205360')

print('b1 == b2:', b1 == b2)  # True  — same ISBN
print('b1 == b3:', b1 == b3)  # False — different ISBN

     
5. Operator Overloading — __add__, __sub__, __mul__

Let custom objects respond naturally to +, -, and *.


class Money:
    def __init__(self, amount, currency='GHS'):
        self.amount   = amount
        self.currency = currency

    def __add__(self, other):
        return Money(self.amount + other.amount, self.currency)

    def __sub__(self, other):
        return Money(self.amount - other.amount, self.currency)

    def __mul__(self, factor):
        return Money(self.amount * factor, self.currency)

    def __str__(self):
        return f'{self.currency} {self.amount:.2f}'

wallet = Money(200.00)
cost   = Money(75.50)
print('Balance after purchase:', wallet - cost)
print('Double the cost       :', cost * 2)
print('Combined total        :', wallet + cost)

     
6. __getitem__ — Index/Key Access

Allows bracket-notation reads: obj[index].


class Catalogue:
    def __init__(self, items):
        self.items = items

    def __getitem__(self, index):
        return self.items[index]

cat = Catalogue(['Dune', 'Brave New World', 'Fahrenheit 451', '1984'])
print('First book :', cat[0])
print('Last book  :', cat[-1])
print('Slice      :', cat[1:3])

     
7. __setitem__ — Index/Key Assignment

Called when you write obj[key] = value.


class Inventory:
    def __init__(self):
        self.stock = {}

    def __setitem__(self, title, quantity):
        self.stock[title] = quantity

    def __getitem__(self, title):
        return self.stock.get(title, 0)

inv = Inventory()
inv['Dune'] = 12
inv['1984'] = 7
print('Dune in stock:', inv['Dune'])
print('1984 in stock:', inv['1984'])

     
8. __contains__ — Membership Testing

Powers the in keyword for custom objects.


class Wishlist:
    def __init__(self, books):
        self.books = books

    def __contains__(self, title):
        return title in self.books

wishlist = Wishlist(['The Alchemist', 'Meditations', 'Deep Work'])
print('"Meditations" in wishlist?', 'Meditations' in wishlist)
print('"Harry Potter" in wishlist?', 'Harry Potter' in wishlist)

     
9. __call__ — Making Objects Callable

Lets you use an object like a function: obj().


class Discount:
    def __init__(self, percentage):
        self.percentage = percentage

    def __call__(self, original_price):
        saving = original_price * (self.percentage / 100)
        return round(original_price - saving, 2)

student_discount = Discount(15)  # 15% off
loyalty_discount = Discount(25)  # 25% off

price = 80.00
print(f'Original price  : GHS {price}')
print(f'After 15% off   : GHS {student_discount(price)}')
print(f'After 25% off   : GHS {loyalty_discount(price)}')

     
10. __iter__ and __next__ — Custom Iteration

Together these two methods make an object usable in a for loop.

__iter__ returns the iterator object (usually self).
__next__ returns the next value, or raises StopIteration when done.

class PriceRange:
    """Iterates over prices from start to end (inclusive) at a given step."""
    def __init__(self, start, end, step=10):
        self.current = start
        self.end     = end
        self.step    = step

    def __iter__(self):
        return self

    def __next__(self):
        if self.current > self.end:
            raise StopIteration
        value = self.current
        self.current += self.step
        return value

print('Prices from GHS 50 to GHS 100 (step GHS 10):')
for p in PriceRange(50, 100, 10):
    print(f'  GHS {p}')

     
11. __bool__ — Boolean Evaluation

Called when an object is tested with if obj: or bool(obj). Must return True or False.


class Inventory:
    def __init__(self, title, quantity):
        self.title    = title
        self.quantity = quantity

    def __bool__(self):
        # An item is 'truthy' only when it has stock available
        return self.quantity > 0

    def __str__(self):
        return f'{self.title} (qty: {self.quantity})'

item1 = Inventory('Dune', 5)
item2 = Inventory('Sapiens', 0)

for item in [item1, item2]:
    status = 'IN STOCK' if item else 'OUT OF STOCK'
    print(f'{item} -> {status}')

print('bool(item1):', bool(item1))
print('bool(item2):', bool(item2))

     
12. __del__ — Destructor / Cleanup

Called when an object is deleted or garbage-collected. Useful for releasing resources such as file handles or network connections.


class LibrarySession:
    def __init__(self, member_name):
        self.member_name = member_name
        print(f'[Session started] Welcome, {self.member_name}!')

    def __del__(self):
        print(f'[Session ended]   Goodbye, {self.member_name}. See you soon!')

session = LibrarySession('Kwame Asante')
print('... browsing books ...')
del session

     
Summary Table

#	Method	Triggered By	Purpose
1	__init__	ClassName()	Initialize object attributes
2	__str__	print(obj)	Human-readable string
3	__repr__	repr(obj)	Debug-friendly string
4	__len__	len(obj)	Return size or count
5	__eq__	obj1 == obj2	Equality check
6	__add__	obj1 + obj2	Addition
7	__sub__	obj1 - obj2	Subtraction
8	__mul__	obj * n	Multiplication
9	__getitem__	obj[i]	Index / key read
10	__setitem__	obj[i] = v	Index / key write
11	__contains__	x in obj	Membership test
12	__call__	obj()	Call like a function
13	__iter__	for x in obj	Start iteration
14	__next__	Next loop step	Advance the iterator
15	__bool__	bool(obj), if obj:	Truthiness
16	__del__	del obj	Cleanup on deletion
Real-World Applications

Method	Practical Scenario
__init__	Creating user accounts, product listings, bank accounts
__str__	Displaying records in reports or dashboards
__repr__	Logging objects during debugging or testing
__len__	Counting cart items, employees, or dataset rows
__eq__	Deduplicating records using unique identifiers (ISBN, NIN)
__add__ / __sub__	Currency arithmetic, coordinate calculations
__mul__	Scaling vectors, calculating bulk prices
__getitem__	Building custom data containers or lookup tables
__setitem__	Updating values in a custom collection
__contains__	Checking membership in a role or permission list
__call__	Reusable discount calculators, validators, transformers
__iter__ / __next__	Paginators, data pipelines, custom number generators
__bool__	Checking if stock is available, a session is active, or a user is verified
__del__	Closing file handles, DB connections, or logging session end