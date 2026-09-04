- Templating
	- happens at compile time
- Move semantics
	- fast way to move data from point a to b
	- Copy & Move
	- Copy: 
		- var1 -> array:
		- var2 ->
	- Move:
		- "Move on Construction"
		- "Move on Assignment", where there is already data
		- i.e.,
		- ```
		  int* a;
		  A(int* val) {a = val}
		  ...
		  ```
		- use std::move(), casts the passed in value to an xvalue
		- ```
		  array[index] = std::move(value_to_move)
		  ```
- Iterators
	- wrappers around pointer that provides functionality
	- What is the point of iterators