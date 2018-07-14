# C++ 
**List initialization {} vs parenthesis ()**

*List initialization does not allow narrowing:*

* An integer cannot be converted to another integer that cannot hold its value.
For example, char to int is allowed, but not int to char.
* A floating-point value cannot be converted to another floating-point type 
that cannot hold its value.
For example, float to double is allowed, but not double to float.
* A floating-point value cannot be converted to an integer type.
* An integer value cannot be converted to a floating-point type.

___

**variables**


The smallest size of a datatype is 1 byte. You can check
the size with sizeof.

```
std::cout << sizeof(bool) << std::endl;

```

___

**char**

The compiler reads **" "** as a collection of chars(*const char* *) and **' '**
as a char. Printing the *const char pointer* without dereferencing it prints
the whole string.

___

**apostrophes in number**

1'000'000 is the same as 1000000, they do nothing but makes it easier to read.

___

**long**

Has a trailing L if it isn't declared as long already - 3L.

```
float a = 2.2; //double(8bytes)
float b = 2.2f; //float(4bytes)
```

___

**casting**

Don't use roundbracket casts. No warnings:

	double a = 2; int b = static_cast<int>(a); 

___

**Constructor**

Constructor initializers makes sure the member variables are initialized to the 
specified values, instead of initializing to default values and then overwritten
to the new values.

If you don't declare any constructors the compiler will do it for you,
but as soon as you initialize one, you need to declare one for each
of the ways you want to be able to initialize an object.
	
	Person(std::string name, std::string lastname);
	Person()=default; //doesnt do anything

___

**struct**

Struct has all its member variables default to public. Use to represent
a bunch of data for easy access.

___

**namespaces**

***Never ever*** use namespaces in header files

	using std::cout; //cout
	using std::endl; //endl
	using std::string; //string
	using namespace std; //cout and endl

___

**Inheritance**

Inside the subclass you only add new member variables/functions or overrides.
	
	class Car : public Vehicle {};

Use the baseclass constructor + initialize extra variables directly.

	Car::Car(int wheels, int weight, std::string cartype) 
	: Vehicle(wheels, weight), type(cartype) {} 

Base class constructor runs first, then the subclass constructor,
then the *subclass destructor* runs followed by the *base class destructor*.

___

**Enums**

Scoped enums are better than regular enums because you can use the fully
qualified names without conflicting with code elsewhere in the program.

	enum class Connection { connected, disconmected };
	Connection c = Connection::connected;

___

**struct**

All variables and methods default to public. Good for containing
data you want to use later.

___

**immediate if**

	std::string var = x>0? "Positive" : "Negative";

___

**switch**

Sometimes you want to use an object to check some member variable,
but you dont want it to be in scope after the switch.

	switch(Foo test = afunction(); test.getNumber())
	case 1:
		break;

___

**Error messages**

Compiler error, usually in header files (declaration).
Linker error, usually in cpp files (implementation).

___

**increment/decrement**

	i++; // return the old value, then increments
	++i; // increments and return the new value

___

**comparisons**

	if(bar = function("hello"))
		//this code runs unless bar is set to false/zero

	if(hello() && world())
		//hello runs before world, if hello() is false then world() wont run

	if(hello() || world())
		//hello() runs first, if true then world() wont be called at all

___

**Bitwise operators**

	0100 & 0011 == 0000; //AND
	0100 | 0011 == 0111; //OR
	0100 ^ 0111 == 0011; //XOR

	4 >> 1 == 8; //All bits 1 pos to the right
	4 << 1 == 2; //All bits 1 pos to the left

___

**Operator overloads**

	//MyObject < OtherObject
	//member function, takes same type
	bool MyClass::operator<(OtherType obj) const; 

	//OtherObject < MyObject
	//free function, different types (friend?).
	bool operator<(OtherObject const& obj, MyClass const& obj2) const; 

	/*To use a get() function from a const reference the get method also must be
	const.
	Friend functions cannot access private variables on references (use getX()).
	Add this to private: in MyClass and now the friend function can access 
	member variables.*/
	friend bool operator<(int i, MyClass const& obj); 

___

**Templates**

Resolved at compile time (faster, no runtime checks). 

*Template function:*

	template <class T>
	T biggest(T const& t1, T const& t2) //const ref makes it usable on literals
	{
		return t1 < t2? t2 : t1;
	}

	biggest<double>(2, 2.2); //forces template to return a double

*Template class:*

	template <class T>
	class Increment
	{
	private:
		T total;
	public:
		Increment(T t) : total(t) {};
		T operator +=(T const& t)
			{return total = total + t; //sums the object before returning };
		T getTotal() const {return total;}
	};

*Template specialization:*

	template <>
	class Increment<Employee>
	{
	private:
		int total;
	public:
		Increment(int i) : total(i) {};
		int operator +=(Employee const& e)
			{return total = total + e.getSalary(); //sums the object before returning };
		int getTotal() const {return total;}
	};

It's up to you to make sure your types work with the templates (read the code).
Change *your* class if it doesn't work

***or***

access the template code and add a template specialization.

___


**References**

A reference is an alias for something else. Cannot be changed once its set. 
Cannot be declared without setting it to a value.

	Car a("Ford");
	Car& aRef = a; //must point to something
	aRef.getName() == a.getName(); //true

___

**Pointers**

A pointer is an alias for something else. It can be declared and point to nothing.
It can be changed to point to something else than what its pointing to.
Don't dereference a pointer that has not been initialized (initialize with 
nullptr, easier to track down error).

	Car* point = nullptr; Car a("Ford");
	point = &a;
	std::string carname = (*point).getName(); //or point->getName();
	int* pBad; *int pBad = 22; //dont do this

	int * const cPointer //cant be changed to point elsewhere.
	int const* cPointer //cant change the value of what its pointing to.

___

**const**

A function declared const **can't** call a function that's not const.
Mark as const last/after the datatype.

	void read(Car const& c); //function promises not to change anything in Car c.
	std::string getName() const; //Car's getName() method promises it wont change any of its member variables.
	int const a = 1; int& changableA = a; // error
	
	Car getMilage() {return milage;} // needs to be const
	Car const a("Ford");
	a.getMilage(); //error const object call to non-const method
___

**Memory Management**

The free store is for variables that lives longer (past its scope).
Variables on the free store is created on the heap (instead of the stack).
Make sure you're not is a position where some line of code makes the 
program skip the 'delete obj'. 

	Car* a = new Car("Ford"); //created on the heap with 'new' with a pointer to it.
	delete a; //frees up the memory and calls destructor, no memory leak

Local vs Heap:

	int main()
	{
		Car* a = new Car("BMW");
		{
			Motorcycle("Honda");
		}// ~Motorcycle() runs here, out of scope
		
		delete a; // ~Car() runs here, memory freed up
	}

Creating errors with pointers and the heap:

	int main()
	{
		Car* a = new Car("Ford");
		Car* b = a; // Both point to the Ford car
		
		delete b; // ~Car() runs, memory is cleaned up and the car doesnt exist
		b = nullptr;
		delete b; // Doesn't crash the program because of the nullptr.
		a->getName(); // Error, the car doesn't exist.
	    delete a; // Error, you're deleting the memory of where Ford was, 
				  // but now theres something else there you deleted.
	}

How to fix, not remembering to delete a pointer; wrap
the pointer around an object (you may want to overload * and ->). 
When the object goes out of scope?
The destructor is called, and you put delete into it.

	~Garage() { delete car; }

When other objects makes a copy of it, make sure to implement copy constructor
to get the correct behavior (create a new car, and it deletes it new car when done).


Use move constructor and move assignment operator to move objects that are about
to go out of scope into a another object without deleting and recreating.

*The heap analogy (with undefined behavior)*



Think of memory as a big warehouse with lots of boxes to put things into. 
When you call "new", the warehouse staff finds an unused box large enough for 
your needs, records that box as being owned by you (so it's not given to someone
 else), and gives you the number of that box so you can put your stuff into it.
 This number would be the "pointer".

Now, when you "delete" that pointer, the reverse happens: the warehouse staff 
notes that this particular box is available again. Contrary to real warehouse 
staff they aren't doing anything with the box — 
*here comes the undefined behavior* — so if you look into it after a 
"delete" you might see your old stuff. Or you might see somebody else’s stuff, 
if the box was reassigned in the meantime.

Technically, you are not allowed to look into your box once you have returned it
to the pool, but this is a somewhat weird warehouse with no keys or guards, so
you can still do whatever you want. However, it might cause problems with the 
new owner of the box, so it's expected that you follow the rules.

___

**Smart Pointers** *(trying to avoid all the constructors and overloads)*

- unique_ptr, low overhead, can't copy it (use std::move), cleans up after going 
out of scope.

- shared_ptr, can be copied, has reference counting

- weak_ptr, can 'look' at the shared_ptr without bumping the reference count.

___

**Inheritance**

*Must follow rule*: if you have one or more virtual functions, mark the 
the destructor as virtual! Derived destructors run first. 

When a function takes a base class object and you pass a derived class object, 
and call a method from it, if the method is virtual the derived version runs,
if not, the base class function runs. *Virtual* costs more. You can only call 
functions that are in the base class. Pointer and smart
pointers act the same way.

When something is private in the base class, it's inaccessible even for derived
classes (could use protected). Instead, use the base class' public functions.

In functions, use const reference instead of a plain copy argument to make the
function able to access derived methods from base class argument.

	class Car : Vehicle {};
	printInfo(Vehicle v); 
	printInfo(Car("Ford")); //Will print info on Vehicle object
	
	printInfo(Vehicle const& v);
	printInfo(Car("Ford")); //Will print info "Ford" on Car object

Use ```static_cast<type>``` to cast base class objects to derived class objects.
This is resolved at compile time. dynamic_cast<type> does a runtime check. Costs
more but also more safe. You can always check with an if to see if the object
is a nullptr.

	Car a("Porsche");
	Vehicle* b = &a;
	Car* c = static_cast<Car*>(b);

___

**slicing**

If you copy a derived object into a base object, the extra member varaibles and
function will disappear. This happens also when passing a double to int.

	double a = 2.2; int b = a; // 0.2 gone

You can't copy a base object into a derived object either. Use reference and 
pointer to pass to functions to avoid slicing.

___

**Other**

Instead of #include you could just paste the declarations into every file
that uses/implements it. 

Mark member functions as const, always, unless they should change some member variable.


*Declaration:* Introduction of a new name in the program.

	void foo(); //declare function
	int a; //declare object
	class Car;
	struct Car;

*Definition:* definition of a previously declared name.
In C++, *a declaration is a definition unless it declares a function without
specifying the function's body.*

	int a;
	void foo() { ... }
	class Car { ... };
	struct Car { ... };
	enum {x, y, z };

Initilization: assignment of a value, at construction time.

	T a = b;
	Car c("Toyota"); Car d{"Ford"};

In modern C++ we try to avoid using 'raw arrays'
(use std::vector or std::array).

*Rule of three*, define all of these or none at all; destructor, copy constructor,
copy assigment operator.

	~Car(); //destructor
	Car(Car const& c); //copy-constructor
	Car& operator=(const Car& c); //copy assignment operator

*Rule of five*, if your class needs move semantic, you need to define all 5;
destructor, copy-constructor, copy-assignment operator, move contructor and 
move assigment operator.

You can avoid the rule of three and rule of five with writing all your own copy 
and move constructors, move and copy assignment operators, destructors
etc. by not writing any of them. Use stack semantics or value semantics that rely
on something from the library to do the resource management.

*Shallow copy*, a copy of an object may reference the same member 
variables/objects as the original. Changes apply to both. There's shallow copy
 with reference count (how many refer to this object) and only deletes
when the counter reaches zero.

*Deep copy*, a copy of an object reference independent cloned member 
variables/objects. Changes apply only to the respective object.