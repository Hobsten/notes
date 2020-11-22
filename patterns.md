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
