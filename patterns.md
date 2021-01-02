### Low coupling 
Imagine a car where you can just change the tire if its punctured, that's low coupling

## Interface 
A restaurant isn't dependent on a particular chef but that it has a chef (`interface`)

## Ecapsulation
Without the `private` keyword anyone can change a member variable and skip input validation (e.g. setting `bob.age = -2`)

## Abstraction
Hide implementation detail, make most methods private if possible

## Inheritance
Reuse already written code 

## Polymorphism
An object can take on many forms 
```
static void drawShape(Shape shape) {
	shape.draw()	
}
drawShape(new Circle());
drawShape(new Rectangle());
```


## Memento
------
The memento pattern is used to implemented undo mechanisms.

Memento pattern  
![Memento Pattern](images/memento.png)  
A text editor with an undo mechanism implemented using Memento pattern  
![Editor with undo](images/memento-impl.png)  

## State
------
The state pattern is a behavioral design pattern that allows an object to alter its behavior when its internal **state** changes (using *polymorphism*). This allows us to follow the *Open Closed Principle*: our classes and modules should be *open for extension* but *closed for modification*.
  
State pattern
![State Pattern](images/state.png)  
Implementation  
![Subclasses with different states/behaviors](images/state-impl.png)


## MVC (Model View Controller)
------

Controller 
- Handles request flow
- Never handles data logic
- Tells the model what to do / what data to get
- Interacts with Model and View (Model and View never interact directly with each other)

Model
- Handles data logic
- Interacts with database
- Handles validation, CRUD
- Doesn't have to worry about client requests

View
- Presents data from controller
- Dynamically rendered
- The rendered data is sent back to controller in a HTML


Client 			---- Get Cats -----------> 	Controller
Controller	---- Get Cats Data ------> 	Model
Controller 	<--- Cats Data -----------	Model
Controller	---- Get Presentation --->	View
Controller 	<--- Cats Presentation ---	View
Client 			<--- <ul> Cats </ul> -----	Controller

If an error occurs, the controller will request an error view from View

## Singleton

When there are classes you only need one instance of through the whole application. An example of this is a class that fetches data from a database (`UserService`).

```
public DatabaseService {
	// This instance is created when the JVM initializes the class (before loading it)
	static DatabaseService databaseService = new DatabaseService();

	private DatabaseService(){ // code ..}

	public static DatabaseService getInstance() {
		return databaseService;
	}
}
```
**Lazy Instantion**, instantiate the object when you use it
```
public DatabaseService {
	static DatabaseService databaseService;

	private DatabaseService(){ // code ..}

	public static DatabaseService getInstance() {
		// Make sure a new object isn't created each time this method is called
		if (databaseService == null) {
			databaseService = new DatabaseService();
		}
		return databaseService;
	}
}
```
This solution will fail when using multiple threads as two threads may enter `getInstance()` at the same time while `databaseService` is `null`

Bad solution. The first thread enters, and the rest has to wait. This addresses the problem but instroduces a performance problem.
```
public static synchronized DatabaseService getInstance() { // ... }
```

Good solution. Here we check for `null` two times and make the last check `synchronized` to make sure only one thread is checking the value at a time. This does not introduce a performance issue as the waiting only occurs in the second `if`-block and only at the start when the instance hasn't been created yet.
```
public static DatabaseService getInstance() { 

	if (databaseService == null) {
		synchronized(DatabaseService.class) {
			if(databaseService == null)
				databaseService = new DatabaseService();
		}
	}
	return databaseService;

}
```

With `Enum`
```
// Has implicit private constructor
enum DatabaseService {
	// same as getInstance()
	INSTANCE;
}
```

## Builder

A class delegates object creation to a `Builder` object instead of creating the objects directly (makes it possible to change the representation of the object independently later). Encapsulates creating and assembling the parts of a complex object in a separate `Builder` object. 

It is hard and error prone to write client code when there are multiple constructors, each with a large set of parameters. 
```
public House(String foundation, String structure, String roof, boolean painted);
public House(String foundation, String structure, String roof, boolean painted, boolean furnished);
...
```
*Which constructor should I invoke? What will be the default values of the parameters? Does the first boolean represent furnishing or painting?*  
Tackling this with `getters` and `setters`
```
House house=new House();
house.setBasement("Concrete, brick, and stone");
house.setRoof("Concrete and reinforced steel");
house.setStructure("Concrete, mortar, brick, and reinforced steel");
house.setFurnished(true);
house.setPainted(true);
```
But now the house collapsed because the roof was built before the structure. And also, customers are calling in with different requirements for their houses; a prefabricated house, a tree house, and an Igloo. The builder pattern handles these scenarios. The builder pattern allows you to enforce a step-by-step process to construct a complex object as a finished product. 

* We don't want the client to have to know every step the order of every step to construct a house
* With different types of houses (representations) we should have specialized builders who take care of the required implementation for each
* A `ConstructionEngineer` should not be directly tied to any particular builder. The `ConstructionEngineer` is only tied to a `HouseBuilder` interface, and can direct any builder that implements this interface. More builders can be added without changing the implementation and we maintain low coupling. 

* Product (`House`): A class that represents the product to create.
* Builder (`HouseBuilder`): An interface to build the parts of a product.
* ConcreteBuilder (`ConcreteHouseBuilder` and `TreeHouseBuilder`): Are concrete classes that implement Builder to construct and assemble parts of the product and return the finished product.
* Director (`ConstructionEngineer`): A class that directs a builder to perform the steps in the order that is required to build the product.

**House.java**
```
public class House {
    private String foundation;
    private String structure;
    private String roof;
    private boolean furnished;
    private boolean painted;
    // getters and setters ...
}
```

**HouseBuilder.java**
```
public interface HouseBuilder {
    void buildFoundation();
    void buildStructure();
    void buildRoof();
    void paintHouse();
    void furnishHouse();
    House getHouse();
}
```
**ConcreteHouseBuilder.java**
```
public class ConcreteHouseBuilder implements HouseBuilder{
    private House house;
    public ConcreteHouseBuilder() {
        this.house = new House();
    }
    @Override
    public void buildFoundation() {
        house.setFoundation("Concrete, brick, and stone");
        System.out.println("ConcreteHouseBuilder: Foundation complete...");
    }
    @Override
    public void buildStructure(){
      house.setStructure("Concrete, mortar, brick, and reinforced steel");
      System.out.println("ConcreteHouseBuilder: Structure complete...");
    }
    @Override
    public void buildRoof(){
      house.setRoof("Concrete and reinforced steel");
        System.out.println("ConcreteHouseBuilder: Roof complete...");
    }
    @Override
    public void paintHouse(){
      house.setPainted(true);
        System.out.println("ConcreteHouseBuilder: Painting complete...");
    }
    @Override
    public void furnishHouse(){
    house.setFurnished(true);
        System.out.println("ConcreteHouseBuilder: Furnishing complete...");
    }
    public House getHouse() {
        System.out.println("ConcreteHouseBuilder: Concrete house complete...");
        return this.house;
    }
}
```

**ConstructionEngineer.java**  
```
public class ConstructionEngineer {
    private HouseBuilder houseBuilder;
    public ConstructionEngineer(HouseBuilder houseBuilder){
        this.houseBuilder = houseBuilder;
    }
    public House constructHouse() {
        this.houseBuilder.buildFoundation();
        this.houseBuilder.buildStructure();
        this.houseBuilder.buildRoof();
        this.houseBuilder.paintHouse();
        this.houseBuilder.furnishHouse();
        return this.houseBuilder.getHouse();
    }
}
```
The `ConstructionEngineer` is not tied to any particular builder. New builder classes can be added without making any changes to the construction process. 


**Test**
```
public class ConstructionEngineerTest {
    @Test
    public void testConstructHouse() throws Exception {
        HouseBuilder concreteHouseBuilder = new ConcreteHouseBuilder();
        ConstructionEngineer engineerA = new ConstructionEngineer(concreteHouseBuilder);
        House houseA = engineerA.constructHouse();
        System.out.println("House is: "+houseA);
        PrefabricatedHouseBuilder prefabricatedHouseBuilder = new PrefabricatedHouseBuilder();
        ConstructionEngineer engineerB = new ConstructionEngineer(prefabricatedHouseBuilder);
        House houseB = engineerB.constructHouse();
        System.out.println("House is: "+houseB);
    }
}
```
The client is now insulated from the object creation process. The client just provide the **Director** a **Builder** to use. The abstract factory pattern is similar to the builder pattern, but the abstract factory is more concerned with **what** is made, while the builder is more concerned with **how** it is made. 


## Chain-of-responsibility

https://en.wikipedia.org/wiki/Chain-of-responsibility_pattern
