- Rules
	- new -> delete, new[] -> delete[]
		- mismatching them is undefined behavior
	- The pointer lives on the stack
		- Even when it points to the heap
		- new puts the pointee on the heap
		- the variable holding the address is a local
	- new T(5) does not equal new T[5]
		- parenthesis = one object initialized to 5
		- brackets = an array of 5, uninitialized
	- * binds looser than postfix **
		- ****p++ is *(p++)
			- it moves the pointer
		- (****p)++
			- changes the value
	- Losing an address = leaking it
		- reassigning the only pointer to a heap block makes that block unreachable and undeletable
	- Copying a pointer does not copy the object
		- After ap = bp there is still one heap object with two names
		- writing through either is visible both
	- An array argument decays
		- void f(int A[], int n) really takes int*
		- that is why n must be passed separately
	- & means two things
		- in declaration, double &&, it is a reference
		- in an expression, &, it is "address of"
	- A reference is bound once
		- it must be initialized
		- cannot be reseated, cannot be null, no arithmetic
Pointers Quiz:
- A1) Object on the stack, and a stack array
	Define an object my_car of type Car and a stack array car_array of size 10 and of type Car
	- ```
	Car my_car;
	Car car_array[10];
	```
- A2) Pointer to a heap
	- Define a pointer car_p to an object Car located on the heap (use new operator)
	- ```
	Car* car_p = new Car
	car_p -> price = 25000.0
	delete car_p;
	car_p = nullptr;
	```
- A3) Pointer to a heap array of 10
	- Define a pointer car_10 to an array of 10 elements of type Car on the heap.
	- ```
	  Car* car_10 = new Car[10];
	  car_10[0] = 77;
	  delete[] car_10; // one delete for the whole block
	  ```
- A4) A vector of 10
	- Define a vector car_vect of type Car with 10 elements
	- ```
	  #include <vector>
	  vector<Car> car_vect(10); // 10 default-constructed Cars
	  vector<Car> car_vect2(10, Car(1, 2.0)); // 10 copies of one Car
	  ```
- A5) A vector of 10 pointers to Car
	- Define a vector car_vect_p of 10 pointers to type Car.
	- ```
	  #include <vector>
	  vector<Car*> car_vect_p(10, nullptr); // 10 pointers, none pointing to anywhere
	  
	  for (Car*& p : car_vect_p) p = new Car;
	  
	  for (Car* p : car_vect_p) delete p;
	  ```
Pointers an Arrays Quiz:
