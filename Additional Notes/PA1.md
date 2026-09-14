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