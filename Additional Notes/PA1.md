Places that allocate memory:
- explicit Vector(size_t count)
- Vector(size_t count, const T& value)
- Copy constructor
- Copy assignment
- grow()
Places that free memory, all delete[] array:
- ~Vector()
- Copy assignment (releasing old block before taking the new)
- Move assignment (same)
- grow() (releasing the old block after transferrign)

The move constructor does neither allocate OR free memory; the move constructor takes an existing block and hands over responsibility

Every allocated block has exactly one owner, and is freed once
- grow(), copy assignment, move assignment: all delete[] the old block before reassigning
- the constructors: array held uninitialized garbage, so nothing to free

''' address = array + (pos × sizeof(T)) '''
pure arithmetic done when you do array[pos], no walking. same three operations done for index of 3 or index 999,998. So operator[] is 0(1).

void push_back(const T& value) {
    if (_size == _capacity) {
        grow();
    }
    array[_size] = value;
    _size++;
}

single push_back, not including grow, is O(n), grow()'s work is log base 2 of n

// insert
for (size_t i = _size; i > index; i--) {
    array[i] = std::move(array[i - 1]);
}

Inserting at the front, loop runs _size_ times, 1,000 moves. O(n)
Inserting at the back, index == _size_, so condition i > index is false immediately, zero moves. O(1).

## Vector operation complexity

| Operation                           | Complexity     | Why                                                                                                                                |
| ----------------------------------- | -------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `size`, `capacity`, `empty`         | O(1)           | Reads a stored counter                                                                                                             |
| `operator[]`, `at`, `front`, `back` | O(1)           | Address arithmetic: `array + pos * sizeof(T)`                                                                                      |
| `push_back`, `pop_back`             | O(1) amortized | Usually just writes a slot; reallocates only at powers of two (log n times), and the total copying across all reallocations is ~2n |
| `insert`, `erase` (front or middle) | O(n)           | Must shift every element after the position                                                                                        |
| `insert`, `erase` (at the back)     | O(1)           | Nothing to shift                                                                                                                   |
| Copy constructor, copy assignment   | O(n)           | Allocates a new block and copies every element                                                                                     |
| Move constructor, move assignment   | O(1)           | Steals the pointer; elements are never touched                                                                                     |
| `clear`                             | O(1)           | Sets `_size = 0`; buffer and capacity unchanged                                                                                    |
| `grow` (internal)                   | O(n)           | Moves every existing element to the new block                                                                                      |
copy = duplicate data
move = transfer ownership



Blanking other.array to nullptr fixes double freeing because delete[] nullptr is defined to do nothing. 

Overall, assignment cleans up first. Construction has nothing to clean.



# `Vector<T>` — Operation Complexity

n = number of elements (`_size`)

## Accessors — all O(1)

| Operation | Complexity | Why |
|---|---|---|
| `size()` | O(1) | Returns the stored `_size` counter |
| `capacity()` | O(1) | Returns the stored `_capacity` counter |
| `empty()` | O(1) | One comparison: `_size == 0` |
| `operator[](pos)` | O(1) | Address arithmetic: `array + pos * sizeof(T)` |
| `at(pos)` | O(1) | Same, plus one bounds comparison |
| `front()` | O(1) | `array[0]` |
| `back()` | O(1) | `array[_size - 1]` |
| `begin()` / `end()` | O(1) | Builds an iterator from `array` / `array + _size` |

## Modifiers

| Operation | Complexity | Why |
|---|---|---|
| `push_back(value)` | **O(1) amortized** | Usually one write + counter bump. Reallocates only when full — at powers of two, so ~log2(n) times over n pushes, and ~2n total element moves. Individual worst case is O(n). |
| `pop_back()` | O(1) | `_size--`. No shifting, no freeing |
| `clear()` | O(1) | `_size = 0`. Buffer and capacity untouched |
| `insert(pos, value)` — at back | O(1) | Nothing to shift |
| `insert(pos, value)` — front/middle | **O(n)** | Every element at and after `pos` shifts one slot right |
| `insert(pos, count, value)` | **O(n + count)** | Shift, then fill `count` slots |
| `erase(pos)` — at back | O(1) | Nothing to shift |
| `erase(pos)` — front/middle | **O(n)** | Every element after `pos` shifts one slot left |
| `erase(first, last)` | **O(n)** | Same shift, by `count` positions |
| `grow()` (private) | **O(n)** | Allocates a new block and moves every element into it |

## Rule of five

| Operation | Complexity | Allocates? | Frees? |
|---|---|---|---|
| `Vector()` | O(1) | no | no |
| `Vector(count)` | O(n) | yes (1) | no |
| `Vector(count, value)` | O(n) | yes (1) | no |
| Copy constructor | **O(n)** | yes (1) | no |
| Move constructor | **O(1)** | no | no |
| Copy assignment | **O(n)** | yes (1) | yes (1) |
| Move assignment | **O(1)** | no | yes (1) |
| `~Vector()` | O(1) | no | yes (1) |

**Rule:** assignments free the block they already hold; constructors don't (a new object holds nothing). Copy duplicates the data; move steals the pointer and blanks the source.

### Which function runs?

Two questions, in order:

1. **Type on the left?** `Vector<int> b = a;` is construction. `b = a;` is assignment. The `=` is not the tell.
2. **Temporary on the right?** `a` (has a name) is a copy. `std::move(a)` or `makeVec()` is a move.

|  | copy (`const&`) | move (`&&`) |
|---|---|---|
| **construct** | copy ctor | move ctor |
| **assign** | copy assign | move assign |

## Iterator operations — all O(1)

`*it`, `it->`, `++it`, `it++`, `--it`, `it--`, `it += n`, `it + n`, `it -= n`, `it - n`, `it1 - it2`, `it[n]`, and all six comparisons.

All are pointer arithmetic or a single comparison on `_ptr` — none depend on n. `it1 - it2` gives the number of elements between them, so `end() - begin() == size()`.

## Iterator invalidation

| Operation | Invalidates? |
|---|---|
| `push_back` | Only if it reallocates (i.e. was full) |
| `insert` | Yes — at and after the position; all of them if it reallocates |
| `erase` | Yes — at and after the position |
| `clear` | Yes — all |
| `pop_back` | The iterator to the removed element |
| Accessors, `size`, `capacity` | No |

**Why:** `grow()` allocates a new block and frees the old one. An iterator is a snapshot of an address, with no link back to the container — after reallocation it points into freed memory.

## Traps

| Trap | What goes wrong |
|---|---|
| `pos > _size - 1` in `at()` | `_size` is unsigned; `0 - 1` wraps to a huge number, so the check passes on an empty vector |
| `array[_size] = value` in `push_back(T&&)` | `value` is named, so it's an lvalue and this copies. Needs `std::move(value)` |
| Forward loop in `insert` | Shifting right must count **down**; forward smears one value across the range |
| Backward loop in `erase` | Shifting left must count **up**, for the same reason |
| No self-assignment guard | `v = v` frees the block, then copies from it |
| No `temp` in `grow()` | Overwrites `array`, leaking the old block and freeing the new one |

**One rule behind most of them:** before you overwrite `array`, account for what it currently points at.