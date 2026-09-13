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