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
You can assign an object of one type to an object of another type if compatible (subclass object assigned to superclass object)
```
public void someMethod(Number n) { /* ... */ }
someMethod(new Integer(10)); 
someMethod(new Double(10.1));
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
