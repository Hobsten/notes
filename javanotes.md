prefer lambdas to anonymous classes
treat type inference as magic, its advanced and it works
leave out types unless it makes the program clearer
you must understand generics to use lambdas
lambdas should be self-explanatory
lambdas should not exceed a few lines


### Generics
---
Enable types (classes and interfaces) to be parameters when defining classes, interfaces and methods (reusability). Input to formal parameters are values, while inputs to type parameters are types.
Enable more bugs to be detectable at compile time rather than runtime.

##### Terms
---
`T` is a type *parameter* in `Foo<T>`  
`String` is a type *argument* in `Foo<String>`  


##### The Diamond (Java SE7) 
---
`Shape<Circle> shape = new Shape<Circle>()` can be written `Shape<Circle> shape = new Shape<>();`
```
public interface Pair<K, V> {
	public K getKey();
	public V getValue();	 
}

public class OrderedPair<K, V> implements Pair<K, V> {
	private K key;
	private V value;

	public OrderedPair(K key, V Value) {
		this.key = key;
		this.value = value;
	}

	public K getKey() { return key; }
	public V getvalue() { return value; }
}

Pair<String, Integer> pair1 = new OrderedPair<String, Integer>("key1", 5);
Pair<String, Integer> pair2 = new OrderedPair<>("key2", 10); // shorter
```

##### Raw type (of a generic type)
---
Parameterized type: `Shape<Circle> shape = new Shape<>();`
Raw type: `Shape shape = new Shape();`  
Note: **A non-generic class or interface type is NOT a raw type!**

##### Generic Methods
---
```
public class Util {
    public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
               p1.getValue().equals(p2.getValue());
    }
}
```
```  
Pair<Integer, String> p1 = new Pair<>(1, "apple");
Pair<Integer, String> p2 = new Pair<>(2, "pear");
boolean same = Util.<Integer, String>compare(p1, p2);
boolean rewritten = Util.compare(p1, p2); // type inference
```  

##### Bounded Type Parameters
---
Restricting the types used as type arguments by requiring them to be of a particular class or subclass.  
`extends` here means it extends a superclass or `implements` an interface.  
```
public class Box<T extends Integer> {
	private T t;
	public void set(T t) { this.t = t; }	
	// Invoke methods defined in the bounds:
	public Boolean isEven() { return t.intValue() % 2 == 0; }
  public <U extends Number> void inspect(U u){
    System.out.println("T: " + t.getClass().getName());
    System.out.println("U: " + u.getClass().getName());
  }
}

```
A type variable may have multiple bounds, but remember to specify classes before interfaces. Order matters.
Operators like `>` doesn't work on objects, but a type parameter can be bounded by the `Comparable<T>` interface.
*Sidenote: Lists (and arrays) of objects that implement this interface can be sorted automatically by `Collections.sort` and `Arrays.sort`*
```
public interface Comparable<T> {
	public int compareTo(T o);
}

public static <T extends Comparable<T>> int countGreaterThan(T[] array, T elem) {
	int count = 0;
	for (T e : array) {
		if(e.compareTo(elem) > 0) {
			count++;
		}
	}
	return count;
}
```

##### Inheritance and Subtypes
---
You can assign an object of one type to an object of another type if compatible (subclass object assigned to superclass object). In other words, you can assign an object of a type to an object of its supertype.
```
public void someMethod(Number n) { /* ... */ }
someMethod(new Integer(10)); 
someMethod(new Double(10.1));
```
or
```
Object someObject = new Object();
Integer someInteger = new Integer(10);
someObject = someInteger;   
```
Same with generics
```
Box<Number> box = new Box<Number>();
box.add(new Integer(10));  
box.add(new Double(10.1));  
```

`MyClass<A>` has no relationship to `MyClass<B>`, regardless of whether or not `A` and `B` are related.
```
public void boxTest(Box<Number> n) { /* ... */ };
boxTest(new Box<Integer>); // Error, Box<Integer> is not a subtype of Box<Number> (Integer is only a subtype of Number)
```

`ArrayList<String>` is a subtype of `List<String>` which is a subtype of `Collection<String>` because `ArrayList<String>` implements `List<String>` which extends `Collection<String>`.  
The type argument must be the same however to preserve the subtyping relationship.  
`MyCustomList<E, F>` are a subtype of `List<E>` as long as it `extends List<E>` and the type argument `E` match. 

##### Parameterized type before return type

Here the type parameter will be typically defined in the class declaration to which the method belongs `class MyClass<T> { // insert method here }`
```
public void createArray(T sample){
    ArrayList<T> list = new ArrayList<T>();
    list.add(sample);
}
```
This method is a *generic method* because it defines its own type parameter.
```
public <T> void createArray(T sample){
    ArrayList<T> list = new ArrayList<T>();
    list.add(sample);
}
```

##### Type inference

*Type inference* is the process of automatically deducing unspecified data types of an expression based on the contextual information.
*Type witness* (readability) is when you specify the parameter instead of just letting the compiler infer the type.  
###### Generic Methods
```
// Adding objects to list
BoxDemo.<Integer>addBox(Integer.valueOf(10), listOfIntegerBoxes); // Type witness
BoxDemo.addBox(Integer.valueOf(20), listOfIntegerBoxes);
```
###### Generic Classes
Use the diamond `<>` to take advantage of type inference during generic class instantiation and not have to specify type parameters.  
In the last example `HashMap()` refers to the `HashMap` raw type, and not the `HashMap<String, List<String>>` type.
```
Map<String, List<String>> myMap = new HashMap<>();
Map<String, List<String>> myMap = new HashMap(); // unchecked conversion warning
```

Constructors can be generic (declare their own formal parameters) in both generic and non-generic classes. Notice the preceding type parameter `<T>`. This is needed because it is declaring its own formal type parameter which is not declared by the class.
```
class MyClass<X> {
  <T> MyClass(T t) {
    // ...
  }
}

new MyClass<Integer>(""); // an instance of the parameterized type MyClass<Integer> with String as the formal type parameter T
```

###### Target Types
The target type of an expression is the data type that the Java compiler expects depending on where the expression appears.
`static <T> List<T> emptyList();`  
`List<String> fooList = Collections.emptyList();`  
The `List<String>` data type is the `target type`.  
`emptyList` returns a value of type `List<T>`, so the compiler infers that the type argument `T` must be the value `String`. 
With type witness  
`List<String> fooList = Collections.<String>emptyList();`  
Java can use method arguments as target types also
```
void processStringList(List<String> stringList) {
	// ...
}

processStringList(Collections.emptyList());
```
`processStringList` requires an argument type of `List<String>` and `Collections.emptyList` returns a value of `List<T>`, so with the target type of `List<String>` the compiler infers the type argument `T` as `String`. 


###### Type Target vs Type Inference

In `int a = b` the *target type* is int. The expression, `b`, should have a type of `int` because its context requires that. 
*Type inference* by contrast is the process used to determine the type of an expression (`b`). The target type may be used as part of the type inference process. 

##### Wildcards

The `?` is called a *wildcard* and represents an unknown type. The wildcard can be used as a type for a 
- parameter
- field
- local variable
- return type (sometimes)
The wildcard is never used as a type argument or a 
- generic method invocation
- generic class instance creation
- supertype

###### Upper Bounded Wildcards

Relaxing the restrictions on a variable. Use the wildcard `?`, followed by the `extends` ( also means `implements` in this context) keyword, followed by its *upper bound* `Class`:  `List<? extends Number>`. A method like this would work on lists of `Number` and subtypes of `Number`. 
```
List<Number> // more restrictive, matches a list of type Number only
List<? extends Number> // matches a list of type Number or any of its subclasses 
```
All methods of the *upper bound* class can be accessed
```
public static double sumOfList(List<? extends Number> list) {
    double s = 0.0;
    for (Number n : list)
        s += n.doubleValue();
    return s;
}
```

###### Unbound Wildcares

The unbounded wildcard `?` specifies an *unknown type*. Useful when
- writing a method that can be implemented using functionality provided in the `Object` class
- the code is using methods in the generic class that don't depend on the type parameter. E.g. `List.size` or `List.clear`  
`?` and `? extends Object` are synonymous.

###### Lower Bounded Wildcards

Restricts the unknown type to be a specific type or a *super type* of that type. Use the wildcard `?`, followed by the `super` keyword, followed by its *lower bound* `Class`: `List<? super Integer>`.
```
List<Integer> // more restrictive, matches a list of type Number only
List<? super Integer> // matches a list of type Integer or any of its superclasses
```


