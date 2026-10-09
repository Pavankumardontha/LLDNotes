# Java Collections Framework - Master From Scratch

## Table of Contents

### Prerequisite
- [Understanding Generics in Java](#prerequisite-understanding-generics-in-java)

### Core Concepts
- [Topic 1: Collections Core Interfaces & Concrete Classes](#topic-1-collections-core-interfaces--concrete-classes)
- [Topic 2: Need of Iterators](#topic-2-need-of-iterators)
- [Topic 3: Iterable and Iterator Interface](#topic-3-iterable-and-iterator-interface)
- [Topic 4: Collection Interface](#topic-4-collection-interface)

### Lists
- [Topic 5: Lists — ArrayList, Vector, LinkedList](#topic-5-lists--arraylist-vector-linkedlist)
- [Topic 6: How to Iterate and Access Elements in ArrayList, Vector, and LinkedList](#topic-6-how-to-iterate-and-access-elements-in-arraylist-vector-and-linkedlist)

### Queues & Stacks
- [Topic 7: Queue Interface in Java](#topic-7-queue-interface-in-java)
- [Topic 8: Deque Interface (Double-Ended Queue)](#topic-8-deque-interface-double-ended-queue)
- [Topic 9: Priority Queue](#topic-9-priority-queue)

### Comparable & Comparator
- [Topic 10: The Comparable Interface](#topic-10-the-comparable-interface)
- [Topic 11: The Comparator Interface (Detailed)](#topic-11-the-comparator-interface-detailed)

### Sets
- [Topic 12: The Set Interface & HashSet](#topic-12-the-set-interface--hashset)
- [Topic 13: LinkedHashSet](#topic-13-linkedhashset)
- [Topic 14: Internal Working of HashSet, equals() and hashCode()](#topic-14-internal-working-of-hashset-equals-and-hashcode)
- [Topic 15: SortedSet Interface](#topic-15-sortedset-interface)
- [Topic 16: NavigableSet Interface](#topic-16-navigableset-interface)
- [Topic 17: TreeSet in Java](#topic-17-treeset-in-java)

### Maps
- [Topic 18: The Map Interface, HashMap & Hashtable](#topic-18-the-map-interface-hashmap--hashtable)
- [Topic 19: SortedMap Interface](#topic-19-sortedmap-interface)
- [Topic 20: NavigableMap Interface](#topic-20-navigablemap-interface)
- [Topic 21: TreeMap in Java](#topic-21-treemap-in-java)

### Sorting
- [Topic 22: Sorting an Array and a List](#topic-22-sorting-an-array-and-a-list)

---

## Prerequisite: Understanding Generics in Java

### What is the Problem Without Generics?

Before Java 5, collections stored everything as `Object`. This caused two major issues:

1. **No type safety** — you could accidentally mix types
2. **Manual casting** — you had to cast every time you retrieved an element

```java
// The OLD way (before generics) — dangerous!
import java.util.ArrayList;

public class WithoutGenerics {
    public static void main(String[] args) {
        ArrayList list = new ArrayList();  // raw type, no <Type>

        list.add("Hello");
        list.add(123);        // No error! Mixing String and Integer
        list.add(true);       // No error! Adding Boolean too

        // When you retrieve, you MUST cast — and hope it's correct
        String value = (String) list.get(0);  // works fine
        String oops = (String) list.get(1);   // RUNTIME CRASH! ClassCastException
        // 123 is Integer, not String — but compiler didn't warn you!
    }
}
```

**The compiler couldn't help you.** Errors only showed up when the program ran — sometimes in production!

---

### What are Generics?

Generics allow you to define classes, interfaces, and methods with a **type parameter** — a placeholder that gets replaced with an actual type when you use it.

**Syntax:** `<E>`, `<T>`, `<K, V>` — angle brackets with a letter inside.

**Common naming conventions:**
| Letter | Stands For | Used In |
|--------|-----------|---------|
| `E` | Element | Collections (List\<E\>, Set\<E\>) |
| `T` | Type | General-purpose classes |
| `K` | Key | Maps (Map\<K, V\>) |
| `V` | Value | Maps (Map\<K, V\>) |
| `N` | Number | Numeric types |

---

### Generics with a Simple Example — The Box Class

#### Without Generics:

```java
class Box {
    private Object item;  // can hold ANYTHING

    public void put(Object item) {
        this.item = item;
    }

    public Object get() {
        return item;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Box box = new Box();
        box.put("Hello");

        // MUST cast — compiler doesn't know what's inside
        String value = (String) box.get();
        System.out.println(value);  // Hello

        // Dangerous! No compile-time check
        box.put(42);
        String wrong = (String) box.get();  // CRASH at runtime!
    }
}
```

#### With Generics:

```java
class Box<E> {           // E is a placeholder — decided when creating an object
    private E item;      // item is of type E

    public void put(E item) {   // accepts only type E
        this.item = item;
    }

    public E get() {     // returns type E
        return item;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        // Here E is replaced with String
        Box<String> stringBox = new Box<>();
        stringBox.put("Hello");           // only String allowed
        String value = stringBox.get();   // no casting needed!
        System.out.println(value);        // Hello

        stringBox.put(42);  // COMPILE ERROR! Cannot put Integer in Box<String>

        // Here E is replaced with Integer
        Box<Integer> intBox = new Box<>();
        intBox.put(42);                    // only Integer allowed
        Integer num = intBox.get();        // no casting needed!
        System.out.println(num);           // 42

        // Here E is replaced with Double
        Box<Double> doubleBox = new Box<>();
        doubleBox.put(3.14);
        Double pi = doubleBox.get();
        System.out.println(pi);            // 3.14
    }
}
```

**The same `Box` class works for any type — but once you decide the type, it's locked in and the compiler enforces it.**

---

### How Does `<E>` Actually Work?

Think of `<E>` like a **fill-in-the-blank**:

```
Definition:       class Box<___> { ___ item; }

When you write:   Box<String>  → class Box { String item; }
                  Box<Integer> → class Box { Integer item; }
                  Box<Dog>     → class Box { Dog item; }
```

The compiler **replaces** `E` with whatever type you specify in `< >`.

---

### Generics in Interfaces — How Collections Use Them

The Collections Framework uses generics extensively:

```java
public interface Iterable<E> {
    Iterator<E> iterator();
}
```

This means:
- `Iterable<String>` → `iterator()` returns `Iterator<String>` → gives you String objects
- `Iterable<Integer>` → `iterator()` returns `Iterator<Integer>` → gives you Integer objects

#### Full chain example:

```java
public interface Collection<E> extends Iterable<E> {
    boolean add(E e);        // if E=String, this becomes add(String e)
    boolean remove(Object o);
    int size();
}

public interface List<E> extends Collection<E> {
    E get(int index);        // if E=String, this returns String
    E set(int index, E element);
}
```

When you create:
```java
List<String> names = new ArrayList<>();
```

Every `E` in the `List` interface becomes `String`:
- `add(E e)` → `add(String e)` — can only add Strings
- `E get(int index)` → `String get(int index)` — returns String directly
- No casting, no mistakes!

---

### Generics with Multiple Type Parameters

An interface or class can have more than one type parameter:

```java
// Map has TWO type parameters: K for Key, V for Value
public interface Map<K, V> {
    V put(K key, V value);
    V get(Object key);
}
```

```java
// K=String, V=Integer
Map<String, Integer> ages = new HashMap<>();
ages.put("Pavan", 25);     // key is String, value is Integer
ages.put("Raj", 30);
Integer age = ages.get("Pavan");  // returns Integer directly

// K=Integer, V=String
Map<Integer, String> idToName = new HashMap<>();
idToName.put(101, "Alice");   // key is Integer, value is String
idToName.put(102, "Bob");
String name = idToName.get(101);  // returns String directly
```

---

### Generic Class with Two Parameters — Custom Example

```java
class Pair<A, B> {
    private A first;
    private B second;

    public Pair(A first, B second) {
        this.first = first;
        this.second = second;
    }

    public A getFirst() { return first; }
    public B getSecond() { return second; }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Pair<String, Integer> person = new Pair<>("Pavan", 25);
        String name = person.getFirst();    // "Pavan" — no casting
        Integer age = person.getSecond();   // 25 — no casting

        Pair<Double, Double> coordinate = new Pair<>(12.5, 78.3);
        Double lat = coordinate.getFirst();
        Double lng = coordinate.getSecond();
    }
}
```

---

### Generic Methods

You can also make individual methods generic (even if the class itself isn't generic):

```java
class Utility {
    // <T> before return type declares a generic method
    public static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.print(element + " ");
        }
        System.out.println();
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Integer[] nums = {1, 2, 3, 4, 5};
        String[] words = {"Hello", "World"};
        Double[] decimals = {1.1, 2.2, 3.3};

        Utility.printArray(nums);      // 1 2 3 4 5
        Utility.printArray(words);     // Hello World
        Utility.printArray(decimals);  // 1.1 2.2 3.3
        // Same method works for any type!
    }
}
```

---

### Important Rule: Generics Only Work with Reference Types

You **cannot** use primitive types with generics:

```java
// INVALID — primitives not allowed
List<int> numbers = new ArrayList<>();      // COMPILE ERROR!
Box<char> letterBox = new Box<>();          // COMPILE ERROR!

// VALID — use wrapper classes instead
List<Integer> numbers = new ArrayList<>();  // OK!
Box<Character> letterBox = new Box<>();     // OK!
```

| Primitive | Wrapper Class |
|-----------|--------------|
| `int` | `Integer` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |
| `float` | `Float` |
| `long` | `Long` |

Java automatically converts between primitives and wrappers (**autoboxing/unboxing**):

```java
List<Integer> numbers = new ArrayList<>();
numbers.add(5);           // autoboxing: int 5 → Integer.valueOf(5)
int value = numbers.get(0);  // unboxing: Integer → int
```

---

### The Diamond Operator `<>`

When you create an object, you don't need to repeat the type on the right side (Java 7+):

```java
// Verbose way (old):
List<String> names = new ArrayList<String>();

// Diamond operator (modern — compiler infers the type):
List<String> names = new ArrayList<>();  // <> is called the diamond operator
```

The compiler looks at the left side (`List<String>`) and infers that `ArrayList<>` must also be `ArrayList<String>`.

---

### Summary — Why Generics Matter for Collections

| Without Generics | With Generics |
|-----------------|---------------|
| `List list = new ArrayList();` | `List<String> list = new ArrayList<>();` |
| `list.add("Hi"); list.add(123);` — no error | `list.add(123);` — COMPILE ERROR |
| `String s = (String) list.get(0);` — manual cast | `String s = list.get(0);` — no cast |
| Bugs found at **runtime** (crash!) | Bugs found at **compile time** (before running) |
| Unsafe, error-prone | Type-safe, clean, reliable |

---

## Topic 1: Collections Core Interfaces & Concrete Classes

### The Collections Framework Hierarchy

```
                    Iterable<E>
                        |
                    Collection<E>
                   /      |       \
              List<E>   Set<E>    Queue<E>
                          |           |
                    SortedSet<E>   Deque<E>
                          |
                    NavigableSet<E>


        Map<K, V>  (separate — NOT under Collection)
            |
        SortedMap<K, V>
            |
        NavigableMap<K, V>
```

---

### 1. Iterable\<E\>

**Purpose:** Makes an object usable in a for-each loop.

**Methods:**
```java
Iterator<E> iterator()
```

**Every collection class implements this** (since Collection extends Iterable).

---

### 2. Collection\<E\>

**Purpose:** Root interface for all collections (except Map). Defines the most basic operations every collection must support.

**Methods:**
```java
int size()
boolean isEmpty()
boolean contains(Object o)
boolean add(E e)
boolean remove(Object o)
boolean containsAll(Collection<?> c)
boolean addAll(Collection<? extends E> c)
boolean removeAll(Collection<?> c)
boolean retainAll(Collection<?> c)
void clear()
Object[] toArray()
Iterator<E> iterator()
```

**No class directly implements just Collection** — they implement its sub-interfaces (List, Set, Queue).

---

### 3. List\<E\> — Ordered, Allows Duplicates, Index-Based

**Purpose:** An ordered collection (sequence). Elements have a position (index) starting from 0. Duplicates are allowed.

**Methods (on top of Collection):**
```java
E get(int index)
E set(int index, E element)
void add(int index, E element)
E remove(int index)
int indexOf(Object o)
int lastIndexOf(Object o)
List<E> subList(int from, int to)
ListIterator<E> listIterator()
```

**Concrete classes implementing List:**

| Class | Internal Structure | Key Characteristics |
|-------|-------------------|---------------------|
| **ArrayList** | Dynamic array (resizable array) | Fast random access by index, slow insert/delete in middle |
| **LinkedList** | Doubly-linked list | Fast insert/delete anywhere, slow random access |
| **Vector** | Dynamic array (synchronized) | Thread-safe but slow, legacy class |
| **Stack** | Extends Vector | LIFO (Last-In-First-Out), legacy class |

---

### 4. Set\<E\> — No Duplicates

**Purpose:** A collection that contains no duplicate elements. Models the mathematical concept of a set.

**Methods:** Same as Collection. No new methods added. The contract is different — `add()` won't add if element already exists.

**Concrete classes implementing Set:**

| Class | Internal Structure | Key Characteristics |
|-------|-------------------|---------------------|
| **HashSet** | HashMap internally | No order guaranteed, fastest (O(1) add/remove/contains) |
| **LinkedHashSet** | HashMap + Linked List | Maintains **insertion order** |
| **TreeSet** | Red-Black Tree | Elements stored in **sorted order** |

---

### 5. SortedSet\<E\> — Sorted, No Duplicates

**Purpose:** A Set that maintains elements in ascending sorted order.

**Methods (on top of Set):**
```java
E first()                              // smallest element
E last()                               // largest element
SortedSet<E> headSet(E toElement)      // elements < toElement
SortedSet<E> tailSet(E fromElement)    // elements >= fromElement
SortedSet<E> subSet(E from, E to)      // elements in range
Comparator<? super E> comparator()     // null if natural order
```

**Concrete class:** `TreeSet`

---

### 6. NavigableSet\<E\> — Sorted + Navigation Methods

**Purpose:** Extends SortedSet with navigation methods to find closest matches.

**Methods (on top of SortedSet):**
```java
E lower(E e)      // greatest element strictly LESS than e
E floor(E e)      // greatest element LESS than or EQUAL to e
E higher(E e)     // smallest element strictly GREATER than e
E ceiling(E e)    // smallest element GREATER than or EQUAL to e
E pollFirst()     // remove and return smallest
E pollLast()      // remove and return largest
NavigableSet<E> descendingSet()  // reverse order view
```

**Concrete class:** `TreeSet`

---

### 7. Queue\<E\> — FIFO Processing

**Purpose:** Holds elements before processing. Typically follows FIFO (First-In, First-Out).

**Methods (on top of Collection):**
```java
// Throws exception if fails:
boolean add(E e)       // insert
E remove()             // remove head
E element()            // peek head

// Returns null/false if fails:
boolean offer(E e)     // insert
E poll()               // remove head
E peek()               // peek head
```

**Why two sets of methods?**

| Operation | Throws Exception | Returns null/false |
|-----------|-----------------|-------------------|
| Insert    | `add(e)`        | `offer(e)`        |
| Remove    | `remove()`      | `poll()`          |
| Examine   | `element()`     | `peek()`          |

**Concrete classes implementing Queue:**

| Class | Key Characteristics |
|-------|---------------------|
| **LinkedList** | Also implements List! Can be used as Queue |
| **PriorityQueue** | Elements dequeued by priority (smallest first by default) |
| **ArrayDeque** | Resizable array-based, faster than LinkedList as Queue |

---

### 8. Deque\<E\> — Double-Ended Queue

**Purpose:** Insert and remove from both ends (front and back). Can work as both Queue (FIFO) and Stack (LIFO).

**Methods (on top of Queue):**
```java
// Head (front) operations:
void addFirst(E e)
E removeFirst()
E getFirst()
boolean offerFirst(E e)
E pollFirst()
E peekFirst()

// Tail (back) operations:
void addLast(E e)
E removeLast()
E getLast()
boolean offerLast(E e)
E pollLast()
E peekLast()

// Stack operations:
void push(E e)    // same as addFirst
E pop()           // same as removeFirst
```

**Concrete classes implementing Deque:**

| Class | Key Characteristics |
|-------|---------------------|
| **ArrayDeque** | Resizable array, fastest Deque/Stack implementation |
| **LinkedList** | Also implements List and Queue |

---

### 9. Map\<K, V\> — Key-Value Pairs (Separate Hierarchy)

**Purpose:** Stores key-value pairs. Each key maps to exactly one value. No duplicate keys (but duplicate values are fine).

**Note:** Map does NOT extend Collection. It's a completely separate hierarchy.

**Methods:**
```java
V put(K key, V value)
V get(Object key)
V remove(Object key)
boolean containsKey(Object key)
boolean containsValue(Object value)
int size()
boolean isEmpty()
void putAll(Map<? extends K, ? extends V> m)
void clear()
Set<K> keySet()
Collection<V> values()
Set<Map.Entry<K, V>> entrySet()
```

**Concrete classes implementing Map:**

| Class | Internal Structure | Key Characteristics |
|-------|-------------------|---------------------|
| **HashMap** | Hash table (array + linked list/tree) | No order, fastest (O(1)), allows one null key |
| **LinkedHashMap** | Hash table + Linked List | Maintains **insertion order** |
| **TreeMap** | Red-Black Tree | Keys stored in **sorted order** |
| **Hashtable** | Hash table (synchronized) | Thread-safe but slow, legacy, NO null keys/values |

---

### 10. SortedMap\<K, V\> — Sorted by Keys

**Purpose:** A Map that maintains keys in ascending sorted order.

**Methods (on top of Map):**
```java
K firstKey()
K lastKey()
SortedMap<K, V> headMap(K toKey)
SortedMap<K, V> tailMap(K fromKey)
SortedMap<K, V> subMap(K fromKey, K toKey)
Comparator<? super K> comparator()
```

**Concrete class:** `TreeMap`

---

### 11. NavigableMap\<K, V\> — Sorted + Navigation

**Purpose:** Extends SortedMap with navigation methods to find closest matches.

**Methods (on top of SortedMap):**
```java
Map.Entry<K,V> lowerEntry(K key)
Map.Entry<K,V> floorEntry(K key)
Map.Entry<K,V> higherEntry(K key)
Map.Entry<K,V> ceilingEntry(K key)
Map.Entry<K,V> pollFirstEntry()
Map.Entry<K,V> pollLastEntry()
NavigableMap<K,V> descendingMap()
```

**Concrete class:** `TreeMap`

---

### Complete Picture — Which Class Implements What?

| Concrete Class | Implements |
|---------------|------------|
| **ArrayList** | List |
| **LinkedList** | List, Queue, Deque |
| **Vector** | List |
| **Stack** | List (extends Vector) |
| **HashSet** | Set |
| **LinkedHashSet** | Set |
| **TreeSet** | Set, SortedSet, NavigableSet |
| **PriorityQueue** | Queue |
| **ArrayDeque** | Queue, Deque |
| **HashMap** | Map |
| **LinkedHashMap** | Map |
| **TreeMap** | Map, SortedMap, NavigableMap |
| **Hashtable** | Map |

---

### The Big Visual Summary

```
Interface              Concrete Classes
─────────────────────────────────────────────────
List<E>            →   ArrayList, LinkedList, Vector, Stack
Set<E>             →   HashSet, LinkedHashSet, TreeSet
Queue<E>           →   LinkedList, PriorityQueue, ArrayDeque
Deque<E>           →   ArrayDeque, LinkedList
Map<K,V>           →   HashMap, LinkedHashMap, TreeMap, Hashtable
```

**Note:** LinkedList is a jack-of-all-trades — it implements List, Queue, AND Deque!

---

## Topic 2: Need of Iterators

### The Problem — How Do You Traverse a Collection?

Different collections store data differently internally:
- **ArrayList** → contiguous array (index works great)
- **LinkedList** → nodes with pointers (index is slow, must walk from start)
- **HashSet** → hash table with buckets (no concept of index at all)
- **TreeSet** → tree structure (no index)

**For an ArrayList — you can use index:**
```java
ArrayList<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("C");

for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));  // works fine — ArrayList supports index
}
```

**For a HashSet — you CANNOT use index:**
```java
HashSet<String> set = new HashSet<>();
set.add("A");
set.add("B");
set.add("C");

// PROBLEM! HashSet has NO get(index) method!
for (int i = 0; i < set.size(); i++) {
    System.out.println(set.get(i));  // COMPILE ERROR! No such method
}
```

---

### What We Need

A single, common mechanism to traverse ANY collection, regardless of its internal structure. The mechanism should:
1. Work the same way for ArrayList, HashSet, LinkedList, TreeSet, etc.
2. Not expose internal details (encapsulation)
3. Let you go through elements one by one

---

### The Solution — Iterators!

An **Iterator** is an object that knows how to traverse a specific collection without exposing how the collection is structured internally.

It works like a cursor/pointer that:
- Starts before the first element
- Can check "is there a next element?" → `hasNext()`
- Can move forward and give you the next element → `next()`

**Same code works for ANY collection:**
```java
// Works for ArrayList
Iterator<String> it1 = arrayList.iterator();
while (it1.hasNext()) System.out.println(it1.next());

// Works for HashSet
Iterator<String> it2 = hashSet.iterator();
while (it2.hasNext()) System.out.println(it2.next());

// Works for TreeSet
Iterator<String> it3 = treeSet.iterator();
while (it3.hasNext()) System.out.println(it3.next());

// Works for LinkedList
Iterator<String> it4 = linkedList.iterator();
while (it4.hasNext()) System.out.println(it4.next());
```

---

### For-each Loop Uses Iterator Internally

```java
for (String s : mySet) {
    System.out.println(s);
}
```

The compiler converts this to:
```java
Iterator<String> it = mySet.iterator();
while (it.hasNext()) {
    String s = it.next();
    System.out.println(s);
}
```

For-each is just syntactic sugar over iterators. But sometimes you need the iterator directly — for example, to **remove elements while iterating**.

---

### Real-World Analogy

Think of a **TV remote with a "Next Channel" button**:
- You don't need to know how channels are stored internally (satellite, cable, streaming)
- You just press "Next" and you get the next channel
- You can check "are there more channels?" before pressing next

The remote is the **Iterator**. The TV's internal channel list is the **Collection**.

---

### Summary — Why Do We Need Iterators?

| Problem | Solution |
|---------|----------|
| Different collections have different internal structures | Iterator provides a unified traversal mechanism |
| Some collections don't support index-based access | Iterator doesn't need indices |
| You don't want to expose internal details | Iterator hides the implementation |
| You want to write generic code that works on any collection | Iterator gives a common interface |

---

## Topic 3: Iterable and Iterator Interface

### The Two Interfaces

There are two separate interfaces that work together:

| Interface | Package | Purpose |
|-----------|---------|---------|
| `Iterable<E>` | `java.lang` | Says "I can give you an iterator" |
| `Iterator<E>` | `java.util` | Says "I can traverse elements one by one" |

---

### Iterator\<E\> Interface

The actual object that does the traversal work.

```java
public interface Iterator<E> {
    boolean hasNext();   // returns true if there are more elements
    E next();           // returns the next element and moves cursor forward
    void remove();      // removes the last element returned by next() [optional]
}
```

**How it works visually:**

```
Elements:    [A]  [B]  [C]  [D]
Cursor:   ^  (starts BEFORE first element)

hasNext() → true
next()    → returns "A", cursor moves forward

Elements:    [A]  [B]  [C]  [D]
Cursor:        ^

hasNext() → true
next()    → returns "B", cursor moves forward

... and so on until hasNext() returns false
```

---

### Iterable\<E\> Interface

Says: "I am a collection that can produce an Iterator."

```java
public interface Iterable<E> {
    Iterator<E> iterator();   // factory method — creates and returns an Iterator
}
```

Any class that implements `Iterable` can be used in a **for-each loop**.

---

### How They Work Together
If we want to call iterator() function on a reference variable pointing to a collection type , the collection type must implement the iterable interface.
```java
ArrayList<String> names = new ArrayList<>();
names.add("Pavan");
names.add("Raj");
names.add("Amit");

// Get an iterator from the collection (Iterable's method)
Iterator<String> it = names.iterator();

// Use the iterator to traverse
while (it.hasNext()) {          // Iterator's method
    String name = it.next();    // Iterator's method
    System.out.println(name);
}
// Output: Pavan, Raj, Amit
```

---

### Creating Your Own Iterable Class

A custom class from scratch to understand the full picture:

```java
import java.util.Iterator;

class NumberRange implements Iterable<Integer> {
    private int start;
    private int end;

    public NumberRange(int start, int end) {
        this.start = start;
        this.end = end;
    }

    @Override
    public Iterator<Integer> iterator() {
        return new NumberRangeIterator();
    }

    // Inner class — the actual Iterator
    private class NumberRangeIterator implements Iterator<Integer> {
        private int current = start;

        @Override
        public boolean hasNext() {
            return current <= end;
        }

        @Override
        public Integer next() {
            return current++;
        }
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        NumberRange range = new NumberRange(1, 5);

        // Because NumberRange implements Iterable, we can use for-each!
        for (int num : range) {
            System.out.println(num);
        }
        // Output: 1, 2, 3, 4, 5

        // Or use the iterator manually:
        Iterator<Integer> it = range.iterator();
        while (it.hasNext()) {
            System.out.println(it.next());
        }
    }
}
```

---

### The remove() Method — Safely Remove While Iterating

A major reason to use iterators directly (instead of for-each) is removing elements during traversal:

```java
ArrayList<Integer> numbers = new ArrayList<>();
numbers.add(1);
numbers.add(2);
numbers.add(3);
numbers.add(4);
numbers.add(5);

// WRONG — ConcurrentModificationException!
for (Integer num : numbers) {
    if (num % 2 == 0) {
        numbers.remove(num);  // CRASH! Cannot modify collection during for-each
    }
}

// CORRECT — use Iterator's remove()
Iterator<Integer> it = numbers.iterator();
while (it.hasNext()) {
    Integer num = it.next();
    if (num % 2 == 0) {
        it.remove();  // safely removes the element returned by last next() call
    }
}
System.out.println(numbers);  // [1, 3, 5]
```

**Why does for-each crash?** Because for-each uses an iterator internally, and if you modify the collection directly (bypassing the iterator), the iterator detects the change and throws `ConcurrentModificationException`. The iterator's own `remove()` is the safe way.

---

### Key Relationship

```
Iterable (the producer)          Iterator (the consumer/traverser)
─────────────────────────        ──────────────────────────────────
"I can give you an iterator"     "I can walk through elements"
Has: iterator() method           Has: hasNext(), next(), remove()
Implemented by: Collections      Returned by: iterator() method
Enables: for-each loop           Does: actual traversal work
```

---

### Summary

| Concept | Iterable\<E\> | Iterator\<E\> |
|---------|---------------|---------------|
| Package | `java.lang` | `java.util` |
| Method(s) | `iterator()` | `hasNext()`, `next()`, `remove()` |
| Role | "I am traversable" | "I do the traversing" |
| Analogy | A bookshelf (has books) | Your finger moving across spines |
| For-each | Enables it | Powers it behind the scenes |

---

## Topic 4: Collection Interface

### What is the Collection Interface?

`Collection<E>` is the **root interface** for List, Set, and Queue. It defines the most fundamental operations that ANY collection (except Map) must support.

```java
public interface Collection<E> extends Iterable<E> {
    // ... methods below
}
```

Since it extends `Iterable<E>`, every Collection can be used in a for-each loop.

---

### All Methods with Explanations and Examples

```java
import java.util.*;

public class CollectionDemo {
    public static void main(String[] args) {
        // Using ArrayList, but these methods work on ANY Collection
        Collection<String> fruits = new ArrayList<>();
```

#### 1. add(E e) — Add a single element

```java
        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Cherry");
        System.out.println(fruits);  // [Apple, Banana, Cherry]
```

Returns `true` if the collection was modified. For Sets, returns `false` if element already exists.

#### 2. size() — Number of elements

```java
        System.out.println(fruits.size());  // 3
```

#### 3. isEmpty() — Check if empty

```java
        System.out.println(fruits.isEmpty());  // false
```

#### 4. contains(Object o) — Check if element exists

```java
        System.out.println(fruits.contains("Apple"));   // true
        System.out.println(fruits.contains("Mango"));   // false
```

#### 5. remove(Object o) — Remove a single element

```java
        fruits.remove("Banana");
        System.out.println(fruits);  // [Apple, Cherry]
```

Removes the **first occurrence** (in Lists). Returns `true` if element was found and removed.

#### 6. addAll(Collection c) — Add all elements from another collection

```java
        Collection<String> moreFruits = new ArrayList<>();
        moreFruits.add("Mango");
        moreFruits.add("Grape");

        fruits.addAll(moreFruits);
        System.out.println(fruits);  // [Apple, Cherry, Mango, Grape]
```

#### 7. containsAll(Collection c) — Check if ALL elements exist

```java
        Collection<String> check = Arrays.asList("Apple", "Mango");
        System.out.println(fruits.containsAll(check));  // true

        Collection<String> check2 = Arrays.asList("Apple", "Papaya");
        System.out.println(fruits.containsAll(check2));  // false (Papaya not present)
```

#### 8. removeAll(Collection c) — Remove all elements that exist in c

```java
        Collection<String> toRemove = Arrays.asList("Apple", "Grape");
        fruits.removeAll(toRemove);
        System.out.println(fruits);  // [Cherry, Mango]
```

#### 9. retainAll(Collection c) — Keep ONLY elements that exist in c (intersection)

```java
        Collection<String> toKeep = Arrays.asList("Cherry", "Banana", "Kiwi");
        fruits.retainAll(toKeep);
        System.out.println(fruits);  // [Cherry]  — only Cherry was in both
```

#### 10. clear() — Remove all elements

```java
        fruits.clear();
        System.out.println(fruits);          // []
        System.out.println(fruits.isEmpty()); // true
```

#### 11. toArray() — Convert to array

```java
        fruits.add("X");
        fruits.add("Y");
        Object[] arr = fruits.toArray();           // returns Object[]
        String[] strArr = fruits.toArray(new String[0]);  // returns typed String[]
```

#### 12. iterator() — Get an Iterator (inherited from Iterable)

```java
        Iterator<String> it = fruits.iterator();
        while (it.hasNext()) {
            System.out.println(it.next());
        }
    }
}
```

---

### Why is Collection Useful as a Parameter Type?

You can write methods that accept **any** collection — this is programming to the interface:

```java
public static void printAll(Collection<String> items) {
    for (String item : items) {
        System.out.println(item);
    }
}

// Works with ANY collection:
printAll(new ArrayList<>(List.of("A", "B")));   // works
printAll(new HashSet<>(Set.of("X", "Y")));      // works
printAll(new LinkedList<>(List.of("1", "2")));  // works
```

You don't care about the specific implementation — just that it's a Collection.

---

## Topic 5: Lists — ArrayList, Vector, LinkedList

### What is a List?

A `List` is an **ordered collection** (also called a sequence) where:
- Elements have a **position (index)** starting from 0
- **Insertion order is preserved** — elements stay in the order you added them
- **Duplicates are allowed** — you can add the same element multiple times

---

### ArrayList

The most commonly used List implementation.

**Internal structure:** A **resizable array** (dynamic array).

```
Internally:  [Apple][Banana][Cherry][  ][  ][  ][  ][  ][  ][  ]
              index 0  1       2       (empty slots for future use)
              
              size = 3
              capacity = 10 (default initial capacity)
```

**How it works:**
- Stores elements in a contiguous block of memory (array)
- When the array is full, it creates a new array **1.5x the size** and copies all elements over
- Default initial capacity is **10**

**Key characteristics:**

| Operation | Time Complexity | Why? |
|-----------|:-:|------|
| `get(index)` | O(1) | Direct access by index — just math: baseAddress + index |
| `add(element)` at end | O(1) amortized | Just put at next empty slot (occasional resize is O(n)) |
| `add(index, element)` in middle | O(n) | Must shift all elements after that index to the right |
| `remove(index)` | O(n) | Must shift all elements after that index to the left |
| `contains(element)` | O(n) | Must scan through elements one by one |

**When to use:** You read/access elements by index frequently, mostly add at the end, rarely insert/remove from the middle.

```java
import java.util.ArrayList;

public class ArrayListDemo {
    public static void main(String[] args) {
        ArrayList<String> names = new ArrayList<>();  // default capacity 10
        ArrayList<Integer> numbers = new ArrayList<>(50);  // capacity 50

        names.add("Pavan");    // index 0
        names.add("Raj");      // index 1
        names.add("Amit");     // index 2

        System.out.println(names.get(0));   // Pavan — O(1) fast!
        System.out.println(names.size());   // 3
    }
}
```

---

### The Vector Class

Almost identical to ArrayList but with one key difference: **all methods are synchronized** (thread-safe).

```java
import java.util.Vector;

Vector<String> v = new Vector<>();
v.add("A");
v.add("B");
v.get(0);  // same API as ArrayList
```

**ArrayList vs Vector:**

| Feature | ArrayList | Vector |
|---------|-----------|--------|
| Synchronization | Not synchronized (not thread-safe) | Synchronized (thread-safe) |
| Performance | Faster (no locking overhead) | Slower (acquires lock on every operation) |
| Growth | Grows by **50%** (1.5x) | Grows by **100%** (2x) |
| When to use | Single-threaded / most cases | Legacy code only |

**Note:** Vector is a legacy class from Java 1.0. In modern Java, use `Collections.synchronizedList()` or `CopyOnWriteArrayList` for thread-safety.

---

### The LinkedList Class

**Internal structure:** A **doubly-linked list** — each element (node) stores data + pointer to next node + pointer to previous node.

```
  null ← [Prev|Apple|Next] ↔ [Prev|Banana|Next] ↔ [Prev|Cherry|Next] → null
              head                                        tail
```

Each node internally:
```java
class Node<E> {
    E data;
    Node<E> next;
    Node<E> prev;
}
```

**Key characteristics:**

| Operation | Time Complexity | Why? |
|-----------|:-:|------|
| `get(index)` | O(n) | Must walk from head (or tail) node by node |
| `add(element)` at end | O(1) | Just update tail's next pointer |
| `add(0, element)` at start | O(1) | Just update head's prev pointer |
| `add(index, element)` in middle | O(n) | Walk to position O(n), then insert O(1) |
| `remove(0)` from start | O(1) | Just move head pointer |
| `contains(element)` | O(n) | Must scan through nodes |

**When to use:** Frequently insert/remove from beginning or middle, don't need random access by index, using it as Queue/Deque.

```java
import java.util.LinkedList;

public class LinkedListDemo {
    public static void main(String[] args) {
        LinkedList<String> names = new LinkedList<>();

        names.add("Pavan");
        names.add("Raj");
        names.addFirst("Amit");   // add at beginning — O(1)
        names.addLast("Suresh");  // add at end — O(1)

        System.out.println(names);  // [Amit, Pavan, Raj, Suresh]

        names.removeFirst();  // O(1)
        names.removeLast();   // O(1)
        System.out.println(names);  // [Pavan, Raj]
    }
}
```

---

### ArrayList vs LinkedList — When to Use Which?

| Criteria | ArrayList | LinkedList |
|----------|-----------|------------|
| Access by index | **Fast O(1)** | Slow O(n) |
| Add/remove at end | Fast O(1) | Fast O(1) |
| Add/remove at beginning | **Slow O(n)** — shifts all | **Fast O(1)** |
| Add/remove in middle | Slow O(n) — shifts elements | Slow O(n) to find + O(1) to insert |
| Memory usage | Less (just array) | More (each node has 2 extra pointers) |
| Cache performance | Better (contiguous memory) | Worse (nodes scattered in memory) |
| Implements | List, RandomAccess | List, Queue, Deque |

**Rule of thumb:** Use ArrayList by default. Use LinkedList only when you specifically need fast insertion/removal at both ends (as a Queue/Deque).

---

### Accessing Pointers in LinkedList

Java's `java.util.LinkedList` **hides all internal pointers from you**. The Node class is `private` — you cannot access `next`/`prev` pointers directly.

```java
// You interact through methods only:
LinkedList<String> list = new LinkedList<>();
list.add("A");
list.add("B");
list.add("C");

list.getFirst();    // "A" — internally returns first.item
list.getLast();     // "C" — internally returns last.item
list.get(1);       // "B" — internally walks pointers for you
```

**If you want direct pointer access — build your own LinkedList:**

```java
class Node<E> {
    E data;
    Node<E> next;
    Node<E> prev;

    Node(E data) {
        this.data = data;
        this.next = null;
        this.prev = null;
    }
}

class MyLinkedList<E> {
    Node<E> head;
    Node<E> tail;
    int size;

    public MyLinkedList() {
        head = null;
        tail = null;
        size = 0;
    }

    public void add(E data) {
        Node<E> newNode = new Node<>(data);
        if (head == null) {
            head = newNode;
            tail = newNode;
        } else {
            tail.next = newNode;
            newNode.prev = tail;
            tail = newNode;
        }
        size++;
    }

    public void printForward() {
        Node<E> current = head;
        while (current != null) {
            System.out.print(current.data + " → ");
            current = current.next;
        }
        System.out.println("null");
    }

    public void printBackward() {
        Node<E> current = tail;
        while (current != null) {
            System.out.print(current.data + " → ");
            current = current.prev;
        }
        System.out.println("null");
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        MyLinkedList<String> list = new MyLinkedList<>();
        list.add("Apple");
        list.add("Banana");
        list.add("Cherry");

        // Direct pointer access!
        System.out.println(list.head.data);           // Apple
        System.out.println(list.head.next.data);      // Banana
        System.out.println(list.head.next.next.data); // Cherry
        System.out.println(list.tail.data);           // Cherry
        System.out.println(list.tail.prev.data);      // Banana

        list.printForward();   // Apple → Banana → Cherry → null
        list.printBackward();  // Cherry → Banana → Apple → null
    }
}
```

**In Collections Framework:** Use `java.util.LinkedList` through its methods (black box).
**In DSA/interviews:** Build your own and access pointers directly.

---

## Topic 6: How to Iterate and Access Elements in ArrayList, Vector, and LinkedList

There are **5 main ways** to iterate over a List. All work for ArrayList, Vector, and LinkedList.

---

### Method 1: For Loop (Index-Based)

```java
List<String> list = new ArrayList<>(List.of("Apple", "Banana", "Cherry", "Date"));

for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));
}
// Output: Apple, Banana, Cherry, Date
```

| Collection | Performance | Verdict |
|---|---|---|
| ArrayList | `get(i)` is O(1) — fast | Great choice |
| Vector | `get(i)` is O(1) — fast | Great choice |
| LinkedList | `get(i)` is O(n) — walks from head every time | **TERRIBLE! O(n²) total. Avoid!** |

**Why LinkedList is terrible with index loop:**
```
get(0) → start at head, 0 steps
get(1) → start at head, 1 step
get(2) → start at head, 2 steps
get(n) → start at head, n steps
Total: 0 + 1 + 2 + ... + n = O(n²) for entire loop!
```

---

### Method 2: For-Each Loop (Enhanced For)

```java
for (String fruit : list) {
    System.out.println(fruit);
}
```

- Internally uses an Iterator (`hasNext()` → `next()`)
- **O(n) for ALL list types** — no index lookups
- Clean and readable
- **Limitation:** Cannot modify the list during iteration, cannot access index

---

### Method 3: Iterator

```java
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    String fruit = it.next();
    System.out.println(fruit);
}
```

- Same as for-each internally but gives **manual control**
- Can **remove** elements safely using `it.remove()`
- O(n) for all collection types

**Remove while iterating:**
```java
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    String fruit = it.next();
    if (fruit.startsWith("B")) {
        it.remove();  // safely removes "Banana"
    }
}
System.out.println(list);  // [Apple, Cherry, Date]
```

---

### Method 4: ListIterator (Bidirectional — Forward and Backward)

Only available for Lists. Can go both directions!

```java
ListIterator<String> lit = list.listIterator();

// Forward
while (lit.hasNext()) {
    int index = lit.nextIndex();
    String fruit = lit.next();
    System.out.println(index + ": " + fruit);
}
// 0: Apple, 1: Banana, 2: Cherry, 3: Date

// Backward (cursor is now at end)
while (lit.hasPrevious()) {
    int index = lit.previousIndex();
    String fruit = lit.previous();
    System.out.println(index + ": " + fruit);
}
// 3: Date, 2: Cherry, 1: Banana, 0: Apple
```

**ListIterator extra methods:**

| Method | Description |
|--------|-------------|
| `hasNext()` | Is there a next element? |
| `next()` | Move forward, return element |
| `hasPrevious()` | Is there a previous element? |
| `previous()` | Move backward, return element |
| `nextIndex()` | Index of element that next() would return |
| `previousIndex()` | Index of element that previous() would return |
| `remove()` | Remove last returned element |
| `set(E e)` | Replace last returned element |
| `add(E e)` | Insert element at current position |

**Starting from a specific index:**
```java
ListIterator<String> lit = list.listIterator(2);  // start from index 2
while (lit.hasNext()) {
    System.out.println(lit.next());
}
// Output: Cherry, Date (skipped Apple and Banana)
```

---

### Method 5: forEach() Method (Java 8+)

```java
list.forEach(fruit -> System.out.println(fruit));

// Or with method reference:
list.forEach(System.out::println);
```

Uses a lambda/functional interface. Clean for simple operations.

---

### Accessing Individual Elements (Without Looping)

```java
List<String> fruits = new ArrayList<>(List.of("Apple", "Banana", "Cherry", "Date"));

String first = fruits.get(0);                      // "Apple"
String last = fruits.get(fruits.size() - 1);       // "Date"
int pos = fruits.indexOf("Cherry");                // 2
int notFound = fruits.indexOf("Mango");            // -1 (not found)
boolean exists = fruits.contains("Banana");        // true
List<String> sub = fruits.subList(1, 3);           // [Banana, Cherry] (from inclusive, to exclusive)
```

---

### Summary — Which Iteration Method to Use?

| Method | Best For | Can Remove? | Can Go Backward? | Knows Index? |
|--------|----------|:-----------:|:----------------:|:------------:|
| For loop | ArrayList/Vector when you need index | No (unsafe) | Yes | Yes |
| For-each | Simple iteration, any collection | No | No | No |
| Iterator | When you need to remove during iteration | Yes | No | No |
| ListIterator | Bidirectional, modify during iteration | Yes | Yes | Yes |
| forEach() | Simple one-liner operations (Java 8+) | No | No | No |

**Golden rule:** Don't use index-based for loop on LinkedList — it's O(n²). Use Iterator or for-each instead.

---

## Topic 7: Queue Interface in Java

### What is a Queue?

A Queue is a collection designed for holding elements **before processing**. It follows **FIFO** — First-In, First-Out — like a real-world queue/line.

```
Enqueue (add) →  [D] [C] [B] [A]  → Dequeue (remove)
                 back           front

First person in line gets served first.
```

**Real-world examples:**
- People standing in line at a bank
- Print jobs waiting for a printer
- Tasks waiting to be processed by a CPU
- Messages waiting to be delivered

---

### How to Create a Queue (Preferred Way)

```java
import java.util.*;

// ArrayDeque is the PREFERRED implementation for Queue
Queue<String> queue = new ArrayDeque<>();
Queue<Integer> numberQueue = new ArrayDeque<>();
```

**Why ArrayDeque?** Faster than LinkedList (contiguous memory, better cache performance, less memory overhead).

---

### Queue Interface Methods — Two Sets

```java
public interface Queue<E> extends Collection<E> {
    // === Throws exception on failure ===
    boolean add(E e);      // insert at tail
    E remove();            // remove from head
    E element();           // look at head without removing

    // === Returns special value on failure ===
    boolean offer(E e);    // insert at tail
    E poll();              // remove from head
    E peek();              // look at head without removing
}
```

| Operation | Throws Exception (if empty/full) | Returns null/false (if empty/full) |
|-----------|:---:|:---:|
| Insert at tail | `add(e)` | `offer(e)` |
| Remove from head | `remove()` | `poll()` |
| Examine head | `element()` | `peek()` |

**Best practice:** Use `offer()`, `poll()`, `peek()` — they're safer (no exceptions).

---

### Each Method Explained with Examples

#### 1. offer(E e) — Add element to the back of queue

```java
Queue<String> queue = new ArrayDeque<>();

queue.offer("Alice");
queue.offer("Bob");
queue.offer("Charlie");
System.out.println(queue);  // [Alice, Bob, Charlie]

// Returns true if added successfully
boolean added = queue.offer("David");
System.out.println(added);  // true
System.out.println(queue);  // [Alice, Bob, Charlie, David]
```

#### 2. add(E e) — Same as offer but throws exception if capacity-restricted and full

```java
Queue<String> queue = new ArrayDeque<>();

queue.add("Alice");
queue.add("Bob");
System.out.println(queue);  // [Alice, Bob]

// For ArrayDeque (no capacity limit), add() and offer() behave the same
// Difference matters for bounded queues (fixed size)
```

#### 3. peek() — Look at the front element WITHOUT removing it

```java
Queue<String> queue = new ArrayDeque<>();
queue.offer("Alice");
queue.offer("Bob");
queue.offer("Charlie");

System.out.println(queue.peek());  // Alice (still in queue)
System.out.println(queue.peek());  // Alice (still in queue — not removed!)
System.out.println(queue);         // [Alice, Bob, Charlie] — unchanged

// On empty queue:
Queue<String> emptyQueue = new ArrayDeque<>();
System.out.println(emptyQueue.peek());  // null (no exception)
```

#### 4. element() — Same as peek but throws exception if empty

```java
Queue<String> queue = new ArrayDeque<>();
queue.offer("Alice");

System.out.println(queue.element());  // Alice (not removed)

// On empty queue:
Queue<String> emptyQueue = new ArrayDeque<>();
System.out.println(emptyQueue.element());  // throws NoSuchElementException!
```

#### 5. poll() — Remove and return the front element

```java
Queue<String> queue = new ArrayDeque<>();
queue.offer("Alice");
queue.offer("Bob");
queue.offer("Charlie");
System.out.println(queue);  // [Alice, Bob, Charlie]

String first = queue.poll();
System.out.println(first);  // Alice (removed from queue)
System.out.println(queue);  // [Bob, Charlie]

String second = queue.poll();
System.out.println(second);  // Bob (removed)
System.out.println(queue);   // [Charlie]

// On empty queue:
Queue<String> emptyQueue = new ArrayDeque<>();
System.out.println(emptyQueue.poll());  // null (no exception)
```

#### 6. remove() — Same as poll but throws exception if empty

```java
Queue<String> queue = new ArrayDeque<>();
queue.offer("Alice");

String removed = queue.remove();
System.out.println(removed);  // Alice

// On empty queue:
Queue<String> emptyQueue = new ArrayDeque<>();
emptyQueue.remove();  // throws NoSuchElementException!
```

#### 7. size() — Number of elements in queue

```java
Queue<String> queue = new ArrayDeque<>();
queue.offer("Alice");
queue.offer("Bob");

System.out.println(queue.size());  // 2
```

#### 8. isEmpty() — Check if queue has no elements

```java
Queue<String> queue = new ArrayDeque<>();
System.out.println(queue.isEmpty());  // true

queue.offer("Alice");
System.out.println(queue.isEmpty());  // false
```

#### 9. contains(Object o) — Check if element exists in queue

```java
Queue<String> queue = new ArrayDeque<>();
queue.offer("Alice");
queue.offer("Bob");

System.out.println(queue.contains("Alice"));  // true
System.out.println(queue.contains("Zara"));   // false
```

---

### FIFO Demonstrated Step by Step

```java
Queue<Integer> queue = new ArrayDeque<>();

queue.offer(10);   // Queue: [10]
queue.offer(20);   // Queue: [10, 20]
queue.offer(30);   // Queue: [10, 20, 30]
queue.offer(40);   // Queue: [10, 20, 30, 40]

queue.poll();      // returns 10 → Queue: [20, 30, 40]
queue.poll();      // returns 20 → Queue: [30, 40]

queue.offer(50);   // Queue: [30, 40, 50]

queue.poll();      // returns 30 → Queue: [40, 50]
queue.poll();      // returns 40 → Queue: [50]
queue.poll();      // returns 50 → Queue: []
queue.poll();      // returns null (empty)
```

---

### Practical Example — Task Processing

```java
import java.util.*;

public class TaskProcessor {
    public static void main(String[] args) {
        Queue<String> taskQueue = new ArrayDeque<>();

        // Tasks arrive
        taskQueue.offer("Send email to client");
        taskQueue.offer("Generate monthly report");
        taskQueue.offer("Backup database");
        taskQueue.offer("Deploy to production");

        // Process tasks one by one (FIFO)
        System.out.println("Processing tasks:");
        while (!taskQueue.isEmpty()) {
            String task = taskQueue.poll();
            System.out.println("  Processing: " + task);
        }
        System.out.println("All tasks done!");
    }
}
```

Output:
```
Processing tasks:
  Processing: Send email to client
  Processing: Generate monthly report
  Processing: Backup database
  Processing: Deploy to production
All tasks done!
```

---

### Iterating Over a Queue

```java
Queue<String> queue = new ArrayDeque<>();
queue.offer("Alice");
queue.offer("Bob");
queue.offer("Charlie");

// Method 1: For-each (does NOT remove elements)
for (String item : queue) {
    System.out.println(item);
}
// Queue still has all elements: [Alice, Bob, Charlie]

// Method 2: Poll until empty (REMOVES elements)
while (!queue.isEmpty()) {
    System.out.println(queue.poll());
}
// Queue is now empty: []
```

---

### Important Rules for ArrayDeque as Queue

1. **No null elements** — `queue.offer(null)` throws `NullPointerException`
2. **No capacity limit** — grows automatically (unlike bounded queues)
3. **Not thread-safe** — don't use from multiple threads without synchronization

---

### Summary of Queue Methods

| Method | What it does | If queue is empty |
|--------|-------------|-------------------|
| `offer(e)` | Add to back | N/A (always works for ArrayDeque) |
| `add(e)` | Add to back | N/A (same as offer for ArrayDeque) |
| `peek()` | Look at front | Returns `null` |
| `element()` | Look at front | Throws `NoSuchElementException` |
| `poll()` | Remove from front | Returns `null` |
| `remove()` | Remove from front | Throws `NoSuchElementException` |
| `size()` | Count elements | Returns 0 |
| `isEmpty()` | Check if empty | Returns `true` |
| `contains(o)` | Check if present | Returns `false` |

---

## Topic 8: Deque Interface (Double-Ended Queue)

### What is a Deque?

A **Deque** (pronounced "deck") is a **Double-Ended Queue** — you can insert and remove elements from **both ends** (front and back).

```
           ← addFirst()                    addLast() →
              pollFirst()                   pollLast()
              peekFirst()                   peekLast()
              
         FRONT                                    BACK
         ┌────┬────┬────┬────┬────┐
         │ A  │ B  │ C  │ D  │ E  │
         └────┴────┴────┴────┴────┘
         
           ← removeFirst()              removeLast() →
```

**A Deque can act as:**
- A **Queue** (FIFO) — add at back, remove from front
- A **Stack** (LIFO) — add at front, remove from front (push/pop)

---

### How to Create a Deque

```java
import java.util.*;

Deque<String> deque = new ArrayDeque<>();
Deque<Integer> numberDeque = new ArrayDeque<>();
```

---

### Deque Interface Methods

```java
public interface Deque<E> extends Queue<E> {
    // === FRONT (First) operations ===
    void addFirst(E e);        // insert at front (throws exception if full)
    boolean offerFirst(E e);   // insert at front (returns false if full)
    E removeFirst();           // remove from front (throws if empty)
    E pollFirst();             // remove from front (returns null if empty)
    E getFirst();              // look at front (throws if empty)
    E peekFirst();             // look at front (returns null if empty)

    // === BACK (Last) operations ===
    void addLast(E e);         // insert at back (throws exception if full)
    boolean offerLast(E e);    // insert at back (returns false if full)
    E removeLast();            // remove from back (throws if empty)
    E pollLast();              // remove from back (returns null if empty)
    E getLast();               // look at back (throws if empty)
    E peekLast();              // look at back (returns null if empty)

    // === Stack operations ===
    void push(E e);            // same as addFirst
    E pop();                   // same as removeFirst
    E peek();                  // same as peekFirst
}
```

---

### Each Method with Examples

#### Front Operations

```java
Deque<String> deque = new ArrayDeque<>();

// offerFirst — add to front
deque.offerFirst("Bob");
System.out.println(deque);  // [Bob]

deque.offerFirst("Alice");
System.out.println(deque);  // [Alice, Bob]  ← Alice went to FRONT

deque.offerFirst("Zara");
System.out.println(deque);  // [Zara, Alice, Bob]  ← Zara went to FRONT

// peekFirst — look at front without removing
System.out.println(deque.peekFirst());  // Zara
System.out.println(deque);              // [Zara, Alice, Bob] — unchanged

// pollFirst — remove from front
System.out.println(deque.pollFirst());  // Zara (removed)
System.out.println(deque);              // [Alice, Bob]
```

#### Back Operations

```java
Deque<String> deque = new ArrayDeque<>();
deque.offerLast("Alice");
deque.offerLast("Bob");
System.out.println(deque);  // [Alice, Bob]

// offerLast — add to back
deque.offerLast("Charlie");
System.out.println(deque);  // [Alice, Bob, Charlie]  ← Charlie went to BACK

// peekLast — look at back without removing
System.out.println(deque.peekLast());  // Charlie
System.out.println(deque);             // [Alice, Bob, Charlie] — unchanged

// pollLast — remove from back
System.out.println(deque.pollLast());  // Charlie (removed)
System.out.println(deque);             // [Alice, Bob]
```

#### Mixing Both Ends

```java
Deque<Integer> deque = new ArrayDeque<>();

deque.offerLast(1);     // [1]
deque.offerLast(2);     // [1, 2]
deque.offerLast(3);     // [1, 2, 3]
deque.offerFirst(0);    // [0, 1, 2, 3]  ← 0 added to front
deque.offerFirst(-1);   // [-1, 0, 1, 2, 3]  ← -1 added to front

System.out.println(deque.pollFirst());  // -1 (from front)
System.out.println(deque.pollLast());   // 3 (from back)
System.out.println(deque);              // [0, 1, 2]
```

---

### Using Deque as a Stack (LIFO)

A Stack is Last-In, First-Out — like a stack of plates.

```java
Deque<String> stack = new ArrayDeque<>();

// push — add to top (front)
stack.push("First");
stack.push("Second");
stack.push("Third");
System.out.println(stack);  // [Third, Second, First]  ← Third is on top

// peek — look at top without removing
System.out.println(stack.peek());  // Third

// pop — remove from top
System.out.println(stack.pop());  // Third (removed)
System.out.println(stack.pop());  // Second (removed)
System.out.println(stack);        // [First]
```

`push()` = `addFirst()`, `pop()` = `removeFirst()`, `peek()` = `peekFirst()`

---

### Using Deque as a Queue (FIFO)

```java
Deque<String> queue = new ArrayDeque<>();

// Add at back (enqueue)
queue.offerLast("Alice");
queue.offerLast("Bob");
queue.offerLast("Charlie");
System.out.println(queue);  // [Alice, Bob, Charlie]

// Remove from front (dequeue)
System.out.println(queue.pollFirst());  // Alice
System.out.println(queue.pollFirst());  // Bob
System.out.println(queue);              // [Charlie]
```

---

### Deque as Queue vs Deque as Stack

| Operation | As Queue (FIFO) | As Stack (LIFO) |
|-----------|----------------|-----------------|
| Add | `offerLast(e)` — add to back | `push(e)` — add to front |
| Remove | `pollFirst()` — remove from front | `pop()` — remove from front |
| Look | `peekFirst()` — look at front | `peek()` — look at front |

---

### Practical Example — Browser Back/Forward History

#### The Problem We're Solving

Open your web browser (Chrome, Firefox, etc.). You see a **Back button (←)** and a **Forward button (→)**. How do these work internally? How does the browser remember which pages to go back to and which pages to go forward to?

The answer: **Two stacks working together.**

#### The Three Things We Need

Think of it like this — at any moment, the browser needs to track:

1. **`currentPage`** — This is a simple `String` variable. It holds the URL/name of the page you are currently looking at on your screen RIGHT NOW.

2. **`backStack`** — This is a Stack (we use `Deque` with `push/pop`). It stores all the pages you visited BEFORE the current page. When you press the Back button, the browser pops from this stack to know which page to show you.

3. **`forwardStack`** — This is another Stack. It stores pages you "left behind" when you pressed Back. When you press the Forward button, the browser pops from this stack to know which page to show you.

#### The Three Actions (Rules)

**Action 1: User clicks a link (visits a new page)**
- The page you were on is now "in the past" → push it onto `backStack`
- The new page becomes your `currentPage`
- Clear `forwardStack` (you started a new browsing path, so forward history is gone)

**Action 2: User presses Back button**
- The page you're leaving might be needed if user presses Forward later → push `currentPage` onto `forwardStack`
- Where to go back to? → pop from `backStack` and make that your new `currentPage`

**Action 3: User presses Forward button**
- The page you're leaving might be needed if user presses Back again → push `currentPage` onto `backStack`
- Where to go forward to? → pop from `forwardStack` and make that your new `currentPage`

#### The Code

```java
import java.util.*;

public class BrowserHistory {
    public static void main(String[] args) {
        Deque<String> backStack = new ArrayDeque<>();
        Deque<String> forwardStack = new ArrayDeque<>();
        String currentPage = "Home";

        System.out.println("Starting at: " + currentPage);
        // STATE: current=Home, backStack=[], forwardStack=[]
        // You see "Home" on your screen. No history yet.

        // ─── USER CLICKS LINK TO GOOGLE ───
        backStack.push(currentPage);   // "Home" goes into backStack (it's in the past now)
        currentPage = "Google";        // Screen now shows Google
        forwardStack.clear();          // No forward history (fresh navigation)
        // STATE: current=Google, backStack=[Home], forwardStack=[]
        // Screen shows Google. If you press Back, you'll go to Home.

        // ─── USER CLICKS LINK TO YOUTUBE ───
        backStack.push(currentPage);   // "Google" goes into backStack
        currentPage = "YouTube";       // Screen now shows YouTube
        forwardStack.clear();
        // STATE: current=YouTube, backStack=[Google, Home], forwardStack=[]
        // Screen shows YouTube. backStack has Google on top, Home at bottom.

        // ─── USER CLICKS LINK TO GITHUB ───
        backStack.push(currentPage);   // "YouTube" goes into backStack
        currentPage = "GitHub";        // Screen now shows GitHub
        forwardStack.clear();
        // STATE: current=GitHub, backStack=[YouTube, Google, Home], forwardStack=[]
        // Screen shows GitHub. backStack has YouTube on top.

        System.out.println("Currently viewing: " + currentPage);  // GitHub

        // ─── USER PRESSES BACK BUTTON ───
        // "I want to go back to the previous page"
        forwardStack.push(currentPage);   // Save "GitHub" in forwardStack (might need it later)
        currentPage = backStack.pop();    // Pop "YouTube" from backStack (it was on top)
        System.out.println("Pressed Back → now at: " + currentPage);  // YouTube
        // STATE: current=YouTube, backStack=[Google, Home], forwardStack=[GitHub]
        // Screen shows YouTube. GitHub is saved in forwardStack.

        // ─── USER PRESSES BACK BUTTON AGAIN ───
        // "I want to go back one more page"
        forwardStack.push(currentPage);   // Save "YouTube" in forwardStack
        currentPage = backStack.pop();    // Pop "Google" from backStack
        System.out.println("Pressed Back → now at: " + currentPage);  // Google
        // STATE: current=Google, backStack=[Home], forwardStack=[YouTube, GitHub]
        // Screen shows Google. forwardStack has YouTube on top, GitHub below.

        // ─── USER PRESSES FORWARD BUTTON ───
        // "Actually, I want to go forward to where I was"
        backStack.push(currentPage);      // Save "Google" in backStack
        currentPage = forwardStack.pop(); // Pop "YouTube" from forwardStack (it was on top)
        System.out.println("Pressed Forward → now at: " + currentPage);  // YouTube
        // STATE: current=YouTube, backStack=[Google, Home], forwardStack=[GitHub]
        // Screen shows YouTube. Can still go forward to GitHub or back to Google.
    }
}
```

#### Why Does This Work?

The key insight is **LIFO (Last-In, First-Out)** behavior of stacks:

- The LAST page you visited before the current one is the FIRST page you should go back to. That's exactly what a stack does — the last thing you pushed is the first thing you pop.

- When you go back multiple times (GitHub → YouTube → Google), each page gets pushed onto forwardStack in that order. So when you press Forward, you get them back in reverse — exactly the right order!

#### Output of the Program:
```
Starting at: Home
Currently viewing: GitHub
Pressed Back → now at: YouTube
Pressed Back → now at: Google
Pressed Forward → now at: YouTube
```

#### Complete State Table

```
Action              Screen Shows    backStack              forwardStack
─────────────────────────────────────────────────────────────────────────
Start               Home           []                     []
Click Google        Google         [Home]                 []
Click YouTube       YouTube        [Google, Home]         []
Click GitHub        GitHub         [YouTube, Google, Home] []
Press BACK          YouTube        [Google, Home]         [GitHub]
Press BACK          Google         [Home]                 [YouTube, GitHub]
Press FORWARD       YouTube        [Google, Home]         [GitHub]
Press FORWARD       GitHub         [YouTube, Google, Home] []
```

---

### Why Use ArrayDeque Instead of the Legacy Stack Class?

| Feature | ArrayDeque (use this) | Stack (avoid) |
|---------|----------------------|---------------|
| Speed | Fast (not synchronized) | Slow (every method is synchronized) |
| Inheritance | Implements Deque | Extends Vector (unnecessary baggage) |
| Design | Clean, purpose-built | Broken — allows index access, breaking LIFO |
| Recommendation | Official Java docs recommend this | Legacy, do not use in new code |

---

### Summary of Deque Methods

| Operation | Front | Back |
|-----------|-------|------|
| Insert (throws) | `addFirst(e)` | `addLast(e)` |
| Insert (safe) | `offerFirst(e)` | `offerLast(e)` |
| Remove (throws) | `removeFirst()` | `removeLast()` |
| Remove (safe) | `pollFirst()` | `pollLast()` |
| Examine (throws) | `getFirst()` | `getLast()` |
| Examine (safe) | `peekFirst()` | `peekLast()` |

| Stack Operation | Equivalent |
|----------------|------------|
| `push(e)` | `addFirst(e)` |
| `pop()` | `removeFirst()` |
| `peek()` | `peekFirst()` |

---

## Topic 9: Priority Queue

### What is a Priority Queue?

A regular Queue follows FIFO — first in, first out. A **PriorityQueue** is different:

**Elements are dequeued based on their PRIORITY, not their insertion order.**

The element with the highest priority (by default, the **smallest value**) is always at the front.

```
Regular Queue (FIFO):    First person in line gets served first
Priority Queue:          Most urgent person gets served first (regardless of when they arrived)
```

**Real-world examples:**
- Hospital emergency room — heart attack patient treated before cold patient
- Operating system — high-priority processes get CPU time first
- Airline boarding — first class boards before economy

---

### How to Create a Priority Queue

```java
import java.util.*;

// Default: smallest element has highest priority (min-heap)
PriorityQueue<Integer> pq = new PriorityQueue<>();
```

---

### Basic Example — Numbers

```java
import java.util.*;

public class PriorityQueueDemo {
    public static void main(String[] args) {
        PriorityQueue<Integer> pq = new PriorityQueue<>();

        // Add elements in random order
        pq.offer(40);
        pq.offer(10);
        pq.offer(30);
        pq.offer(20);
        pq.offer(50);

        // Poll always removes the SMALLEST element
        System.out.println(pq.poll());  // 10 (smallest)
        System.out.println(pq.poll());  // 20 (next smallest)
        System.out.println(pq.poll());  // 30
        System.out.println(pq.poll());  // 40
        System.out.println(pq.poll());  // 50 (largest — last to come out)
    }
}
```

We added 40 first, but 10 comes out first because it's the smallest. This is NOT FIFO!

---

### Key Characteristics

| Feature | Details |
|---------|---------|
| Internal structure | Binary Heap (min-heap by default) |
| Ordering | Smallest element always at the head |
| Duplicates | Allowed |
| Null elements | NOT allowed (throws NullPointerException) |
| Thread-safe | No |
| Time: offer() | O(log n) |
| Time: poll() | O(log n) |
| Time: peek() | O(1) |

---

### Methods (Same as Queue Interface)

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();

pq.offer(30);     // add element — O(log n)
pq.offer(10);
pq.offer(20);

pq.peek();        // look at smallest without removing → 10
pq.poll();        // remove and return smallest → 10
pq.size();        // number of elements → 2
pq.isEmpty();     // is it empty? → false
pq.contains(20);  // does it contain 20? → true
```

---

### Important: Printing Does NOT Show Sorted Order

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(40);
pq.offer(10);
pq.offer(30);
pq.offer(20);

// This does NOT guarantee sorted output!
System.out.println(pq);  // internal order is NOT fully sorted

// Only poll() guarantees elements in priority order:
while (!pq.isEmpty()) {
    System.out.print(pq.poll() + " ");  // 10 20 30 40 — guaranteed sorted!
}
```

Internally PriorityQueue uses a binary heap — only the root (minimum) is guaranteed to be smallest. The rest is partially ordered.

---

### PriorityQueue with Strings

Strings are compared alphabetically:

```java
PriorityQueue<String> pq = new PriorityQueue<>();
pq.offer("Banana");
pq.offer("Apple");
pq.offer("Cherry");
pq.offer("Date");

while (!pq.isEmpty()) {
    System.out.println(pq.poll());
}
// Output: Apple, Banana, Cherry, Date (alphabetical order)
```

---

### The Problem — How to Use PriorityQueue with Custom Objects?

Java knows how to compare Integers (10 < 20) and Strings ("Apple" < "Banana").

But what about your own objects? Or arrays?

```java
int[] patientA = {3, 101};  // severity 3, patient ID 101
int[] patientB = {1, 102};  // severity 1, patient ID 102

// Which has higher priority? Java doesn't know!
// Should it compare by first element? second? the sum?
```

**Solution: You create a Comparator — a class that tells Java HOW to compare two objects.**

---

### What is a Comparator?

A `Comparator` is an interface with one method: `compare(T a, T b)`.

```java
public interface Comparator<T> {
    int compare(T a, T b);
}
```

You implement this method and return:
- **Negative number** (like -1) → `a` should come BEFORE `b` (a has higher priority)
- **Zero** (0) → `a` and `b` are equal in priority
- **Positive number** (like +1) → `a` should come AFTER `b` (b has higher priority)

---

### Creating a Comparator — Step by Step

#### Example 1: Comparing int[] arrays by severity (first element)

We want: smaller severity number = higher priority (treated first).

```java
import java.util.*;

// Step 1: Create a class that implements Comparator<int[]>
class SeverityComparator implements Comparator<int[]> {

    @Override
    public int compare(int[] a, int[] b) {
        // a[0] is severity of patient a
        // b[0] is severity of patient b

        if (a[0] < b[0]) {
            return -1;  // a has LOWER severity number → a comes FIRST (higher priority)
        } else if (a[0] > b[0]) {
            return 1;   // a has HIGHER severity number → a comes LATER (lower priority)
        } else {
            return 0;   // same severity
        }
    }
}
```

```java
// Step 2: Pass the comparator to PriorityQueue
public class HospitalER {
    public static void main(String[] args) {
        SeverityComparator comparator = new SeverityComparator();
        PriorityQueue<int[]> er = new PriorityQueue<>(comparator);

        er.offer(new int[]{3, 101});  // Patient 101: mild injury (severity 3)
        er.offer(new int[]{1, 102});  // Patient 102: heart attack (severity 1)
        er.offer(new int[]{2, 103});  // Patient 103: broken arm (severity 2)
        er.offer(new int[]{5, 104});  // Patient 104: headache (severity 5)
        er.offer(new int[]{1, 105});  // Patient 105: stroke (severity 1)

        // Poll always returns the patient with LOWEST severity number
        System.out.println("Treatment order:");
        while (!er.isEmpty()) {
            int[] patient = er.poll();
            System.out.println("  Severity " + patient[0] + " → Patient " + patient[1]);
        }
    }
}
```

Output:
```
Treatment order:
  Severity 1 → Patient 102  (heart attack — treated first!)
  Severity 1 → Patient 105  (stroke — same severity)
  Severity 2 → Patient 103  (broken arm)
  Severity 3 → Patient 101  (mild injury)
  Severity 5 → Patient 104  (headache — treated last)
```

Patient 101 arrived first but gets treated 4th because his severity is lower priority!

---

#### How the Comparator Works Internally

When you `offer()` a new patient, the PriorityQueue calls your comparator to decide where to place it:

```
Comparing {1, 102} vs {3, 101}:
    a[0] = 1, b[0] = 3
    1 < 3 → return -1 (negative)
    Negative means: a comes BEFORE b
    So {1, 102} gets HIGHER priority than {3, 101}

Comparing {5, 104} vs {1, 102}:
    a[0] = 5, b[0] = 1
    5 > 1 → return 1 (positive)
    Positive means: a comes AFTER b
    So {5, 104} gets LOWER priority than {1, 102}
```

---

#### Example 2: Comparator for Student Objects

```java
class Student {
    String name;
    int marks;

    Student(String name, int marks) {
        this.name = name;
        this.marks = marks;
    }

    @Override
    public String toString() {
        return name + " (" + marks + ")";
    }
}
```

**Comparator — lowest marks first (ascending):**

```java
class MarksAscendingComparator implements Comparator<Student> {
    @Override
    public int compare(Student a, Student b) {
        if (a.marks < b.marks) {
            return -1;  // a has less marks → a comes first
        } else if (a.marks > b.marks) {
            return 1;   // a has more marks → a comes later
        } else {
            return 0;   // same marks
        }
    }
}
```

**Comparator — highest marks first (descending):**

```java
class MarksDescendingComparator implements Comparator<Student> {
    @Override
    public int compare(Student a, Student b) {
        if (a.marks > b.marks) {
            return -1;  // a has MORE marks → a comes FIRST (we want highest first)
        } else if (a.marks < b.marks) {
            return 1;   // a has LESS marks → a comes LATER
        } else {
            return 0;
        }
    }
}
```

**Comparator — alphabetical by name:**

```java
class NameComparator implements Comparator<Student> {
    @Override
    public int compare(Student a, Student b) {
        return a.name.compareTo(b.name);
        // String's compareTo() already returns negative/zero/positive
        // "Apple".compareTo("Banana") → negative (A comes before B)
        // "Banana".compareTo("Apple") → positive (B comes after A)
    }
}
```

**Using them:**

```java
public class Main {
    public static void main(String[] args) {

        // Queue where student with LOWEST marks gets polled first
        PriorityQueue<Student> pq1 = new PriorityQueue<>(new MarksAscendingComparator());
        pq1.offer(new Student("Pavan", 85));
        pq1.offer(new Student("Raj", 72));
        pq1.offer(new Student("Amit", 91));

        System.out.println("Lowest marks first:");
        while (!pq1.isEmpty()) {
            System.out.println("  " + pq1.poll());
        }
        // Output:
        //   Raj (72)
        //   Pavan (85)
        //   Amit (91)

        // Queue where student with HIGHEST marks gets polled first
        PriorityQueue<Student> pq2 = new PriorityQueue<>(new MarksDescendingComparator());
        pq2.offer(new Student("Pavan", 85));
        pq2.offer(new Student("Raj", 72));
        pq2.offer(new Student("Amit", 91));

        System.out.println("Highest marks first:");
        while (!pq2.isEmpty()) {
            System.out.println("  " + pq2.poll());
        }
        // Output:
        //   Amit (91)
        //   Pavan (85)
        //   Raj (72)

        // Queue sorted alphabetically by name
        PriorityQueue<Student> pq3 = new PriorityQueue<>(new NameComparator());
        pq3.offer(new Student("Pavan", 85));
        pq3.offer(new Student("Raj", 72));
        pq3.offer(new Student("Amit", 91));

        System.out.println("Alphabetical by name:");
        while (!pq3.isEmpty()) {
            System.out.println("  " + pq3.poll());
        }
        // Output:
        //   Amit (91)
        //   Pavan (85)
        //   Raj (72)
    }
}
```

---

#### Example 3: Max PriorityQueue (Largest Integer First)

By default PriorityQueue gives smallest first. To get largest first, create a comparator that reverses the logic:

```java
class MaxComparator implements Comparator<Integer> {
    @Override
    public int compare(Integer a, Integer b) {
        if (a > b) {
            return -1;  // a is LARGER → a comes FIRST (we want largest first)
        } else if (a < b) {
            return 1;   // a is SMALLER → a comes LATER
        } else {
            return 0;
        }
    }
}

public class MaxPQDemo {
    public static void main(String[] args) {
        PriorityQueue<Integer> maxPQ = new PriorityQueue<>(new MaxComparator());

        maxPQ.offer(40);
        maxPQ.offer(10);
        maxPQ.offer(30);
        maxPQ.offer(50);
        maxPQ.offer(20);

        while (!maxPQ.isEmpty()) {
            System.out.print(maxPQ.poll() + " ");
        }
        // Output: 50 40 30 20 10 (largest first!)
    }
}
```

You can also use Java's built-in reverse comparator:
```java
PriorityQueue<Integer> maxPQ = new PriorityQueue<>(Collections.reverseOrder());
```

---

#### Example 4: Anonymous Class (No Separate File Needed)

Instead of creating a whole separate class, you can create a Comparator inline:

```java
PriorityQueue<Student> pq = new PriorityQueue<>(new Comparator<Student>() {
    @Override
    public int compare(Student a, Student b) {
        if (a.marks < b.marks) {
            return -1;
        } else if (a.marks > b.marks) {
            return 1;
        } else {
            return 0;
        }
    }
});
```

This does the same as a separate class but written right where you need it.

---

### Comparator Return Value Summary

| a vs b | Return | Meaning |
|--------|--------|---------|
| a should come FIRST | Negative (-1) | a has higher priority |
| a and b are EQUAL | Zero (0) | Same priority |
| a should come LATER | Positive (+1) | b has higher priority |

**For ascending order (smallest first):** return negative when a < b
**For descending order (largest first):** return negative when a > b

---

### Ways to Provide a Comparator

| Method | When to Use |
|--------|-------------|
| Separate class (`class MyComparator implements Comparator<T>`) | When you reuse the same comparator in multiple places |
| Anonymous class (`new Comparator<T>() { ... }`) | When you use it only once, inline |

---

### PriorityQueue vs Regular Queue

| Feature | Regular Queue (ArrayDeque) | PriorityQueue |
|---------|---------------------------|---------------|
| Order | FIFO (insertion order) | By priority (smallest/comparator) |
| Use case | Process in arrival order | Process by importance |
| peek()/poll() | Returns first inserted | Returns highest priority element |
| Internal | Array/circular buffer | Binary heap |
| Comparator needed? | No | Only for custom objects |

---

## Topic 10: The Comparable Interface

### The Problem — Natural Ordering

We used Comparator to tell PriorityQueue how to compare objects. But notice this:

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(30);
pq.offer(10);
pq.offer(20);
pq.poll();  // 10 — smallest comes out. But we never gave a Comparator!
```

How does PriorityQueue know 10 < 20 < 30 without a Comparator? Because `Integer` has a **built-in natural ordering** defined inside the class itself using the **Comparable** interface.

---

### What is Comparable?

`Comparable` is an interface that a class implements to define its own **natural ordering** — "how should MY objects be compared by default?"

```java
public interface Comparable<T> {
    int compareTo(T other);
}
```

It has ONE method: `compareTo`. The object compares ITSELF against `other`:
- **Negative** → `this` is LESS than `other` (this comes first)
- **Zero** → `this` is EQUAL to `other`
- **Positive** → `this` is GREATER than `other` (other comes first)

---

### Classes That Already Implement Comparable

Java's built-in classes already implement Comparable:

| Class | Natural Ordering |
|-------|-----------------|
| `Integer` | Numeric (1 < 2 < 3) |
| `Double` | Numeric (1.1 < 2.2 < 3.3) |
| `String` | Alphabetical ("Apple" < "Banana") |
| `Character` | Unicode value ('A' < 'B' < 'a') |

That's why PriorityQueue, TreeSet, and sorting work with these types without a Comparator — they already know how to compare themselves!

```java
// Integer implements Comparable<Integer>
// So internally: 10.compareTo(30) → returns negative → 10 comes first

// String implements Comparable<String>
// "Apple".compareTo("Banana") → returns negative → Apple comes first
```

---

### Making YOUR Class Comparable

Say you have a `Student` class and want its natural ordering to be by marks (ascending):

```java
class Student implements Comparable<Student> {
    String name;
    int marks;

    Student(String name, int marks) {
        this.name = name;
        this.marks = marks;
    }

    @Override
    public int compareTo(Student other) {
        // "this" is the current student object
        // "other" is the student being compared against

        if (this.marks < other.marks) {
            return -1;   // this student has LESS marks → this comes FIRST
        } else if (this.marks > other.marks) {
            return 1;    // this student has MORE marks → this comes LATER
        } else {
            return 0;    // same marks
        }
    }

    @Override
    public String toString() {
        return name + " (" + marks + ")";
    }
}
```

Now use Student directly with PriorityQueue **without** any Comparator:

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        // No Comparator needed! Student knows how to compare itself.
        PriorityQueue<Student> pq = new PriorityQueue<>();

        pq.offer(new Student("Pavan", 85));
        pq.offer(new Student("Raj", 72));
        pq.offer(new Student("Amit", 91));
        pq.offer(new Student("Suresh", 65));

        System.out.println("Students by marks (lowest first):");
        while (!pq.isEmpty()) {
            System.out.println("  " + pq.poll());
        }
    }
}
```

Output:
```
Students by marks (lowest first):
  Suresh (65)
  Raj (72)
  Pavan (85)
  Amit (91)
```

---

### Using Comparable with TreeSet (Auto-Sorted)

```java
public class Main {
    public static void main(String[] args) {
        TreeSet<Student> students = new TreeSet<>();

        students.add(new Student("Pavan", 85));
        students.add(new Student("Raj", 72));
        students.add(new Student("Amit", 91));
        students.add(new Student("Suresh", 65));

        // TreeSet automatically sorts using compareTo()
        for (Student s : students) {
            System.out.println(s);
        }
    }
}
// Output: Suresh (65), Raj (72), Pavan (85), Amit (91)
```

---

### Using Comparable with Collections.sort()

```java
public class Main {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>();
        students.add(new Student("Pavan", 85));
        students.add(new Student("Raj", 72));
        students.add(new Student("Amit", 91));
        students.add(new Student("Suresh", 65));

        Collections.sort(students);  // uses compareTo() automatically

        for (Student s : students) {
            System.out.println(s);
        }
    }
}
// Output: Suresh (65), Raj (72), Pavan (85), Amit (91)
```

---

### What Happens If Your Class Does NOT Implement Comparable?

```java
class Employee {
    String name;
    int salary;

    Employee(String name, int salary) {
        this.name = name;
        this.salary = salary;
    }
}

public class Main {
    public static void main(String[] args) {
        PriorityQueue<Employee> pq = new PriorityQueue<>();
        pq.offer(new Employee("Pavan", 50000));
        pq.offer(new Employee("Raj", 60000));
        // RUNTIME EXCEPTION! ClassCastException!
        // "Employee cannot be cast to java.lang.Comparable"
    }
}
```

PriorityQueue tries to call `compareTo()` but Employee doesn't have it → CRASH!

**Fix:** Either implement `Comparable<Employee>` in the class, OR pass a `Comparator` to PriorityQueue.

---

## Topic 11: The Comparator Interface (Detailed)

### Comparable vs Comparator — The Big Difference

| Feature | Comparable | Comparator |
|---------|-----------|------------|
| Interface | `java.lang.Comparable<T>` | `java.util.Comparator<T>` |
| Method | `compareTo(T other)` | `compare(T a, T b)` |
| Where defined | INSIDE the class being compared | OUTSIDE (a separate class) |
| Who calls it | Object compares ITSELF with another | External class compares TWO objects |
| How many orderings | Only ONE natural ordering per class | UNLIMITED — create many comparators |
| Modifies original class? | YES — you add `implements Comparable` | NO — original class stays untouched |
| Use when | The class has ONE obvious ordering | You need MULTIPLE ways to sort |

---

### When to Use Which?

**Use Comparable when:**
- There's ONE obvious "natural" way to order objects
- You OWN the class (can modify its source code)
- Example: Students naturally ordered by roll number

**Use Comparator when:**
- You need MULTIPLE ways to sort the same objects (by name, by marks, by age)
- You DON'T own the class (can't modify its source code)
- You want to override the natural ordering temporarily

---

### Example — Using Both Together

The class defines ONE natural ordering via Comparable. External comparators provide ALTERNATIVE orderings.

```java
import java.util.*;

// Student has a NATURAL ordering by marks (via Comparable)
class Student implements Comparable<Student> {
    String name;
    int marks;

    Student(String name, int marks) {
        this.name = name;
        this.marks = marks;
    }

    // Natural ordering: by marks ascending
    @Override
    public int compareTo(Student other) {
        if (this.marks < other.marks) return -1;
        else if (this.marks > other.marks) return 1;
        else return 0;
    }

    @Override
    public String toString() {
        return name + " (" + marks + ")";
    }
}

// EXTERNAL comparator: sort by name alphabetically
class NameComparator implements Comparator<Student> {
    @Override
    public int compare(Student a, Student b) {
        return a.name.compareTo(b.name);
    }
}

// EXTERNAL comparator: sort by marks descending
class MarksDescendingComparator implements Comparator<Student> {
    @Override
    public int compare(Student a, Student b) {
        if (a.marks > b.marks) return -1;
        else if (a.marks < b.marks) return 1;
        else return 0;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>();
        students.add(new Student("Pavan", 85));
        students.add(new Student("Raj", 72));
        students.add(new Student("Amit", 91));

        // Using NATURAL ordering (Comparable — by marks ascending)
        Collections.sort(students);
        System.out.println("By marks (natural): " + students);
        // [Raj (72), Pavan (85), Amit (91)]

        // Using CUSTOM ordering (Comparator — by name)
        Collections.sort(students, new NameComparator());
        System.out.println("By name: " + students);
        // [Amit (91), Pavan (85), Raj (72)]

        // Using ANOTHER custom ordering (Comparator — by marks descending)
        Collections.sort(students, new MarksDescendingComparator());
        System.out.println("By marks descending: " + students);
        // [Amit (91), Pavan (85), Raj (72)]
    }
}
```

The class has a natural ordering by marks via Comparable. But when you need a different ordering, you provide a Comparator externally — without changing the Student class!

---

### Summary

| Question | Comparable | Comparator |
|----------|-----------|------------|
| "How does this object compare to another?" | `this.compareTo(other)` | `compare(a, b)` |
| Defined where? | Inside the class | Separate class |
| Can have multiple? | No (one per class) | Yes (unlimited) |
| Modifies class? | Yes | No |
| Used by default in PriorityQueue/TreeSet? | Yes | Only if provided explicitly |

---

## Topic 12: The Set Interface & HashSet

### What is a Set?

A `Set` is a collection that **does not allow duplicate elements**. It models the mathematical concept of a set.

Think of it like a bag of unique marbles — you cannot have two identical marbles in the same bag.

```
ArrayList (allows duplicates):   [Apple, Banana, Apple, Cherry, Apple]  → size = 5
HashSet (no duplicates):         {Apple, Banana, Cherry}                → size = 3
```

---

### Key Properties of Set

1. **No duplicates** — if you try to add an element that already exists, it's simply ignored
2. **No index** — you CANNOT do `set.get(0)`. Elements have no position
3. **Unordered (HashSet)** — elements are NOT stored in insertion order or any order

---

### HashSet — The Most Common Set Implementation

HashSet is the fastest Set implementation. Internally it uses a **HashMap** — each element is stored as a key in the HashMap with a dummy value.

**How to create:**

```java
import java.util.*;

Set<String> fruits = new HashSet<>();
Set<Integer> numbers = new HashSet<>();
```

---

### HashSet Basics — Adding and Duplicates

```java
import java.util.*;

public class HashSetDemo {
    public static void main(String[] args) {
        Set<String> fruits = new HashSet<>();

        // Adding elements
        System.out.println(fruits.add("Apple"));    // true — added
        System.out.println(fruits.add("Banana"));   // true — added
        System.out.println(fruits.add("Cherry"));   // true — added
        System.out.println(fruits.add("Apple"));    // false — DUPLICATE, ignored!
        System.out.println(fruits.add("Banana"));   // false — DUPLICATE, ignored!

        System.out.println(fruits);       // {Banana, Cherry, Apple} — NO ORDER GUARANTEED
        System.out.println(fruits.size()); // 3 (not 5!)
    }
}
```

`add()` returns `true` if the element was added, `false` if it already existed.

---

### All HashSet Methods with Examples

```java
import java.util.*;

public class HashSetMethods {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>();

        // === add(element) — add an element ===
        set.add("Apple");
        set.add("Banana");
        set.add("Cherry");
        System.out.println(set);  // {Apple, Banana, Cherry} (order may vary)

        // === size() — number of elements ===
        System.out.println(set.size());  // 3

        // === isEmpty() — check if set has no elements ===
        System.out.println(set.isEmpty());  // false

        // === contains(element) — check if element exists ===
        System.out.println(set.contains("Apple"));  // true
        System.out.println(set.contains("Mango"));  // false

        // === remove(element) — remove an element ===
        set.remove("Banana");
        System.out.println(set);  // {Apple, Cherry}

        // === addAll(collection) — add all elements from another collection ===
        Set<String> more = new HashSet<>();
        more.add("Mango");
        more.add("Grape");
        set.addAll(more);
        System.out.println(set);  // {Apple, Cherry, Mango, Grape}

        // === containsAll(collection) — check if ALL elements exist ===
        Set<String> check = new HashSet<>(Set.of("Apple", "Mango"));
        System.out.println(set.containsAll(check));  // true

        // === removeAll(collection) — remove all elements that exist in given collection ===
        set.removeAll(Set.of("Apple", "Grape"));
        System.out.println(set);  // {Cherry, Mango}

        // === retainAll(collection) — keep ONLY elements that exist in given collection ===
        set.add("Apple");
        set.add("Grape");
        // set is now: {Cherry, Mango, Apple, Grape}
        set.retainAll(Set.of("Cherry", "Apple", "Kiwi"));
        System.out.println(set);  // {Cherry, Apple} — only these were in both

        // === clear() — remove all elements ===
        set.clear();
        System.out.println(set);          // []
        System.out.println(set.isEmpty()); // true
    }
}
```

---

### Iterating Over a HashSet

Since there's no index, you CANNOT use `get(i)`. Use for-each or Iterator:

```java
Set<String> colors = new HashSet<>();
colors.add("Red");
colors.add("Green");
colors.add("Blue");

// Method 1: For-each
for (String color : colors) {
    System.out.println(color);
}

// Method 2: Iterator (use this if you need to remove during iteration)
Iterator<String> it = colors.iterator();
while (it.hasNext()) {
    String color = it.next();
    if (color.equals("Green")) {
        it.remove();  // safely remove during iteration
    }
}
System.out.println(colors);  // {Red, Blue}
```

**Warning:** The order of elements printed is NOT predictable with HashSet!

---

### Common Use Case — Removing Duplicates from a List

```java
List<Integer> listWithDuplicates = new ArrayList<>(List.of(1, 2, 3, 2, 1, 4, 3, 5, 5));
System.out.println(listWithDuplicates);  // [1, 2, 3, 2, 1, 4, 3, 5, 5]

Set<Integer> uniqueNumbers = new HashSet<>(listWithDuplicates);
System.out.println(uniqueNumbers);  // {1, 2, 3, 4, 5} — duplicates gone!

// Convert back to list if needed
List<Integer> uniqueList = new ArrayList<>(uniqueNumbers);
System.out.println(uniqueList);  // [1, 2, 3, 4, 5]
```

---

### HashSet Time Complexity

| Operation | Time Complexity | Comparison with ArrayList |
|-----------|:-:|:-:|
| `add(element)` | O(1) average | ArrayList add at end: O(1) |
| `remove(element)` | O(1) average | ArrayList remove: O(n) |
| `contains(element)` | **O(1) average** | ArrayList contains: **O(n)** |
| Iteration | O(n) | Same |

HashSet is MUCH faster than ArrayList for checking if an element exists!

---

### Important: HashSet Does NOT Maintain Order

```java
Set<Integer> set = new HashSet<>();
set.add(30);
set.add(10);
set.add(20);
set.add(50);
set.add(40);

System.out.println(set);  // could be {50, 20, 40, 10, 30} — no guaranteed order!
// NOT insertion order, NOT sorted order — unpredictable!
```

If you need order → use **LinkedHashSet** (insertion order) or **TreeSet** (sorted order).

---

## Topic 13: LinkedHashSet

### What is LinkedHashSet?

LinkedHashSet does everything HashSet does (no duplicates, fast operations) BUT also **maintains insertion order** — elements come out in the same order you added them.

Think of it as: **HashSet + remembers the order you added elements.**

---

### HashSet vs LinkedHashSet — The Difference

```java
import java.util.*;

public class LinkedHashSetDemo {
    public static void main(String[] args) {
        // HashSet — NO order guarantee
        Set<String> hashSet = new HashSet<>();
        hashSet.add("Banana");
        hashSet.add("Apple");
        hashSet.add("Cherry");
        hashSet.add("Date");
        System.out.println("HashSet:       " + hashSet);
        // Could be: {Cherry, Apple, Date, Banana} — random order!

        // LinkedHashSet — MAINTAINS insertion order
        Set<String> linkedHashSet = new LinkedHashSet<>();
        linkedHashSet.add("Banana");
        linkedHashSet.add("Apple");
        linkedHashSet.add("Cherry");
        linkedHashSet.add("Date");
        System.out.println("LinkedHashSet: " + linkedHashSet);
        // Always: {Banana, Apple, Cherry, Date} — insertion order preserved!
    }
}
```

---

### How It Works Internally

- **HashSet** = HashMap (hash table only)
- **LinkedHashSet** = HashMap + **Doubly Linked List** connecting entries in insertion order

The linked list maintains insertion order. When you iterate, it follows the linked list.

---

### Methods — Same as HashSet

LinkedHashSet has the exact same methods as HashSet. Only difference is the iteration order:

```java
import java.util.*;

public class LinkedHashSetMethods {
    public static void main(String[] args) {
        Set<Integer> set = new LinkedHashSet<>();

        set.add(30);
        set.add(10);
        set.add(20);
        set.add(50);
        set.add(40);
        set.add(10);   // duplicate — ignored
        set.add(30);   // duplicate — ignored

        System.out.println(set);          // [30, 10, 20, 50, 40] — insertion order!
        System.out.println(set.size());   // 5

        set.remove(20);
        System.out.println(set);          // [30, 10, 50, 40] — order preserved

        System.out.println(set.contains(50));  // true

        for (int num : set) {
            System.out.print(num + " ");
        }
        // Output: 30 10 50 40 (insertion order)
    }
}
```

---

### Practical Example — Tracking Unique Visitors in Order

```java
Set<String> visitors = new LinkedHashSet<>();
visitors.add("Alice");
visitors.add("Bob");
visitors.add("Alice");    // already visited — ignored
visitors.add("Charlie");
visitors.add("Bob");      // already visited — ignored
visitors.add("David");

System.out.println("Visitors in order of first visit:");
for (String visitor : visitors) {
    System.out.println("  " + visitor);
}
// Output: Alice, Bob, Charlie, David
```

---

### When to Use Which Set?

| Use Case | Which Set? |
|----------|-----------|
| Don't care about order, just need uniqueness + speed | **HashSet** |
| Need uniqueness AND insertion order preserved | **LinkedHashSet** |
| Need uniqueness AND sorted order | **TreeSet** |

---

### Performance

| Operation | HashSet | LinkedHashSet |
|-----------|:-:|:-:|
| add() | O(1) | O(1) |
| remove() | O(1) | O(1) |
| contains() | O(1) | O(1) |
| Memory | Less | Slightly more (extra linked list pointers) |
| Order | No guarantee | Insertion order |

---

## Topic 14: Internal Working of HashSet, equals() and hashCode()

### How Does HashSet Know If an Element Already Exists?

HashSet uses **two methods** together to determine if two objects are the same:
1. `hashCode()` — gives a number (hash) for the object — decides which bucket to check
2. `equals()` — checks if two objects are truly equal in content

---

### What is hashCode()?

Every object in Java has a `hashCode()` method (inherited from Object class). It returns an integer that represents the object.

```java
String s = "Apple";
System.out.println(s.hashCode());  // 63476538 (some number)

String s2 = "Banana";
System.out.println(s2.hashCode());  // 1982479237 (different number)

String s3 = "Apple";
System.out.println(s3.hashCode());  // 63476538 (SAME as s — same content, same hash)
```

**Rule:** If two objects are equal, they MUST have the same hashCode.

---

### What is equals()?

`equals()` checks if two objects are truly the same in content.

```java
String s1 = "Apple";
String s2 = "Apple";
String s3 = "Banana";

System.out.println(s1.equals(s2));  // true — same content
System.out.println(s1.equals(s3));  // false — different content
```

---

### How HashSet Uses Both Together

When you call `set.add(element)`, HashSet does this internally:

```
Step 1: Calculate hashCode() of the element
        → This tells which "bucket" (slot) to look in

Step 2: Go to that bucket. Does any existing element have the same hashCode?
        → If NO → element is NEW → add it
        → If YES → go to Step 3

Step 3: Call equals() to compare the new element with existing element(s)
        → If equals() returns true → DUPLICATE → don't add
        → If equals() returns false → hash collision (different object, same bucket) → add it
```

**Visual:**

```
HashSet internal array (buckets):

Bucket 0: []
Bucket 1: [Apple]         ← hashCode("Apple") % arraySize = 1
Bucket 2: []
Bucket 3: [Banana, Grape] ← both landed in bucket 3 (collision)
Bucket 4: [Cherry]

Adding "Apple" again:
  1. hashCode("Apple") → points to bucket 1
  2. Bucket 1 has "Apple" — same hashCode? YES
  3. "Apple".equals("Apple") → true → DUPLICATE → rejected!
```

---

### The Problem with Custom Objects

For String and Integer, hashCode() and equals() are already properly implemented. But for YOUR objects, the default behaviour compares **memory addresses** not content:

```java
class Student {
    String name;
    int rollNo;

    Student(String name, int rollNo) {
        this.name = name;
        this.rollNo = rollNo;
    }
}

Set<Student> students = new HashSet<>();
Student s1 = new Student("Pavan", 101);
Student s2 = new Student("Pavan", 101);  // same name and rollNo!

students.add(s1);
students.add(s2);
System.out.println(students.size());  // 2!! Not 1!
// HashSet thinks they are DIFFERENT — because default hashCode uses memory address
```

---

### The Fix — Override equals() and hashCode()

```java
import java.util.Objects;

class Student {
    String name;
    int rollNo;

    Student(String name, int rollNo) {
        this.name = name;
        this.rollNo = rollNo;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Student other = (Student) obj;
        return this.rollNo == other.rollNo && this.name.equals(other.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, rollNo);
    }

    @Override
    public String toString() {
        return name + " (" + rollNo + ")";
    }
}
```

Now duplicates are correctly detected:

```java
Set<Student> students = new HashSet<>();
Student s1 = new Student("Pavan", 101);
Student s2 = new Student("Pavan", 101);

students.add(s1);
students.add(s2);  // correctly identified as duplicate!

System.out.println(students.size());  // 1 ✓
```

---

### The Contract Between equals() and hashCode()

| Rule | Description |
|------|-------------|
| 1 | If `a.equals(b)` is true → `a.hashCode() == b.hashCode()` MUST be true |
| 2 | If `a.hashCode() != b.hashCode()` → `a.equals(b)` MUST be false |
| 3 | If `a.hashCode() == b.hashCode()` → `a.equals(b)` may or may not be true (collision) |

**NEVER override just one — always override both together!**

---

### What Happens If You Only Override equals() But NOT hashCode()?

```java
// hashCode NOT overridden — uses default memory address
Set<Student> set = new HashSet<>();
Student s1 = new Student("Pavan", 101);
Student s2 = new Student("Pavan", 101);

set.add(s1);
set.add(s2);
System.out.println(set.size());  // 2!! BROKEN!

// Why? s1 and s2 have different memory addresses → different hashCodes
// They land in different buckets → equals() is never even called!
```

hashCode() is the FIRST filter. If hashCodes differ, equals() is never checked.

---

### Complete Working Example

```java
import java.util.*;

class Employee {
    String name;
    int id;

    Employee(String name, int id) {
        this.name = name;
        this.id = id;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Employee other = (Employee) obj;
        return this.id == other.id && this.name.equals(other.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, id);
    }

    @Override
    public String toString() {
        return name + " (ID: " + id + ")";
    }
}

public class Main {
    public static void main(String[] args) {
        Set<Employee> employees = new HashSet<>();

        employees.add(new Employee("Pavan", 101));
        employees.add(new Employee("Raj", 102));
        employees.add(new Employee("Pavan", 101));  // duplicate — ignored!
        employees.add(new Employee("Amit", 103));
        employees.add(new Employee("Raj", 102));    // duplicate — ignored!

        System.out.println("Unique employees: " + employees.size());  // 3
        for (Employee e : employees) {
            System.out.println("  " + e);
        }
    }
}
```

Output:
```
Unique employees: 3
  Pavan (ID: 101)
  Raj (ID: 102)
  Amit (ID: 103)
```

---

### Summary

| Concept | Purpose |
|---------|---------|
| `hashCode()` | Returns an integer — fast first check to find the bucket |
| `equals()` | Compares actual content — accurate final check for duplication |
| Override both | ALWAYS override both together for custom objects in HashSet/HashMap |

---

## Topic 15: SortedSet Interface

### What is SortedSet?

`SortedSet` is a Set that keeps elements in **ascending sorted order** automatically. Every time you add an element, it goes to the correct sorted position.

```java
import java.util.*;

SortedSet<Integer> set = new TreeSet<>();
set.add(50);
set.add(10);
set.add(30);
set.add(20);
set.add(40);

System.out.println(set);  // [10, 20, 30, 40, 50] — always sorted!
```

No matter what order you add elements, they're always stored sorted.

---

### SortedSet Methods

```java
import java.util.*;

public class SortedSetDemo {
    public static void main(String[] args) {
        SortedSet<Integer> set = new TreeSet<>();
        set.add(50);
        set.add(10);
        set.add(30);
        set.add(20);
        set.add(40);
        // set = [10, 20, 30, 40, 50]

        // first() — smallest element
        System.out.println(set.first());  // 10

        // last() — largest element
        System.out.println(set.last());   // 50

        // headSet(toElement) — all elements STRICTLY LESS THAN toElement
        System.out.println(set.headSet(30));  // [10, 20] (excludes 30)

        // tailSet(fromElement) — all elements GREATER THAN OR EQUAL to fromElement
        System.out.println(set.tailSet(30));  // [30, 40, 50] (includes 30)

        // subSet(from, to) — elements in range [from, to)
        System.out.println(set.subSet(20, 40));  // [20, 30] (includes 20, excludes 40)
    }
}
```

---

## Topic 16: NavigableSet Interface

### What is NavigableSet?

`NavigableSet` extends `SortedSet` and adds **navigation methods** — find the closest element to a given value.

---

### NavigableSet Methods with Examples

```java
import java.util.*;

public class NavigableSetDemo {
    public static void main(String[] args) {
        NavigableSet<Integer> set = new TreeSet<>();
        set.add(10);
        set.add(20);
        set.add(30);
        set.add(40);
        set.add(50);
        // set = [10, 20, 30, 40, 50]

        // lower(e) — greatest element STRICTLY LESS THAN e
        System.out.println(set.lower(30));   // 20 (biggest number less than 30)
        System.out.println(set.lower(10));   // null (nothing less than 10)

        // floor(e) — greatest element LESS THAN OR EQUAL to e
        System.out.println(set.floor(30));   // 30 (30 exists, so return it)
        System.out.println(set.floor(25));   // 20 (25 doesn't exist, closest smaller is 20)

        // higher(e) — smallest element STRICTLY GREATER THAN e
        System.out.println(set.higher(30));  // 40 (smallest number greater than 30)
        System.out.println(set.higher(50));  // null (nothing greater than 50)

        // ceiling(e) — smallest element GREATER THAN OR EQUAL to e
        System.out.println(set.ceiling(30)); // 30 (30 exists, so return it)
        System.out.println(set.ceiling(25)); // 30 (25 doesn't exist, closest larger is 30)

        // pollFirst() — remove and return smallest
        System.out.println(set.pollFirst()); // 10 (removed)
        System.out.println(set);             // [20, 30, 40, 50]

        // pollLast() — remove and return largest
        System.out.println(set.pollLast());  // 50 (removed)
        System.out.println(set);             // [20, 30, 40]

        // descendingSet() — reverse order view
        System.out.println(set.descendingSet());  // [40, 30, 20]
    }
}
```

---

### NavigableSet Methods — Quick Reference

| Method | Returns | Example (set = [10,20,30,40,50]) |
|--------|---------|----------------------------------|
| `lower(30)` | Greatest element **< 30** | 20 |
| `floor(30)` | Greatest element **<= 30** | 30 |
| `higher(30)` | Smallest element **> 30** | 40 |
| `ceiling(30)` | Smallest element **>= 30** | 30 |
| `floor(25)` | Greatest element **<= 25** | 20 (25 not present) |
| `ceiling(25)` | Smallest element **>= 25** | 30 (25 not present) |
| `pollFirst()` | Remove & return smallest | 10 |
| `pollLast()` | Remove & return largest | 50 |
| `descendingSet()` | Reverse order view | [50,40,30,20,10] |

**Memory trick:**
- `lower` / `higher` → strictly less / greater (does NOT include the element itself)
- `floor` / `ceiling` → includes the element itself if it exists

---

## Topic 17: TreeSet in Java

### What is TreeSet?

`TreeSet` is the concrete class that implements `Set`, `SortedSet`, and `NavigableSet`. It's the ONLY implementation of SortedSet and NavigableSet.

**Internal structure:** Red-Black Tree (a self-balancing binary search tree).

---

### Key Characteristics

| Feature | Details |
|---------|---------|
| Ordering | Always sorted (natural order or Comparator) |
| Duplicates | Not allowed |
| Null | NOT allowed (throws NullPointerException) |
| Time: add/remove/contains | O(log n) |
| Thread-safe | No |

---

### Basic TreeSet Example

```java
import java.util.*;

public class TreeSetDemo {
    public static void main(String[] args) {
        TreeSet<Integer> numbers = new TreeSet<>();

        numbers.add(50);
        numbers.add(10);
        numbers.add(30);
        numbers.add(20);
        numbers.add(40);
        numbers.add(10);   // duplicate — ignored

        System.out.println(numbers);        // [10, 20, 30, 40, 50] — sorted!
        System.out.println(numbers.size()); // 5

        // SortedSet methods
        System.out.println(numbers.first());  // 10
        System.out.println(numbers.last());   // 50

        // NavigableSet methods
        System.out.println(numbers.lower(30));    // 20
        System.out.println(numbers.higher(30));   // 40
        System.out.println(numbers.floor(25));    // 20
        System.out.println(numbers.ceiling(25));  // 30
    }
}
```

---

### TreeSet with Strings (Sorted Alphabetically)

```java
TreeSet<String> names = new TreeSet<>();
names.add("Pavan");
names.add("Amit");
names.add("Raj");
names.add("Suresh");
names.add("Bob");

System.out.println(names);  // [Amit, Bob, Pavan, Raj, Suresh] — alphabetical!
```

---

### TreeSet with Custom Objects (Using Comparable)

The object's class must implement `Comparable`, otherwise TreeSet throws ClassCastException.

```java
class Student implements Comparable<Student> {
    String name;
    int marks;

    Student(String name, int marks) {
        this.name = name;
        this.marks = marks;
    }

    @Override
    public int compareTo(Student other) {
        if (this.marks < other.marks) return -1;
        else if (this.marks > other.marks) return 1;
        else return 0;
    }

    @Override
    public String toString() {
        return name + " (" + marks + ")";
    }
}

public class Main {
    public static void main(String[] args) {
        TreeSet<Student> students = new TreeSet<>();
        students.add(new Student("Pavan", 85));
        students.add(new Student("Raj", 72));
        students.add(new Student("Amit", 91));
        students.add(new Student("Suresh", 65));

        System.out.println(students);
        // [Suresh (65), Raj (72), Pavan (85), Amit (91)] — sorted by marks!

        System.out.println(students.first());  // Suresh (65)
        System.out.println(students.last());   // Amit (91)
    }
}
```

---

### TreeSet with Comparator (External Ordering)

If you want a different ordering without modifying the class, pass a Comparator:

```java
class NameComparator implements Comparator<Student> {
    @Override
    public int compare(Student a, Student b) {
        return a.name.compareTo(b.name);
    }
}

public class Main {
    public static void main(String[] args) {
        TreeSet<Student> byName = new TreeSet<>(new NameComparator());
        byName.add(new Student("Pavan", 85));
        byName.add(new Student("Raj", 72));
        byName.add(new Student("Amit", 91));

        System.out.println(byName);
        // [Amit (91), Pavan (85), Raj (72)] — sorted alphabetically by name!
    }
}
```

---

### TreeSet in Descending Order

```java
TreeSet<Integer> set = new TreeSet<>();
set.add(10);
set.add(40);
set.add(20);
set.add(50);
set.add(30);

// Descending view
NavigableSet<Integer> descending = set.descendingSet();
System.out.println(descending);  // [50, 40, 30, 20, 10]

// Or create with reverse comparator
TreeSet<Integer> reverseSet = new TreeSet<>(Collections.reverseOrder());
reverseSet.addAll(Set.of(10, 40, 20, 50, 30));
System.out.println(reverseSet);  // [50, 40, 30, 20, 10]
```

---

### Comparison: HashSet vs LinkedHashSet vs TreeSet

| Feature | HashSet | LinkedHashSet | TreeSet |
|---------|---------|---------------|---------|
| Order | No guarantee | Insertion order | Sorted order |
| Duplicates | No | No | No |
| Null | Allowed (one) | Allowed (one) | NOT allowed |
| Performance | O(1) | O(1) | O(log n) |
| Internal | HashMap | HashMap + LinkedList | Red-Black Tree |
| When to use | Just need unique, fastest | Unique + insertion order | Unique + sorted |

---

## Topic 18: The Map Interface, HashMap & Hashtable

### What is a Map?

A Map stores data as **key-value pairs**. Every entry has a key (the identifier) and a value (the data associated with that key).

```
Key        →    Value
─────────────────────────
"Pavan"    →    25         (name → age)
"Raj"      →    30
"Amit"     →    28
```

**Rules:**
- Every key is **unique** — no duplicate keys allowed
- Each key maps to **exactly one** value
- Values CAN be duplicated (multiple keys can have the same value)

**Real-world examples:**
- Dictionary: Word (key) → Definition (value)
- Phone book: Name (key) → Phone number (value)
- Student records: Roll number (key) → Student name (value)

---

### Map is a SEPARATE Hierarchy

Map does NOT extend Collection. It's completely independent.

```
Collection hierarchy:   Iterable → Collection → List/Set/Queue
Map hierarchy:          Map → SortedMap → NavigableMap
```

---

### How to Create a Map

```java
import java.util.*;

Map<String, Integer> ages = new HashMap<>();      // Key=String, Value=Integer
Map<Integer, String> idToName = new HashMap<>();  // Key=Integer, Value=String
```

---

### All Map Methods with Examples

```java
import java.util.*;

public class MapDemo {
    public static void main(String[] args) {
        Map<String, Integer> ages = new HashMap<>();

        // === put(key, value) — add or update a key-value pair ===
        ages.put("Pavan", 25);
        ages.put("Raj", 30);
        ages.put("Amit", 28);
        System.out.println(ages);  // {Pavan=25, Raj=30, Amit=28}

        // If key already exists, put() UPDATES the value and returns OLD value
        Integer oldValue = ages.put("Pavan", 26);
        System.out.println(oldValue);  // 25 (old value returned)
        System.out.println(ages);      // {Pavan=26, Raj=30, Amit=28}

        // === get(key) — retrieve value by key ===
        System.out.println(ages.get("Raj"));    // 30
        System.out.println(ages.get("Suresh")); // null (key doesn't exist)

        // === getOrDefault(key, defaultValue) — get value or return default ===
        System.out.println(ages.getOrDefault("Raj", 0));     // 30 (exists)
        System.out.println(ages.getOrDefault("Suresh", 0));  // 0 (doesn't exist)

        // === containsKey(key) — check if key exists ===
        System.out.println(ages.containsKey("Pavan"));   // true
        System.out.println(ages.containsKey("Suresh"));  // false

        // === containsValue(value) — check if value exists ===
        System.out.println(ages.containsValue(30));  // true
        System.out.println(ages.containsValue(99));  // false

        // === size() — number of key-value pairs ===
        System.out.println(ages.size());  // 3

        // === isEmpty() — check if map has no entries ===
        System.out.println(ages.isEmpty());  // false

        // === remove(key) — remove a key-value pair, returns the value ===
        Integer removed = ages.remove("Amit");
        System.out.println(removed);  // 28
        System.out.println(ages);     // {Pavan=26, Raj=30}

        // === putIfAbsent(key, value) — add only if key doesn't already exist ===
        ages.putIfAbsent("Raj", 99);    // Raj exists → NOT updated
        ages.putIfAbsent("Suresh", 35); // Suresh doesn't exist → added
        System.out.println(ages);  // {Pavan=26, Raj=30, Suresh=35}

        // === keySet() — get all keys as a Set ===
        Set<String> keys = ages.keySet();
        System.out.println(keys);  // [Pavan, Raj, Suresh]

        // === values() — get all values as a Collection ===
        Collection<Integer> values = ages.values();
        System.out.println(values);  // [26, 30, 35]

        // === entrySet() — get all key-value pairs ===
        Set<Map.Entry<String, Integer>> entries = ages.entrySet();
        for (Map.Entry<String, Integer> entry : entries) {
            System.out.println(entry.getKey() + " → " + entry.getValue());
        }
        // Pavan → 26
        // Raj → 30
        // Suresh → 35

        // === clear() — remove all entries ===
        ages.clear();
        System.out.println(ages);  // {}
    }
}
```

---

### Iterating Over a Map

```java
Map<String, Integer> ages = new HashMap<>();
ages.put("Pavan", 25);
ages.put("Raj", 30);
ages.put("Amit", 28);

// Method 1: Iterate over keys, then get value
for (String key : ages.keySet()) {
    System.out.println(key + " = " + ages.get(key));
}

// Method 2: Iterate over entrySet (PREFERRED — no extra lookup)
for (Map.Entry<String, Integer> entry : ages.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}

// Method 3: Iterate over values only
for (Integer value : ages.values()) {
    System.out.println(value);
}
```

Method 2 is preferred because entrySet() gives both key and value directly without an extra `get()` call.

---

### HashMap vs Hashtable

| Feature | HashMap | Hashtable |
|---------|---------|-----------|
| Null keys | Allows ONE null key | NOT allowed |
| Null values | Allows multiple null values | NOT allowed |
| Synchronized | No (not thread-safe) | Yes (thread-safe, but slow) |
| Performance | Faster | Slower (locking overhead) |
| When to use | Always (modern code) | Legacy only — don't use in new code |

Always use HashMap. For thread-safety use `ConcurrentHashMap` instead of Hashtable.

---

### Practical Example — Counting Word Frequency

```java
import java.util.*;

public class WordCount {
    public static void main(String[] args) {
        String sentence = "apple banana apple cherry banana apple";
        String[] words = sentence.split(" ");

        Map<String, Integer> frequency = new HashMap<>();

        for (String word : words) {
            frequency.put(word, frequency.getOrDefault(word, 0) + 1);
        }

        System.out.println(frequency);
        // {apple=3, banana=2, cherry=1}
    }
}
```

---

### Practical Example — Adjacency List for Graph (DSA)

In graph problems, you represent a graph as an adjacency list using HashMap:

```java
import java.util.*;

public class GraphDemo {
    public static void main(String[] args) {
        // Graph: 1 connects to [2,3], 2 connects to [1,4], etc.
        Map<Integer, List<Integer>> graph = new HashMap<>();

        graph.put(1, new ArrayList<>(List.of(2, 3)));
        graph.put(2, new ArrayList<>(List.of(1, 4)));
        graph.put(3, new ArrayList<>(List.of(1)));
        graph.put(4, new ArrayList<>(List.of(2)));

        // Print adjacency list
        for (Map.Entry<Integer, List<Integer>> entry : graph.entrySet()) {
            System.out.println("Node " + entry.getKey() + " → " + entry.getValue());
        }
        // Node 1 → [2, 3]
        // Node 2 → [1, 4]
        // Node 3 → [1]
        // Node 4 → [2]

        // Get neighbors of node 1
        List<Integer> neighbors = graph.get(1);
        System.out.println("Neighbors of 1: " + neighbors);  // [2, 3]
    }
}
```

---

## Topic 19: SortedMap Interface

### What is SortedMap?

A Map that keeps its **keys in ascending sorted order** automatically.

```java
import java.util.*;

SortedMap<String, Integer> map = new TreeMap<>();
map.put("Cherry", 3);
map.put("Apple", 1);
map.put("Banana", 2);

System.out.println(map);  // {Apple=1, Banana=2, Cherry=3} — keys sorted alphabetically!
```

---

### SortedMap Methods

```java
import java.util.*;

public class SortedMapDemo {
    public static void main(String[] args) {
        SortedMap<Integer, String> map = new TreeMap<>();
        map.put(50, "Fifty");
        map.put(10, "Ten");
        map.put(30, "Thirty");
        map.put(20, "Twenty");
        map.put(40, "Forty");
        // map = {10=Ten, 20=Twenty, 30=Thirty, 40=Forty, 50=Fifty}

        // firstKey() — smallest key
        System.out.println(map.firstKey());  // 10

        // lastKey() — largest key
        System.out.println(map.lastKey());   // 50

        // headMap(toKey) — all entries with keys STRICTLY LESS THAN toKey
        System.out.println(map.headMap(30));  // {10=Ten, 20=Twenty}

        // tailMap(fromKey) — all entries with keys GREATER THAN OR EQUAL to fromKey
        System.out.println(map.tailMap(30));  // {30=Thirty, 40=Forty, 50=Fifty}

        // subMap(fromKey, toKey) — entries with keys in range [from, to)
        System.out.println(map.subMap(20, 40));  // {20=Twenty, 30=Thirty}
    }
}
```

---

## Topic 20: NavigableMap Interface

### What is NavigableMap?

Extends SortedMap with **navigation methods** — find the closest entry to a given key.

---

### NavigableMap Methods with Examples

```java
import java.util.*;

public class NavigableMapDemo {
    public static void main(String[] args) {
        NavigableMap<Integer, String> map = new TreeMap<>();
        map.put(10, "Ten");
        map.put(20, "Twenty");
        map.put(30, "Thirty");
        map.put(40, "Forty");
        map.put(50, "Fifty");
        // map = {10=Ten, 20=Twenty, 30=Thirty, 40=Forty, 50=Fifty}

        // lowerEntry(key) — entry with greatest key STRICTLY LESS THAN key
        System.out.println(map.lowerEntry(30));   // 20=Twenty
        System.out.println(map.lowerKey(30));     // 20

        // floorEntry(key) — entry with greatest key LESS THAN OR EQUAL to key
        System.out.println(map.floorEntry(30));   // 30=Thirty (30 exists)
        System.out.println(map.floorEntry(25));   // 20=Twenty (25 doesn't exist)

        // higherEntry(key) — entry with smallest key STRICTLY GREATER THAN key
        System.out.println(map.higherEntry(30));  // 40=Forty
        System.out.println(map.higherKey(30));    // 40

        // ceilingEntry(key) — entry with smallest key GREATER THAN OR EQUAL to key
        System.out.println(map.ceilingEntry(30)); // 30=Thirty (30 exists)
        System.out.println(map.ceilingEntry(25)); // 30=Thirty (25 doesn't exist)

        // pollFirstEntry() — remove and return entry with smallest key
        System.out.println(map.pollFirstEntry()); // 10=Ten (removed)
        System.out.println(map);  // {20=Twenty, 30=Thirty, 40=Forty, 50=Fifty}

        // pollLastEntry() — remove and return entry with largest key
        System.out.println(map.pollLastEntry());  // 50=Fifty (removed)
        System.out.println(map);  // {20=Twenty, 30=Thirty, 40=Forty}

        // descendingMap() — reverse order view
        System.out.println(map.descendingMap());  // {40=Forty, 30=Thirty, 20=Twenty}
    }
}
```

---

### NavigableMap Methods — Quick Reference

| Method | Returns | Example (keys = [10,20,30,40,50]) |
|--------|---------|-----------------------------------|
| `lowerEntry(30)` | Entry with greatest key **< 30** | 20=Twenty |
| `floorEntry(30)` | Entry with greatest key **<= 30** | 30=Thirty |
| `higherEntry(30)` | Entry with smallest key **> 30** | 40=Forty |
| `ceilingEntry(30)` | Entry with smallest key **>= 30** | 30=Thirty |
| `pollFirstEntry()` | Remove & return smallest entry | 10=Ten |
| `pollLastEntry()` | Remove & return largest entry | 50=Fifty |
| `descendingMap()` | Reverse order view | {50, 40, 30, 20, 10} |

---

## Topic 21: TreeMap in Java

### What is TreeMap?

`TreeMap` is the concrete class that implements `Map`, `SortedMap`, and `NavigableMap`. It keeps keys in **sorted order** using a Red-Black Tree.

---

### Key Characteristics

| Feature | Details |
|---------|---------|
| Key ordering | Always sorted (natural order or Comparator) |
| Duplicate keys | Not allowed |
| Null keys | NOT allowed (throws NullPointerException) |
| Null values | Allowed |
| Time: put/get/remove | O(log n) |
| Thread-safe | No |

---

### Basic TreeMap Example

```java
import java.util.*;

public class TreeMapDemo {
    public static void main(String[] args) {
        TreeMap<String, Integer> scores = new TreeMap<>();

        scores.put("Pavan", 85);
        scores.put("Amit", 91);
        scores.put("Raj", 72);
        scores.put("Suresh", 65);
        scores.put("Bob", 88);

        // Keys always sorted alphabetically
        System.out.println(scores);
        // {Amit=91, Bob=88, Pavan=85, Raj=72, Suresh=65}

        // SortedMap methods
        System.out.println(scores.firstKey());  // Amit
        System.out.println(scores.lastKey());   // Suresh

        // NavigableMap methods
        System.out.println(scores.lowerKey("Pavan"));    // Bob
        System.out.println(scores.higherKey("Pavan"));   // Raj
        System.out.println(scores.floorKey("Charlie"));  // Bob (Charlie not present)
        System.out.println(scores.ceilingKey("Charlie")); // Pavan
    }
}
```

---

### TreeMap with Integer Keys

```java
TreeMap<Integer, String> map = new TreeMap<>();
map.put(300, "Three Hundred");
map.put(100, "One Hundred");
map.put(500, "Five Hundred");
map.put(200, "Two Hundred");
map.put(400, "Four Hundred");

System.out.println(map);
// {100=One Hundred, 200=Two Hundred, 300=Three Hundred, 400=Four Hundred, 500=Five Hundred}

System.out.println(map.headMap(300));  // {100=One Hundred, 200=Two Hundred}
System.out.println(map.tailMap(300));  // {300=Three Hundred, 400=Four Hundred, 500=Five Hundred}
```

---

### TreeMap with Custom Comparator (Reverse Order)

```java
TreeMap<Integer, String> reverseMap = new TreeMap<>(Collections.reverseOrder());
reverseMap.put(10, "Ten");
reverseMap.put(30, "Thirty");
reverseMap.put(20, "Twenty");

System.out.println(reverseMap);  // {30=Thirty, 20=Twenty, 10=Ten} — descending!
```

---

### Comparison: HashMap vs LinkedHashMap vs TreeMap

| Feature | HashMap | LinkedHashMap | TreeMap |
|---------|---------|--------------|---------|
| Key order | No guarantee | Insertion order | Sorted order |
| Null keys | Allowed (one) | Allowed (one) | NOT allowed |
| Performance | O(1) | O(1) | O(log n) |
| Internal | Hash table | Hash table + LinkedList | Red-Black Tree |
| When to use | Fast lookup, don't care about order | Need insertion order | Need keys sorted |

---

## Topic 22: Sorting an Array and a List

### Sorting Arrays — Using Arrays.sort()

`java.util.Arrays` provides `sort()` for arrays.

#### Sorting Primitive Arrays (Ascending)

```java
import java.util.Arrays;

public class ArraySortDemo {
    public static void main(String[] args) {
        int[] numbers = {50, 10, 40, 20, 30};

        Arrays.sort(numbers);
        System.out.println(Arrays.toString(numbers));  // [10, 20, 30, 40, 50]
    }
}
```

#### Sorting a Specific Range in Array

```java
int[] nums = {50, 40, 30, 20, 10};
Arrays.sort(nums, 1, 4);  // sort only index 1, 2, 3 (fromIndex inclusive, toIndex exclusive)
System.out.println(Arrays.toString(nums));  // [50, 20, 30, 40, 10]
```

#### Sorting String Arrays

```java
String[] names = {"Pavan", "Amit", "Raj", "Suresh", "Bob"};
Arrays.sort(names);
System.out.println(Arrays.toString(names));  // [Amit, Bob, Pavan, Raj, Suresh]
```

#### Sorting in Descending Order (Object Arrays Only)

`Collections.reverseOrder()` does NOT work with primitive arrays (`int[]`). You must use wrapper arrays (`Integer[]`):

```java
Integer[] numbers = {50, 10, 40, 20, 30};  // Integer[], not int[]
Arrays.sort(numbers, Collections.reverseOrder());
System.out.println(Arrays.toString(numbers));  // [50, 40, 30, 20, 10]

String[] names = {"Pavan", "Amit", "Raj", "Suresh", "Bob"};
Arrays.sort(names, Collections.reverseOrder());
System.out.println(Arrays.toString(names));  // [Suresh, Raj, Pavan, Bob, Amit]
```

#### Sorting with Custom Comparator

```java
class LengthComparator implements Comparator<String> {
    @Override
    public int compare(String a, String b) {
        if (a.length() < b.length()) return -1;
        else if (a.length() > b.length()) return 1;
        else return 0;
    }
}

String[] names = {"Pavan", "Amit", "Raj", "Suresh", "Bob"};
Arrays.sort(names, new LengthComparator());
System.out.println(Arrays.toString(names));  // [Raj, Bob, Amit, Pavan, Suresh] — by length
```

---

### Sorting Lists — Using Collections.sort()

`java.util.Collections` provides `sort()` for Lists.

#### Sorting a List in Ascending Order

```java
import java.util.*;

List<Integer> numbers = new ArrayList<>(List.of(50, 10, 40, 20, 30));
Collections.sort(numbers);
System.out.println(numbers);  // [10, 20, 30, 40, 50]
```

#### Sorting a List in Descending Order

```java
List<Integer> numbers = new ArrayList<>(List.of(50, 10, 40, 20, 30));
Collections.sort(numbers, Collections.reverseOrder());
System.out.println(numbers);  // [50, 40, 30, 20, 10]
```

#### Sorting a List of Strings

```java
List<String> names = new ArrayList<>(List.of("Pavan", "Amit", "Raj", "Suresh"));

Collections.sort(names);  // alphabetical ascending
System.out.println(names);  // [Amit, Pavan, Raj, Suresh]

Collections.sort(names, Collections.reverseOrder());  // descending
System.out.println(names);  // [Suresh, Raj, Pavan, Amit]
```

#### Sorting Custom Objects in a List (Using Comparable)

```java
class Student implements Comparable<Student> {
    String name;
    int marks;

    Student(String name, int marks) {
        this.name = name;
        this.marks = marks;
    }

    @Override
    public int compareTo(Student other) {
        if (this.marks < other.marks) return -1;
        else if (this.marks > other.marks) return 1;
        else return 0;
    }

    @Override
    public String toString() {
        return name + "(" + marks + ")";
    }
}

public class Main {
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>();
        students.add(new Student("Pavan", 85));
        students.add(new Student("Raj", 72));
        students.add(new Student("Amit", 91));
        students.add(new Student("Suresh", 65));

        // Sort using natural ordering (Comparable — by marks)
        Collections.sort(students);
        System.out.println(students);
        // [Suresh(65), Raj(72), Pavan(85), Amit(91)]
    }
}
```

#### Sorting Custom Objects with External Comparator

```java
class NameComparator implements Comparator<Student> {
    @Override
    public int compare(Student a, Student b) {
        return a.name.compareTo(b.name);
    }
}

Collections.sort(students, new NameComparator());
System.out.println(students);
// [Amit(91), Pavan(85), Raj(72), Suresh(65)] — alphabetical by name
```

---

### Arrays.sort() vs Collections.sort()

| Feature | Arrays.sort() | Collections.sort() |
|---------|---------------|-------------------|
| Works on | Arrays (`int[]`, `String[]`) | Lists (`ArrayList`, `LinkedList`) |
| Package | `java.util.Arrays` | `java.util.Collections` |
| Primitive types | Yes (`int[]`, `double[]`) | No (Lists use wrapper types) |
| Descending | Only with Object arrays + Comparator | With Comparator |
| Custom order | Pass Comparator | Pass Comparator |

---

### Summary — How to Sort in Java

| What to sort | Ascending | Descending |
|---|---|---|
| `int[]` | `Arrays.sort(arr)` | Sort ascending, then reverse manually |
| `Integer[]` | `Arrays.sort(arr)` | `Arrays.sort(arr, Collections.reverseOrder())` |
| `String[]` | `Arrays.sort(arr)` | `Arrays.sort(arr, Collections.reverseOrder())` |
| `List<Integer>` | `Collections.sort(list)` | `Collections.sort(list, Collections.reverseOrder())` |
| `List<CustomObj>` | `Collections.sort(list)` (needs Comparable) | `Collections.sort(list, comparator)` |

---

## ALL TOPICS COMPLETE!

### Final Checklist — Java Collections Framework

All 22 topics covered from scratch with examples:
1. Generics
2. Collections Core Interfaces
3. Collections Concrete Classes
4. Need of Iterators
5. Iterable and Iterator Interface
6. Collection Interface
7. Lists (ArrayList, Vector, LinkedList) + Iteration Methods
8. Queue Interface
9. Deque Interface (Stack, ArrayDeque, Browser History)
10. Priority Queue
11. Comparable Interface
12. Comparator Interface
13. Set Interface & HashSet
14. LinkedHashSet
15. Internal Working of HashSet (equals & hashCode)
16. SortedSet Interface
17. NavigableSet Interface
18. TreeSet
19. Map Interface, HashMap & Hashtable
20. Adjacency List with HashMap
21. SortedMap, NavigableMap & TreeMap
22. Sorting Arrays and Lists

---

*Last updated: All Topics Complete — Java Collections Framework Mastered!*
