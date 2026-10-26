# 🎯 Python for Interviews — Complete Guide (Basic → Advanced)

> **Everything you need for the Python interview round**
> Deep understanding, not just syntax — the *why*, the internals, and the gotchas interviewers love
> Concepts → mechanism → common follow-ups → quick reference

---

## 📑 CONTENTS

**FUNDAMENTALS**
1. Python Characteristics
2. Data Types & Mutability ⭐
3. Variables, References & Memory Model ⭐

**DATA STRUCTURES (deep)**
4. Lists
5. Tuples
6. Dictionaries ⭐⭐
7. Sets
8. Strings
9. Time Complexity of Operations ⭐

**FUNCTIONS**
10. Functions, *args / **kwargs ⭐
11. Lambda, map / filter / reduce
12. Comprehensions ⭐
13. Mutable Default Argument Gotcha ⭐⭐

**ITERATORS & GENERATORS**
14. Iterators ⭐
15. Generators ⭐⭐
16. Generator Expressions

**ADVANCED FUNCTIONS**
17. Closures ⭐
18. Decorators ⭐⭐
19. Context Managers ⭐

**OOP (deep)**
20. Classes & self ⭐
21. Inheritance & MRO ⭐⭐
22. Dunder / Magic Methods ⭐⭐
23. Properties & Encapsulation
24. Instance vs Class vs Static Methods ⭐
25. Dataclasses, Abstract Classes

**MEMORY & INTERNALS**
26. Memory Management & GC ⭐
27. The GIL ⭐⭐⭐
28. is vs == & Interning ⭐
29. Shallow vs Deep Copy ⭐

**CONCURRENCY**
30. Threading vs Multiprocessing vs Asyncio ⭐⭐⭐
31. Asyncio Deep ⭐

**ERRORS, MODULES, STDLIB, MODERN**
32. Exceptions ⭐
33. Modules & Packages
34. Standard Library (collections/itertools/functools) ⭐
35. Type Hints & Modern Python ⭐

**INTERVIEW SECTION**
36. Gotchas & Top Q&A + Quick Reference ⭐⭐

---

# 1️⃣ PYTHON CHARACTERISTICS ⭐

## What is Python?
High-level, **interpreted**, **dynamically-typed**, garbage-collected, general-purpose language. Multi-paradigm (OOP, functional, procedural).

## Key properties (common opening question)
- **Interpreted**: executed line by line by the interpreter (CPython compiles to **bytecode** → runs on the Python Virtual Machine). Not machine code directly.
- **Dynamically typed**: variable types are checked at **runtime**, not declared. A name can rebind to any type.
- **Strongly typed**: no implicit weird coercions (`"2" + 2` raises an error, unlike JS).
- **Garbage collected**: automatic memory management (reference counting + cycle collector).

## CPython vs others
- **CPython**: the reference implementation (C). Has the GIL.
- **PyPy**: JIT-compiled, faster. **Jython** (JVM), **IronPython** (.NET).

## Compiled vs Interpreted nuance ⭐
Python source → **bytecode** (`.pyc`) → interpreted by the PVM. So it's "compiled to bytecode, then interpreted." Not fully compiled to native like C.

---

# 2️⃣ DATA TYPES & MUTABILITY ⭐⭐

## Built-in types
| Category | Types |
|----------|-------|
| Numeric | `int`, `float`, `complex`, `bool` |
| Sequence | `list`, `tuple`, `range`, `str` |
| Mapping | `dict` |
| Set | `set`, `frozenset` |
| Binary | `bytes`, `bytearray`, `memoryview` |
| None | `NoneType` |

## Mutable vs Immutable ⭐⭐ (critical — asked constantly)
| Mutable (can change in place) | Immutable (cannot change) |
|-------------------------------|---------------------------|
| `list`, `dict`, `set`, `bytearray` | `int`, `float`, `bool`, `str`, `tuple`, `frozenset`, `bytes` |

- **Immutable** doesn't mean "constant" — it means the object can't be changed; you create a new object instead. `x = 5; x += 1` creates a **new** int object and rebinds `x`.
- **Why it matters**: immutables are **hashable** → can be dict keys / set elements. Mutables generally can't.
- **Gotcha**: a tuple is immutable, but if it contains a mutable object, that inner object can still change: `t = ([1,2], 3); t[0].append(4)` works.

## Why int/str are immutable
Enables **caching/interning** (small int cache, string interning), safe use as dict keys, and thread-safety for simple values.

---

# 3️⃣ VARIABLES, REFERENCES & MEMORY MODEL ⭐

## Everything is an object; variables are names (references)
A variable is a **name bound to an object**, not a box holding a value. Assignment binds a name to an object; it doesn't copy.

```python
a = [1, 2, 3]
b = a          # b references the SAME list
b.append(4)
print(a)       # [1, 2, 3, 4]  — both see the change
```

## Pass by "object reference" ⭐⭐ (classic question)
Python is neither pure pass-by-value nor pass-by-reference — it's **"pass by object reference"** (a.k.a. "call by sharing"):
- The function receives a **reference to the same object**.
- **Mutable** arg → changes inside the function are visible outside (if mutated in place).
- **Immutable** arg → rebinding inside the function doesn't affect the caller (you just rebind the local name).

```python
def f(lst, x):
    lst.append(1)   # mutates caller's list (visible)
    x += 1          # rebinds local only (NOT visible)
```

## id() and type()
- `id(obj)` → the object's identity (memory address in CPython).
- `type(obj)` → its type.

---

# 4️⃣ LISTS ⭐

Ordered, mutable, allows duplicates, heterogeneous.

## Internals
A list is a **dynamic array** of pointers to objects. It **over-allocates** capacity so `append` is **amortized O(1)** (occasionally resizes + copies).

## Key operations & complexity
| Operation | Complexity |
|-----------|------------|
| Index access `lst[i]` | O(1) |
| Append | O(1) amortized |
| Insert/delete at position | O(n) (shifts elements) |
| Search `x in lst` | O(n) |
| Sort | O(n log n) |

## Useful methods
`append`, `extend`, `insert`, `remove`, `pop`, `index`, `count`, `sort`, `reverse`.
- `sort()` sorts in place (returns None); `sorted()` returns a new list.
- **Slicing**: `lst[start:stop:step]` → returns a **new** list (shallow copy). `lst[::-1]` reverses.

## Copy gotcha
`b = a` → same object. `b = a[:]` or `a.copy()` → shallow copy (new list, same inner objects).

---

# 5️⃣ TUPLES ⭐

Ordered, **immutable**, allows duplicates.

## List vs Tuple ⭐ (common question)
| List | Tuple |
|------|-------|
| Mutable | Immutable |
| `[]` | `()` |
| Slower, more memory | Faster, less memory |
| Not hashable | Hashable (if elements are) → dict key |
| For homogeneous collections | Often heterogeneous records |

## Why use tuples?
- **Hashable** → usable as dict keys / set elements.
- **Faster** + less memory than lists.
- Signals "this shouldn't change" (safety).
- **Tuple unpacking**: `a, b = 1, 2`; swap: `a, b = b, a` (no temp var).
- Single-element tuple needs a comma: `(1,)` not `(1)`.

---

# 6️⃣ DICTIONARIES ⭐⭐

Key-value mapping; keys unique + **hashable**; values any type.

## Internals — hash table ⭐⭐ (deep follow-up)
A dict is a **hash table**. `hash(key)` → bucket index → store/lookup. This gives **O(1) average** lookup/insert/delete. Worst case O(n) with many hash collisions (rare).
- Keys must be **immutable/hashable** (str, int, tuple) — because a mutable key's hash could change, breaking lookups.

## Ordering ⭐ (version-specific — know this)
- **Python 3.7+**: dicts maintain **insertion order** (guaranteed by the language spec; was an implementation detail in 3.6).
- Before 3.7: unordered. Use `collections.OrderedDict` historically.

## Key operations & complexity
| Operation | Complexity |
|-----------|------------|
| `d[key]` get/set/delete | O(1) average |
| `key in d` | O(1) average |
| Iterate | O(n) |

## Useful methods / patterns
- `d.get(key, default)` — safe access (no KeyError).
- `d.setdefault(key, default)`, `d.pop(key)`, `d.keys()/.values()/.items()`.
- `collections.defaultdict(list)` — auto-creates missing keys.
- `collections.Counter` — counts hashable items.
- **Merge** (3.9+): `d1 | d2`. **Dict comprehension**: `{k: v for k, v in pairs}`.

---

# 7️⃣ SETS ⭐

Unordered, mutable, **unique** elements, hashable members. Backed by a **hash table** → O(1) membership.

## Use cases
- **Deduplication**: `set(my_list)`.
- **Fast membership**: `x in my_set` is O(1) vs O(n) for a list.
- **Set algebra**: union `|`, intersection `&`, difference `-`, symmetric difference `^`.

## frozenset
Immutable set → hashable → can be a dict key or an element of another set.

---

# 8️⃣ STRINGS ⭐

Immutable sequence of Unicode characters.

## Key points
- **Immutable** → every "modification" creates a new string. Concatenating in a loop with `+` is O(n²) → use `"".join(list_of_strings)` (O(n)).
- **f-strings** (3.6+): `f"{name} is {age}"` — fastest, most readable formatting.
- Common methods: `split`, `join`, `strip`, `replace`, `find`, `startswith`, `upper/lower`, `format`.
- Slicing works like lists: `s[::-1]` reverses.

## String interning ⭐
CPython **interns** some strings (identifiers, small literals) → identical strings may share one object (`a is b` can be True). Don't rely on `is` for string equality — use `==`.

---

# 9️⃣ TIME COMPLEXITY OF OPERATIONS ⭐ (interviewers test this)

| Structure | Access | Search | Insert | Delete |
|-----------|--------|--------|--------|--------|
| **list** | O(1) | O(n) | O(n)* | O(n) |
| **dict** | O(1) avg | O(1) avg | O(1) avg | O(1) avg |
| **set** | — | O(1) avg | O(1) avg | O(1) avg |
| **tuple** | O(1) | O(n) | immutable | immutable |

*list append is O(1) amortized; insert at arbitrary position is O(n).

**Key takeaway**: use **dict/set for membership & lookups** (O(1)), lists for ordered sequences. "Is X in a big collection?" → set, not list.

---

# 🔟 FUNCTIONS, *args / **kwargs ⭐

## Basics
Functions are **first-class objects** — can be passed around, returned, stored in variables/data structures.

## *args and **kwargs ⭐⭐
- `*args` → collects extra **positional** args into a **tuple**.
- `**kwargs` → collects extra **keyword** args into a **dict**.
```python
def f(a, *args, **kwargs):
    # args is a tuple, kwargs is a dict
```
- Unpacking: `f(*my_list, **my_dict)` spreads them into arguments.

## Argument types
Positional, keyword, default, variadic (`*args`/`**kwargs`), keyword-only (after `*`), positional-only (before `/`, 3.8+).

## Scope & LEGB rule ⭐
Name resolution order: **Local → Enclosing → Global → Built-in**. Use `global` / `nonlocal` to rebind outer names.

---

# 1️⃣1️⃣ LAMBDA, map / filter / reduce

## Lambda
Anonymous single-expression function: `square = lambda x: x**2`. Use for short throwaway functions (e.g., `sort(key=lambda x: x[1])`).

## map / filter / reduce
- `map(fn, iterable)` → applies fn to each item (lazy iterator).
- `filter(fn, iterable)` → keeps items where fn is truthy (lazy).
- `reduce(fn, iterable)` → cumulative reduction (from `functools`).
- **Pythonic preference**: comprehensions are usually clearer than map/filter.

---

# 1️⃣2️⃣ COMPREHENSIONS ⭐

Concise construction of lists/dicts/sets.
```python
[x**2 for x in range(10) if x % 2 == 0]        # list
{x: x**2 for x in range(5)}                    # dict
{x % 3 for x in range(10)}                     # set
(x**2 for x in range(10))                      # generator (lazy!)
```
- More **readable and faster** than equivalent loops (optimized in C).
- **Don't overdo it** — nested/complex comprehensions hurt readability.

---

# 1️⃣3️⃣ MUTABLE DEFAULT ARGUMENT GOTCHA ⭐⭐ (famous trap)

```python
def add(item, lst=[]):     # BUG
    lst.append(item)
    return lst

add(1)   # [1]
add(2)   # [1, 2]  ← same list persists across calls!
```

**Why**: default arguments are evaluated **once**, at function definition — not per call. The same list object is reused.
**Fix**:
```python
def add(item, lst=None):
    if lst is None:
        lst = []
    lst.append(item)
    return lst
```

---

# 1️⃣4️⃣ ITERATORS ⭐

## Iterable vs Iterator ⭐ (common distinction)
- **Iterable**: has `__iter__()` — can be looped over (list, dict, str).
- **Iterator**: has `__next__()` (and `__iter__()`) — produces values one at a time, remembers its position. `iter(iterable)` returns an iterator.
```python
it = iter([1, 2, 3])
next(it)  # 1
next(it)  # 2  → StopIteration when exhausted
```
A `for` loop internally calls `iter()` then `next()` until `StopIteration`.

---

# 1️⃣5️⃣ GENERATORS ⭐⭐ (favorite topic)

Functions that **yield** values lazily, one at a time, pausing and resuming state.

```python
def gen():
    yield 1
    yield 2
    yield 3
```

## yield vs return ⭐
- `return` ends the function and returns once.
- `yield` **pauses** the function, returns a value, and **resumes** from there on the next `next()` call — preserving local state.

## Why generators matter ⭐⭐ (the "why")
- **Memory efficient**: produce items on the fly instead of building a full list in memory. Can represent **infinite** sequences.
```python
def infinite():
    n = 0
    while True:
        yield n
        n += 1
```
- Process huge files/streams lazily: `for line in file:` reads line by line.
- A generator is an **iterator** (implements `__next__`).

## Generator expression
`(x**2 for x in range(1000000))` — lazy, memory-efficient alternative to a list comprehension. Use when you only iterate once.

---

# 1️⃣6️⃣ CLOSURES ⭐

A **closure** = a nested function that **remembers variables from its enclosing scope**, even after the outer function has returned.

```python
def multiplier(n):
    def multiply(x):
        return x * n      # n is "captured" from the enclosing scope
    return multiply

double = multiplier(2)
double(5)   # 10  — remembers n=2
```

## Why/where
- Data hiding, function factories, and the basis of **decorators**.
- Use `nonlocal` to modify a captured variable.

---

# 1️⃣7️⃣ DECORATORS ⭐⭐ (very common, go deep)

A decorator is a **callable that takes a function and returns a modified function** — adds behavior without changing the original's code. Built on closures.

```python
def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        print(f"{func.__name__}: {time.time()-start:.4f}s")
        return result
    return wrapper

@timer
def slow(): ...
# @timer is sugar for: slow = timer(slow)
```

## Key points ⭐
- `*args, **kwargs` in the wrapper → works for any function signature.
- Use `@functools.wraps(func)` on the wrapper to **preserve the original's name/docstring/metadata** (otherwise it becomes `wrapper`).
- **Decorators with arguments** need an extra layer (a decorator factory returning a decorator).
- Common real uses: logging, timing, caching (`@functools.lru_cache`), authentication, retries, FastAPI/Flask routes, `@property`, `@staticmethod`.

---

# 1️⃣8️⃣ CONTEXT MANAGERS ⭐

Manage setup/teardown around a block — guarantee cleanup (files, locks, connections) even on exceptions.

```python
with open("f.txt") as f:
    data = f.read()
# file is automatically closed, even if an error occurs
```

## How it works ⭐
The `with` statement calls `__enter__()` on entry and `__exit__()` on exit (always, even on exception). Implement both to make a class a context manager.

## The easy way — contextlib
```python
from contextlib import contextmanager
@contextmanager
def managed():
    print("setup")        # __enter__
    yield resource
    print("teardown")     # __exit__
```

---

# 1️⃣9️⃣ CLASSES & self ⭐

```python
class Dog:
    species = "Canis"             # class attribute (shared)
    def __init__(self, name):     # constructor
        self.name = name          # instance attribute
    def bark(self):               # instance method
        return f"{self.name} barks"
```

## self ⭐
`self` is the **instance** passed automatically as the first argument to instance methods. It's how methods access instance data. (Explicit by convention — it's not a keyword.)

## __init__ vs __new__ ⭐
- `__new__`: **creates** the instance (rarely overridden — used for immutables, singletons, metaclasses).
- `__init__`: **initializes** the already-created instance. `__new__` runs first, then `__init__`.

## Class vs instance attributes
Class attributes are shared across all instances; instance attributes are per-object. **Gotcha**: a mutable class attribute is shared — mutating it affects all instances.

---

# 2️⃣0️⃣ INHERITANCE & MRO ⭐⭐

## Inheritance
```python
class Puppy(Dog):
    def bark(self):
        return super().bark() + " (cutely)"   # super() calls parent
```

## Types
Single, multiple (multiple parents), multilevel, hierarchical.

## MRO (Method Resolution Order) ⭐⭐ (deep follow-up)
With multiple inheritance, Python determines which method to call via the **MRO**, computed by the **C3 linearization** algorithm. It defines a consistent, predictable order to search base classes.
- Check with `ClassName.__mro__` or `ClassName.mro()`.
- `super()` follows the MRO (not just "the parent") — important in diamond inheritance to avoid calling a shared ancestor twice.

## The Diamond Problem
When two parents share a common base, C3 linearization ensures each class appears once in the MRO and `super()` traverses them in order → no duplicate calls.

---

# 2️⃣1️⃣ DUNDER / MAGIC METHODS ⭐⭐

Special methods (double underscore) that define how objects behave with built-in operations — "operator overloading" and the **data model**.

| Method | Enables |
|--------|---------|
| `__init__` | Construction |
| `__str__` / `__repr__` | `str()` (user) / `repr()` (debug) |
| `__len__` | `len(obj)` |
| `__getitem__` / `__setitem__` | Indexing `obj[i]` |
| `__iter__` / `__next__` | Iteration |
| `__eq__`, `__lt__`, ... | `==`, `<` comparisons |
| `__add__`, `__mul__`, ... | `+`, `*` operators |
| `__call__` | Make instance callable `obj()` |
| `__enter__` / `__exit__` | Context manager |
| `__hash__` | Hashability (dict keys) |

## __str__ vs __repr__ ⭐
- `__repr__`: unambiguous, for **developers/debugging** (ideally recreates the object). Fallback for `__str__`.
- `__str__`: readable, for **end users** (`print()`).

---

# 2️⃣2️⃣ PROPERTIES & ENCAPSULATION

## Access conventions ⭐ (Python has no true "private")
- `name` — public.
- `_name` — "protected" by **convention** (internal use; not enforced).
- `__name` — **name mangling** → becomes `_ClassName__name` (avoids subclass clashes; not truly private, just harder to reach).

## @property ⭐
Turn a method into a managed attribute — add logic to getters/setters while keeping clean `obj.attr` syntax.
```python
class Circle:
    def __init__(self, r): self._r = r
    @property
    def area(self):            # access as circle.area (no parens)
        return 3.14159 * self._r**2
    @property
    def r(self): return self._r
    @r.setter
    def r(self, value):
        if value < 0: raise ValueError("negative radius")
        self._r = value
```

---

# 2️⃣3️⃣ INSTANCE vs CLASS vs STATIC METHODS ⭐

```python
class C:
    def instance_method(self): ...        # takes self (the instance)
    @classmethod
    def class_method(cls): ...            # takes cls (the class)
    @staticmethod
    def static_method(): ...              # takes nothing special
```

| Method | First arg | Can access | Use |
|--------|-----------|------------|-----|
| **Instance** | `self` | Instance + class state | Normal methods |
| **Class** | `cls` | Class state only | **Factory methods**, alternative constructors |
| **Static** | none | Neither (namespaced only) | Utility functions related to the class |

Classic class-method use: alternative constructors, e.g., `datetime.fromtimestamp()`.

---

# 2️⃣4️⃣ DATACLASSES & ABSTRACT CLASSES

## @dataclass (3.7+) ⭐
Auto-generates `__init__`, `__repr__`, `__eq__`, etc. from annotations — removes boilerplate.
```python
from dataclasses import dataclass
@dataclass
class Point:
    x: int
    y: int = 0      # default
# p = Point(1, 2); print(p) → Point(x=1, y=2); equality works
```

## Abstract Base Classes (ABC) ⭐
Define an interface; can't be instantiated; force subclasses to implement abstract methods.
```python
from abc import ABC, abstractmethod
class Shape(ABC):
    @abstractmethod
    def area(self): ...
# Shape() → TypeError; subclass must implement area()
```

---

# 2️⃣5️⃣ MEMORY MANAGEMENT & GC ⭐

## Reference counting (primary) ⭐
Every object tracks a **reference count**. When it drops to **0** (no references), the object is **immediately deallocated**.
```python
a = []       # refcount 1
b = a        # refcount 2
del a        # refcount 1
del b        # refcount 0 → freed
```

## Generational garbage collector (for cycles) ⭐
Reference counting **can't free reference cycles** (A→B→A). A separate **cyclic GC** periodically detects and collects unreachable cycles. It's **generational** (gen 0/1/2) — newer objects checked more often (most objects die young).

## Other details
- **Memory pools**: CPython uses `pymalloc` for small objects (efficiency).
- `del` removes a **name/reference**, not necessarily the object (only frees it if refcount hits 0).
- `gc` module lets you interact with the cycle collector.

---

# 2️⃣6️⃣ THE GIL ⭐⭐⭐ (near-guaranteed question)

## What is the GIL?
The **Global Interpreter Lock** is a mutex in **CPython** that allows **only one thread to execute Python bytecode at a time**, even on multi-core CPUs.

## Why does it exist? ⭐
To make **memory management (reference counting) thread-safe** simply and to simplify C-extension integration. Without it, every refcount update would need locking.

## The consequence ⭐⭐
- **CPU-bound** multithreading gets **no speedup** (threads can't run Python code in parallel) — sometimes even slower (lock contention).
- **I/O-bound** multithreading **does** help — the GIL is **released during I/O waits** (file, network, sleep), so other threads run.

## How to achieve true parallelism ⭐
- **`multiprocessing`**: separate processes, each with its **own interpreter + GIL** → true parallelism on multiple cores (but higher memory + IPC overhead).
- **C extensions / NumPy**: release the GIL during heavy native computation.
- **asyncio**: concurrency (not parallelism) for I/O-bound work in a single thread.
- **Modern note**: Python 3.13 introduced an **experimental free-threaded (no-GIL) build** — know it exists, but the GIL is still the default/standard answer.

## The crisp interview answer
"The GIL means only one thread runs Python bytecode at a time in CPython. So use **threading for I/O-bound** tasks (GIL released on I/O) and **multiprocessing for CPU-bound** tasks (bypasses the GIL with separate processes)."

---

# 2️⃣7️⃣ is vs == & INTERNING ⭐

## == vs is ⭐⭐ (classic)
- `==` → **value equality** (calls `__eq__`).
- `is` → **identity** (same object in memory — same `id()`).

```python
a = [1,2,3]; b = [1,2,3]
a == b   # True  (same values)
a is b   # False (different objects)
```

## When to use `is`
Only for **singletons**: `if x is None`, `is True`, `is False`. Never for value comparison.

## Interning gotcha ⭐
Small integers (**−5 to 256**) and some strings are **cached/interned** → `is` may surprisingly return True:
```python
a = 256; b = 256; a is b    # True (cached)
a = 257; b = 257; a is b    # False (not cached) — CPython detail
```
**Lesson**: don't use `is` to compare numbers/strings — use `==`.

---

# 2️⃣8️⃣ SHALLOW vs DEEP COPY ⭐

```python
import copy
shallow = copy.copy(obj)       # new outer object, shares inner objects
deep    = copy.deepcopy(obj)   # fully independent recursive copy
```

## The difference ⭐
- **Shallow copy**: copies the outer container but **inner objects are shared** (references). Mutating a nested object affects both.
- **Deep copy**: recursively copies everything → fully independent.

```python
a = [[1, 2], [3, 4]]
b = copy.copy(a)
b[0].append(99)     # affects a too! a = [[1,2,99],[3,4]]
c = copy.deepcopy(a)
c[0].append(0)      # a unaffected
```
- `a[:]`, `a.copy()`, `dict(d)`, `list(l)` → all **shallow**.

---

# 2️⃣9️⃣ THREADING vs MULTIPROCESSING vs ASYNCIO ⭐⭐⭐

| | Threading | Multiprocessing | Asyncio |
|--|-----------|-----------------|---------|
| Parallelism | No (GIL) | **Yes** (separate processes) | No (single thread) |
| Best for | **I/O-bound** | **CPU-bound** | **I/O-bound, high concurrency** |
| Memory | Shared (light) | Separate (heavy) | Shared (lightest) |
| Overhead | Low | High (process spawn + IPC) | Lowest |
| Switching | OS preemptive | OS | Cooperative (`await`) |

## The decision rule ⭐⭐
- **CPU-bound** (computation, number crunching): **multiprocessing** (bypass GIL).
- **I/O-bound, few tasks** (file/network/DB): **threading**.
- **I/O-bound, thousands of concurrent tasks** (web servers, API calls, scraping): **asyncio** (most scalable, lowest overhead).

## Concurrency vs Parallelism ⭐
- **Concurrency**: managing many tasks that make progress by interleaving (one at a time, switching) — asyncio, threading.
- **Parallelism**: tasks literally running **simultaneously** on multiple cores — multiprocessing.

---

# 3️⃣0️⃣ ASYNCIO DEEP ⭐

Single-threaded **cooperative** concurrency via an **event loop**.

```python
import asyncio
async def fetch(url):
    await asyncio.sleep(1)      # non-blocking wait; yields control
    return f"done {url}"

async def main():
    results = await asyncio.gather(fetch("a"), fetch("b"))  # concurrent
```

## How it works ⭐
- `async def` defines a **coroutine**. `await` **suspends** it, yielding control back to the event loop so other coroutines run while it waits (e.g., on I/O).
- The **event loop** schedules coroutines; when one awaits I/O, another runs. All on **one thread** → no GIL contention, no locking for shared state (mostly).
- **Key rule**: never call a **blocking** function (e.g., `time.sleep`, blocking DB calls) inside a coroutine — it blocks the whole loop. Use async equivalents (`asyncio.sleep`, async DB drivers).

## When to use
Thousands of concurrent I/O operations (API gateways, web servers like FastAPI, web scraping, chat). **FastAPI** (your stack) is built on asyncio.

---

# 3️⃣1️⃣ EXCEPTIONS ⭐

```python
try:
    risky()
except ValueError as e:
    handle(e)
except (KeyError, TypeError):
    ...
else:
    # runs only if NO exception
    ...
finally:
    # ALWAYS runs (cleanup)
    ...
```

## Key points ⭐
- **`else`**: runs only if no exception was raised in `try`.
- **`finally`**: always runs — cleanup (closing resources). Runs even if `return`/`break` in try.
- **`raise`**: throw an exception; `raise ... from e` chains causes.
- **Custom exceptions**: subclass `Exception`.
- **EAFP vs LBYL** ⭐: Python prefers **EAFP** ("Easier to Ask Forgiveness than Permission" — try/except) over **LBYL** ("Look Before You Leap" — check first). E.g., just `try: d[key]` rather than `if key in d`.
- Catch **specific** exceptions, not bare `except:` (which hides bugs and catches `KeyboardInterrupt`).

---

# 3️⃣2️⃣ MODULES & PACKAGES

- **Module**: a `.py` file. **Package**: a directory of modules (historically with `__init__.py`).
- `import x`, `from x import y`, `import x as z`.
- `if __name__ == "__main__":` ⭐ — code runs only when the file is executed directly, not when imported. (`__name__` is `"__main__"` for the entry script, the module name when imported.)
- **Virtual environments** (`venv`): isolate project dependencies. `pip` installs packages.

---

# 3️⃣3️⃣ STANDARD LIBRARY HIGHLIGHTS ⭐

## collections ⭐
- `Counter` — count hashable items (`Counter(list).most_common(3)`).
- `defaultdict` — dict with default factory (no KeyError).
- `OrderedDict` — ordered dict (less needed since 3.7).
- `namedtuple` — lightweight immutable record with named fields.
- `deque` — **double-ended queue**, O(1) appends/pops **both ends** (use for queues/stacks; list is O(n) at the front).

## itertools ⭐
Lazy iterator building blocks: `chain`, `combinations`, `permutations`, `product`, `groupby`, `count`, `cycle`, `islice`. Memory-efficient.

## functools ⭐
- `lru_cache` / `cache` — **memoization** decorator (huge for recursion/repeated calls).
- `reduce`, `partial` (fix some args), `wraps` (for decorators).

## Other staples
`os`, `sys`, `json`, `re` (regex), `datetime`, `math`, `random`, `heapq` (priority queue), `bisect` (binary search on sorted lists).

---

# 3️⃣4️⃣ TYPE HINTS & MODERN PYTHON ⭐

## Type hints (3.5+)
```python
def greet(name: str, times: int = 1) -> str: ...
from typing import Optional, Union, List, Dict
x: Optional[int] = None          # int or None
items: list[str] = []            # 3.9+ builtin generics
```
- **Not enforced at runtime** — they're for readability, IDEs, and static checkers (**mypy**, **pyright**). Important in production/FastAPI code (FastAPI uses them for validation via Pydantic).

## Modern features to know ⭐
- **f-strings** (3.6): `f"{x=}"` (3.8) prints `x=value`.
- **Walrus operator `:=`** (3.8): assign within an expression: `if (n := len(a)) > 10:`.
- **Positional-only `/` and keyword-only `*`** params.
- **Structural pattern matching `match`/`case`** (3.10): switch-like on structure.
- **Union syntax `X | Y`** (3.10) instead of `Union`.

---

# 3️⃣5️⃣ GOTCHAS & TOP Q&A + QUICK REFERENCE ⭐⭐

## Classic gotchas interviewers use

**1. Mutable default argument** → reused across calls (use `None` sentinel).

**2. Late binding closures** →
```python
funcs = [lambda: i for i in range(3)]
[f() for f in funcs]   # [2, 2, 2] not [0,1,2]!
```
Closures capture the **variable**, not its value at creation. Fix: `lambda i=i: i` (bind as default).

**3. `is` vs `==`** → `is` for identity/None only; `==` for values. Int caching (−5..256) causes surprises.

**4. Modifying a list while iterating** → skips elements/errors. Iterate over a copy or build a new list.

**5. Shallow copy of nested structures** → inner objects shared. Use `deepcopy`.

**6. `a = b = []`** → both names point to the **same** list.

**7. Integer/float precision** → `0.1 + 0.2 != 0.3` (floating point). Use `math.isclose` or `decimal`.

**8. Tuple with one element** → needs a comma: `(1,)`.

## Rapid-fire Q&A

**Q: Is Python compiled or interpreted?**
Compiled to bytecode, then interpreted by the PVM (CPython).

**Q: Mutable vs immutable types?**
Mutable: list, dict, set. Immutable: int, float, str, tuple, frozenset. Immutables are hashable.

**Q: How is Python memory managed?**
Reference counting (frees at refcount 0) + a generational cyclic GC for reference cycles.

**Q: What is the GIL and its implication?**
A CPython mutex allowing one thread to run bytecode at a time. Threading helps I/O-bound (GIL released on I/O); multiprocessing for CPU-bound (bypasses GIL).

**Q: List vs tuple?**
List mutable, tuple immutable (faster, hashable, usable as dict key).

**Q: `==` vs `is`?**
`==` value equality; `is` identity (same object). Use `is` only for None/singletons.

**Q: Shallow vs deep copy?**
Shallow copies the outer object, shares inner refs; deep copies recursively (independent).

**Q: What are decorators?**
Callables that wrap a function to add behavior without modifying it (built on closures). Use `functools.wraps`.

**Q: Generators vs lists?**
Generators yield lazily (memory-efficient, can be infinite); lists hold everything in memory.

**Q: `*args` / `**kwargs`?**
Collect extra positional args into a tuple / keyword args into a dict.

**Q: Threading vs multiprocessing vs asyncio?**
Threading for I/O-bound (GIL-limited), multiprocessing for CPU-bound (true parallelism), asyncio for massive I/O concurrency (single thread, event loop).

**Q: What is MRO?**
Method Resolution Order — the C3-linearization order Python searches base classes; `super()` follows it.

**Q: `__init__` vs `__new__`?**
`__new__` creates the instance; `__init__` initializes it.

**Q: Instance vs class vs static method?**
Instance takes `self`; class takes `cls` (factories); static takes neither (utility).

**Q: What is a closure?**
A nested function that remembers variables from its enclosing scope after the outer function returns.

**Q: `@property`?**
Exposes a method as a managed attribute (getter/setter logic with clean attribute syntax).

**Q: Why use `with` (context manager)?**
Guarantees setup/teardown (e.g., closing files) even on exceptions, via `__enter__`/`__exit__`.

**Q: EAFP vs LBYL?**
Python prefers EAFP (try/except) over checking first (LBYL).

**Q: How to make membership tests fast?**
Use a set/dict (O(1)) instead of a list (O(n)).

## Quick Reference Cheat Sheet ⭐

### Mutable / Immutable
Mutable: list, dict, set, bytearray. Immutable: int, float, bool, str, tuple, frozenset, bytes.

### Complexity
dict/set lookup O(1); list index O(1), search/insert-at-pos O(n); deque ends O(1).

### Memory
Reference counting + generational cyclic GC. del removes a reference.

### GIL
One thread runs bytecode at a time (CPython). I/O-bound → threading; CPU-bound → multiprocessing.

### Concurrency
Threading (I/O, GIL-bound) · Multiprocessing (CPU, parallel) · Asyncio (I/O, event loop, single thread).

### Copy
copy.copy = shallow (shares inner); copy.deepcopy = independent.

### is vs ==
is = identity (None/singletons); == = value.

### OOP
self/cls, __init__ vs __new__, MRO (C3), dunders, @property, @classmethod/@staticmethod, dataclass, ABC.

### Functions
first-class, *args/**kwargs, closures, decorators (@wraps), lambda, comprehensions, generators (yield).

### Stdlib
collections (Counter/defaultdict/deque/namedtuple), itertools, functools (lru_cache/partial), heapq, re, json.

### Gotchas
mutable default arg · late-binding closures · is-caching · mutate-while-iterate · shallow nested copy · float precision.

---

# 🎯 HOW TO PREP WITH THIS

1. **Nail the GIL, mutability, and concurrency choices** (threading vs multiprocessing vs asyncio) — asked in almost every Python interview.
2. **Be able to explain decorators, generators, and closures with the *why*** — these separate juniors from mid/senior.
3. **Know the gotchas cold** — mutable defaults, `is` vs `==`, shallow copy, late-binding closures. Interviewers love these traps.
4. **Tie to your backend/FastAPI work** — asyncio, type hints/Pydantic, context managers are daily tools for you; speak from experience.
5. **Know complexity** of core structures — "use a set for O(1) membership" is a reflex answer they want.

This covers the vast majority of Python interview questions from fundamentals to internals. Combine the mechanisms with your production Python experience and you'll be strong.

**You've got this, bro. 🚀**
