# clean-code-python

[![Build Status](https://travis-ci.com/zedr/clean-code-python.svg?branch=master)](https://travis-ci.com/zedr/clean-code-python)
[![](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/download/releases/3.8.3/)

# Table of Contents

- [clean-code-python](#clean-code-python)
- [Table of Contents](#table-of-contents)
    - [Introduction](#introduction)
        - [Why do we need clean code?](#why-do-we-need-clean-code)
    - [**Variables**](#variables)
        - [Use meaningful and pronounceable variable names](#use-meaningful-and-pronounceable-variable-names)
        - [Use the same vocabulary for the same type of variable](#use-the-same-vocabulary-for-the-same-type-of-variable)
        - [Use searchable names](#use-searchable-names)
        - [Avoid encodings](#avoid-encodings)
        - [Use explanatory variables](#use-explanatory-variables)
        - [Avoid Mental Mapping](#avoid-mental-mapping)
        - [Don"t add unneeded context](#dont-add-unneeded-context)
        - [Pick one word per concept](#pick-one-word-per-concept)
        - [Use default arguments instead of short circuiting or conditionals](#use-default-arguments-instead-of-short-circuiting-or-conditionals)
    - [**Functions**](#functions)
        - [Functions should do one thing](#functions-should-do-one-thing)
        - [Functions should be small](#functions-should-be-small)
        - [One level of abstraction per function](#one-level-of-abstraction-per-function)
        - [Reading code from top to bottom](#reading-code-from-top-to-bottom)
        - [Function arguments (2 or fewer ideally)](#function-arguments-2-or-fewer-ideally)
        - [Function names should say what they do](#function-names-should-say-what-they-do)
        - [Functions should only be one level of abstraction](#functions-should-only-be-one-level-of-abstraction)
        - [Don"t use flags as function parameters](#dont-use-flags-as-function-parameters)
        - [Avoid side effects](#avoid-side-effects)
        - [Avoiding side effects part 2](#avoiding-side-effects-part-2)
    - [**Objects and Data Structures**](#objects-and-data-structures)
    - [**Classes**](#classes)
        - [**Single Responsibility Principle (SRP)**](#single-responsibility-principle-srp)
        - [**Open/Closed Principle (OCP)**](#openclosed-principle-ocp)
        - [**Liskov Substitution Principle (LSP)**](#liskov-substitution-principle-lsp)
        - [**Interface Segregation Principle (ISP)**](#interface-segregation-principle-isp)
        - [**Dependency Inversion Principle (DIP)**](#dependency-inversion-principle-dip)
    - [**Don"t repeat yourself (DRY)**](#dont-repeat-yourself-dry)
    - [Miscallenous advice](#miscallenous-advice)
        - [Switch statements](#switch-statements)

## Introduction

### Why do we need clean code?
"The ratio of time spent on reading vs writing code is well over 10:1. We are constantly reading old code
as part of the effort to write new code. Making code easier to read makes it easier to write" [ref bob martin]

The boy scout rule:
- if we all checked in code a little cleaner than when we checked it out, the code can simply not rot.
- Can you imagine working on a project where the code got better as time went on? :mindblown:

Inspired from [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-ruby)

Targets Python3.7+

## **Variables**
- Use intention revealing names and don't be afraid to change them if you find better names
- Why does it exist? How is it used? 
- If a name requires a comment it does not reveal it's intention

Bad:
```python
d: int  # elapsed time in days
```
Good:
```python
elapsed_time_in_days: int
```

Be explicit with your variable names

```python
def get_them(input_list):
    output = []
    for x in intput_list:
        if x == 4
            output.append(x)
        
    return output
```

What sorts of things are implied by this code?
1. What kinds of things are in `input_list`?
2. What is the significance of the value 4?
3. How would I use the list being returned?


better:
```python
FLAGGED = 4

def get_flagged_cell(cells):
    flagged_cells = []
    for cell in cells:
        if cell == FLAGGED:
            flagged_cells.append(x)
        
    return flagged_cells
```

best: (sidenote about list comprehensions)
```python
def get_flagged_cell(cells):
    return [cell for cell in cells if cell == FLAGGED]
```

Notice how much more *EXPLICIT* the code is in the improved examples

### Use meaningful and pronounceable variable names

**Bad:**
```python
import datetime


ymdstr = datetime.date.today().strftime("%y-%m-%d")
```
How do I pronounce this? "yem dee string"? Fun or not, we're tolerating poor naming here. New developers
will have to have this variable explained to them. Use proper english terms even if the term is a little
longer. Let autocomplete save the keystrokes for you.

**Good**:
```python
import datetime


current_date: str = datetime.date.today().strftime("%y-%m-%d")
```

**[⬆ back to top](#table-of-contents)**

### Use the same vocabulary for the same type of variable

**Bad:**
Here we use three different names for the same underlying entity:
```python
def get_user_info(): pass
def get_client_data(): pass
def get_customer_record(): pass
```

**Good**:
If the entity is the same, you should be consistent in referring to it in your functions:
```python
def get_user_info(): pass
def get_user_data(): pass
def get_user_record(): pass
```

**Even better**
Python is (also) an object oriented programming language. If it makes sense, package the functions together with the concrete implementation
of the entity in your code, as instance attributes, property methods, or methods:

```python
from typing import Union, Dict, Text


class Record:
    pass


class User:
    info : str

    @property
    def data(self) -> Dict[Text, Text]:
        return {}

    def get_record(self) -> Union[Record, None]:
        return Record()
        
```

You want to avoid adding noise to your variable names. Noise words add "meaningless" distinction. In the above example
`info` and `data` are indistinct noise words. How do programmers know which function to call?
Distinguish names so the reader knows the difference

**[⬆ back to top](#table-of-contents)**

### Use searchable names
We will read more code than we will ever write. It"s important that the code we do write is 
readable and searchable. By *not* naming variables that end up being meaningful for 
understanding our program, we hurt our readers.
Make your names searchable.

**Bad:**
```python
import time


# What is the number 86400 for again?
time.sleep(86400)
```

**Good**:
```python
import time


# Declare them in the global namespace for the module.
SECONDS_IN_A_DAY = 60 * 60 * 24
time.sleep(SECONDS_IN_A_DAY)
```

Single letter variable names should almost always be avoided. Personally, I like the length of the name 
to correspond to the size of its scope. If a variable or constant is used in multiple places/bodies of code
it is imperative that you give it a search friendly name.

**[⬆ back to top](#table-of-contents)**

### Avoid encodings
In modern python we have a rich type annotation system and we can use type checkers
to enforce these types, if you want to add them. What you want to avoid is adding type
information to the variable name. Given that python is also a dynamic language this may
also lead to disinformation.

Bad
```python
car_dict = {"make": "tesla"}
```

Better:
```python
car = {"make": "tesla"}
```

### Use explanatory variables
**Bad:**
```python
import re


address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$"

matches = re.match(city_zip_code_regex, address)
if matches:
    print(f"{matches[1]}: {matches[2]}")
```

**Not bad**:

It"s better, but we are still heavily dependent on regex.

```python
import re


address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$"
matches = re.match(city_zip_code_regex, address)

if matches:
    city, zip_code = matches.groups()
    print(f"{city}: {zip_code}")
```

**Good**:

Decrease dependence on regex by naming subpatterns.
```python
import re


address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(?P<city>.+?)\s*(?P<zip_code>\d{5})?$"

matches = re.match(city_zip_code_regex, address)
if matches:
    print(f"{matches['city']}, {matches['zip_code']}")
```
**[⬆ back to top](#table-of-contents)**

### Avoid Mental Mapping
Don’t force the reader of your code to translate what the variable means.
Explicit is better than implicit.

**Bad:**
```python
seq = ("Austin", "New York", "San Francisco")

for item in seq:
    #do_stuff()
    #do_some_other_stuff()

    # Wait, what's `item` again?
    print(item)
```

**Good**:
```python
locations = ("Austin", "New York", "San Francisco")

for location in locations:
    #do_stuff()
    #do_some_other_stuff()
    # ...
    print(location)
```
**[⬆ back to top](#table-of-contents)**


### Don"t add unneeded context
This avoids adding disinformation. Do not refer to a a grouping of accounts
as an `accounts_list` unless your stakeholders say it's a list. Try to use
vocabulary from the business domain if your class/object name tells you something, 
don't repeat that in your variable name.

**Bad:**

```python
class Car:
    car_make: str
    car_model: str
    car_color: str
```

**Good**:

```python
class Car:
    make: str
    model: str
    color: str
```

### Pick one word per concept
Pick one word for an abstract concept and stick with it. For example, it's confusing to have
`fetch`, `retrieve`, `get` as equivalent methods on different classes. How do you remember
which name goes on each class? 

_"A consistent lexicon is a great boon to the programmers who must use your code"_

**[⬆ back to top](#table-of-contents)**

### Use default arguments instead of short circuiting or conditionals

**Tricky**

Why write:

```python
import hashlib


def create_micro_brewery(name):
    name = "Hipster Brew Co." if name is None else name
    slug = hashlib.sha1(name.encode()).hexdigest()
    # etc.
```

... when you can specify a default argument instead? This also makes it clear that
you are expecting a string as the argument.

**Good**:

```python
from typing import Text
import hashlib


def create_micro_brewery(name: Text = "Hipster Brew Co."):
    slug = hashlib.sha1(name.encode()).hexdigest()
    # etc.
```

**[⬆ back to top](#table-of-contents)**
## **Functions**


### Functions should do one thing
This is by far the most important rule in software engineering. When functions do more 
than one thing, they are harder to compose, test, and reason about. When you can isolate 
a function to just one action, they can be refactored easily and your code will read much 
cleaner. If you take nothing else away from this guide other than this, you"ll be ahead 
of many developers.

**Bad:**
```python
from typing import List


class Client:
    active: bool


def email(client: Client) -> None:
    pass


def email_clients(clients: List[Client]) -> None:
    """Filter active clients and send them an email.
    """
    for client in clients:
        if client.active:
            email(client)
```

**Good**:
```python
from typing import List


class Client:
    active: bool


def email(client: Client) -> None:
    pass


def get_active_clients(clients: List[Client]) -> List[Client]:
    """Filter active clients.
    """
    return [client for client in clients if client.active]


def email_clients(clients: List[Client]) -> None:
    """Send an email to a given list of clients.
    """
    for client in get_active_clients(clients):
        email(client)
```

Do you see an opportunity for using generators now?

**Even better**
```python
from typing import Generator, Iterator


class Client:
    active: bool


def email(client: Client):
    pass


def active_clients(clients: Iterator[Client]) -> Generator[Client, None, None]:
    """Only active clients"""
    return (client for client in clients if client.active)


def email_client(clients: Iterator[Client]) -> None:
    """Send an email to a given list of clients.
    """
    for client in active_clients(clients):
        email(client)
```

### Functions should be small
This rule is a natural evolution of the first rule. Your functions should be small. 
The second rule is that they should be smaller than that.  How small should your functions be?
A good way to know if your function is small enough and is doing one thing is to check if another
function could be extracted from it without merely restating its implementation. For example:

**Listing 1 - Bad:**

```python
class MonthlyDonationsReport:
    # omitted for brevity...
    def aggregate(self, organization_id, n_years=DEFAULT_N_YEARS):
        current_year = datetime.now().year

        result = self.collection.aggregate(
            organization_id,
            [
                {
                    "$match": {
                        f"{self.time_field}": {
                            "$gte": datetime(current_year + 1 - n_years, 1, 1)
                        }
                    }
                },
                {
                    "$group": {
                        "_id": {
                            "year": {"$year": f"${self.time_field}"},
                            "month": {"$month": f"${self.time_field}"},
                        },
                        "sum": {"$sum": f"${self.accumulator_field}"},
                        "count": {"$sum": 1},
                    }
                },
                {"$sort": {"_id.year": 1, "_id.month": 1}},
            ],
        )

        self._data = query_to_df(result)
        self._data.rename(
            columns={"_id.month": "month", "_id.year": "year"}, inplace=True
        )
        self._data = self._fill_missing_months(self._data, n_years)
        return self.data

```

**Listing 2 - Better:**
```python
    def aggregate(self, organization_id, n_years=DEFAULT_N_YEARS):
        current_year = datetime.now().year
        self._data = self._group_monthly_donations(organization_id, n_years)
        self._data = self._fill_missing_months(self._data, n_years)
        return self.data
    
```

### One level of abstraction per function
To make sure our function is doing one thing, we need to make sure that the statements
within our function are all at the same level of abstraction. 

Notice above in Listing 1 that the function has 3 levels of abstraction.

- The first aggregation operates on the raw mongo collection (low level of abstraction)
- Next we operate on the dataframe returned by the query (medium level of abstraction)
- Lastly the `_fill_missing_months` method is a high level of abstraction

In listing 2 above. All expressions at an even level of abstraction.

Mixing levels of abstraction is confusing for the reader. Readers may not be able to tell what's an
essential concept, or a detail. Even worse, once details are mixed with essential concepts, more and more
details typically get added to the function causing it to grow.

### Reading code from top to bottom
We want code to read like a top down narrative. We want every function to be followed by those
at the next level of abstraction. In other words, we want to be able to read code as though it were
a set of `To` paragraphs. For example:

    To compute the donor scores we create a pipeline which consists of 4 steps, fetching, preprocessing, processing and post processing.
        
        To fetch the data we must fetch contact data and donations data.
        
            To fetch the donations data we must query the database for all donations within the last year for the input organization
            
            To fetch all the contacts we must query the database for all contacts for the input organization

This provides the reader with a nice entry point into the program.

**Good example provided below (nice work @Surohit):**

```python
class DonationsEmailsInboundsWeatherFetcher:
    # Init is like the intro paragraph. It sets us up for what to expect
    def __init__(self, region=None, organization_id=None):
        self.region = region
        self.organization_id = organization_id
    
    # The fetch method is the main body. The reader can clearly see the core ideas being expressed
    # If we go further down the file we see the level of abstraction increase.
    # These are the lower level details that the reader can look into after understanding the main ideas
    def fetch(self, region, organization_id):  
        self.region = region
        self.organization_id = organization_id

        org_city, org_country = self.fetch_org_cities()
        ripeness_donations = self.fetch_donations(org_city, org_country)
        ripeness_emails = self.fetch_emails(ripeness_donations)
        inbounds = self.fetch_inbounds(ripeness_donations, ripeness_emails)
        weather_data = self.fetch_org_weather(ripeness_donations)

        return RipenessDonationsData(
            organization_id=organization_id,
            region=region,
            donations=ripeness_donations.df_donations,
            emails=ripeness_emails.df_emails,
            inbound_emails=inbounds,
            sunshine=weather_data.sunshine,
            temperature=weather_data.temperature,
            start_date=ripeness_donations.min_date,
            end_date=ripeness_donations.max_date,
            contacts_list=ripeness_donations.contacts,
        )

```

### Function arguments (2 or fewer ideally)
Limiting the amount of function parameters is incredibly important because it makes 
testing your function easier. Having more than three leads to a combinatorial explosion 
where you have to test tons of different cases with each separate argument.

Zero arguments is the ideal case. One or two arguments is ok, and three should be avoided. 
Anything more than that should be consolidated. Usually, if you have more than two 
arguments then your function is trying to do too much. In cases where it"s not, most 
of the time a higher-level object will suffice as an argument.

**Bad:**
```python
def create_menu(title, body, button_text, cancellable):
    pass
```

**Java-esque**:
```python
class Menu:
    def __init__(self, config: dict):
        self.title = config["title"]
        self.body = config["body"]
        # ...

menu = Menu(
    {
        "title": "My Menu",
        "body": "Something about my menu",
        "button_text": "OK",
        "cancellable": False
    }
)
```

**Also good**
```python
from typing import Text


class MenuConfig:
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: Text
    body: Text
    button_text: Text
    cancellable: bool = False


def create_menu(config: MenuConfig) -> None:
    title = config.title
    body = config.body
    # ...


config = MenuConfig()
config.title = "My delicious menu"
config.body = "A description of the various items on the menu"
config.button_text = "Order now!"
# The instance attribute overrides the default class attribute.
config.cancellable = True

create_menu(config)
```

**Fancy**
```python
from typing import NamedTuple


class MenuConfig(NamedTuple):
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool = False


def create_menu(config: MenuConfig):
    title, body, button_text, cancellable = config
    # ...


create_menu(
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!"
    )
)
```

**Even fancier**
```python
from typing import Text
from dataclasses import astuple, dataclass


@dataclass
class MenuConfig:
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: Text
    body: Text
    button_text: Text
    cancellable: bool = False

def create_menu(config: MenuConfig):
    title, body, button_text, cancellable = astuple(config)
    # ...


create_menu(
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!"
    )
)
```

**Even fancier, Python3.8+ only**
```python
from typing import TypedDict, Text


class MenuConfig(TypedDict):
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: Text
    body: Text
    button_text: Text
    cancellable: bool


def create_menu(config: MenuConfig):
    title = config["title"]
    # ...


create_menu(
    # You need to supply all the parameters
    MenuConfig(
        title="My delicious menu", 
        body="A description of the various items on the menu",
        button_text="Order now!",
        cancellable=True
    )
)
```
**[⬆ back to top](#table-of-contents)**

**[⬆ back to top](#table-of-contents)**

### Function names should say what they do

**Bad:**

```python
class Email:
    def handle(self) -> None:
        pass

message = Email()
# What is this supposed to do again?
message.handle()
```

**Good:**

```python
class Email:
    def send(self) -> None:
        """Send this message"""

message = Email()
message.send()
```

**[⬆ back to top](#table-of-contents)**

### Functions should only be one level of abstraction

When you have more than one level of abstraction, your function is usually doing too 
much. Splitting up functions leads to reusability and easier testing.

**Bad:**

```python
# type: ignore

def parse_better_js_alternative(code: str) -> None:
    regexes = [
        # ...
    ]

    statements = code.split('\n')
    tokens = []
    for regex in regexes:
        for statement in statements:
            pass

    ast = []
    for token in tokens:
        pass

    for node in ast:
        pass
```

**Good:**

```python
from typing import Tuple, List, Text, Dict


REGEXES: Tuple = (
   # ...
)


def parse_better_js_alternative(code: Text) -> None:
    tokens: List = tokenize(code)
    syntax_tree: List = parse(tokens)

    for node in syntax_tree:
        pass


def tokenize(code: Text) -> List:
    statements = code.split()
    tokens: List[Dict] = []
    for regex in REGEXES:
        for statement in statements:
            pass

    return tokens


def parse(tokens: List) -> List:
    syntax_tree: List[Dict] = []
    for token in tokens:
        pass

    return syntax_tree
```

**[⬆ back to top](#table-of-contents)**

### Don"t use flags as function parameters

Flags tell your user that this function does more than one thing. Functions 
should do one thing. Split your functions if they are following different code 
paths based on a boolean.

**Bad:**

```python
from typing import Text
from tempfile import gettempdir
from pathlib import Path


def create_file(name: Text, temp: bool) -> None:
    if temp:
        (Path(gettempdir()) / name).touch()
    else:
        Path(name).touch()
```

**Good:**

```python
from typing import Text
from tempfile import gettempdir
from pathlib import Path


def create_file(name: Text) -> None:
    Path(name).touch()


def create_temp_file(name: Text) -> None:
    (Path(gettempdir()) / name).touch()
```

**[⬆ back to top](#table-of-contents)**

### Avoid side effects

A function produces a side effect if it does anything other than take a value in 
and return another value or values. For example, a side effect could be writing 
to a file, modifying some global variable, or accidentally wiring all your money
to a stranger.

Now, you do need to have side effects in a program on occasion - for example, like
in the previous example, you might need to write to a file. In these cases, you
should centralize and indicate where you are incorporating side effects. Don"t have
several functions and classes that write to a particular file - rather, have one
(and only one) service that does it.

The main point is to avoid common pitfalls like sharing state between objects
without any structure, using mutable data types that can be written to by anything,
or using an instance of a class, and not centralizing where your side effects occur.
If you can do this, you will be happier than the vast majority of other programmers.

**Bad:**

```python
# type: ignore

# This is a module-level name.
# It"s good practice to define these as immutable values, such as a string.
# However...
fullname = "Ryan McDermott"

def split_into_first_and_last_name() -> None:
    # The use of the global keyword here is changing the meaning of the
    # the following line. This function is now mutating the module-level
    # state and introducing a side-effect!
    global fullname
    fullname = fullname.split()

split_into_first_and_last_name()

# MyPy will spot the problem, complaining about 'Incompatible types in 
# assignment: (expression has type "List[str]", variable has type "str")'
print(fullname)  # ["Ryan", "McDermott"]

# OK. It worked the first time, but what will happen if we call the
# function again?
```

**Good:**
```python
from typing import List, AnyStr


def split_into_first_and_last_name(name: AnyStr) -> List[AnyStr]:
    return name.split()

fullname = "Ryan McDermott"
name, surname = split_into_first_and_last_name(fullname)

print(name, surname)  # => Ryan McDermott
```

**Also good**
```python
from typing import Text
from dataclasses import dataclass


@dataclass
class Person:
    name: Text

    @property
    def name_as_first_and_last(self) -> list:
        return self.name.split() 


# The reason why we create instances of classes is to manage state!
person = Person("Ryan McDermott")
print(person.name)  # => "Ryan McDermott"
print(person.name_as_first_and_last)  # => ["Ryan", "McDermott"]
```

### Avoiding side effects part 2
In Python, everything is an object and everything is passed by value, 
but these values are references to objects. In the case of objects and lists, if your method makes a change
in a shopping cart array, for example, by adding an item to purchase,
then any other method that uses that `cart` array will be affected by this
addition. That may be great, however it can be bad too. Let's imagine a bad
situation:

The user clicks the "Purchase", button which calls a `purchase` method that
spawns a network request and sends the `cart` array to the server. Because
of a bad network connection, the `purchase` method has to keep retrying the
request. Now, what if in the meantime the user accidentally clicks "Add to Cart"
button on an item they don't actually want before the network request begins?
If that happens and the network request begins, then that purchase method
will send the accidentally added item because it has a reference to a shopping
cart array that the `add_item_to_cart` method modified by adding an unwanted
item.

A great solution would be for the `add_item_to_cart` to always clone the `cart`,
edit it, and return the clone. This ensures that no other methods that are
holding onto a reference of the shopping cart will be affected by any changes.

Two caveats to mention to this approach:
  1. There might be cases where you actually want to modify the input object,
but when you adopt this programming practice you will find that those cases
are pretty rare. Most things can be refactored to have no side effects!

  2. Python comes built in the with a [copy](https://docs.python.org/3/library/copy.html) 
  module which allows for copying and deep copying objects.

  3. Cloning big objects can be expensive in terms of performance. So if you find that you're doing it
  often, you may want to consider implementing the `__copy__` and `__deepcopy__` methods for improved
  performance. More info [here](https://pymotw.com/3/copy/)

**Bad:**
```python
from datetime import datetime

def add_item_to_cart(cart: list, item):
  cart.append({"item": item, "time": datetime.now()})
```

**Good:**
```python
from datetime import datetime

def add_item_to_cart(cart, item):
    cart_copy = cart[:]
    cart_copy.append({"item": item, "time": datetime.now()})
    return cart_copy
```


**[⬆ back to top](#table-of-contents)**

## **Objects and Data Structures**

*Coming soon*

**[⬆ back to top](#table-of-contents)**

## **Classes**

### **Single Responsibility Principle (SRP)**
### **Open/Closed Principle (OCP)**
### **Liskov Substitution Principle (LSP)**
### **Interface Segregation Principle (ISP)**
### **Dependency Inversion Principle (DIP)**

*Coming soon*

**[⬆ back to top](#table-of-contents)**

## **Don"t repeat yourself (DRY)**

*Coming soon*

**[⬆ back to top](#table-of-contents)**

## Miscallenous advice

### Switch statements

**Problem:**
- They often do more than one thing
- They are often repeated in multiple places

**Solution**
- Bury them in an abstract base class or function 
