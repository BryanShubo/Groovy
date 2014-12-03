

####1. Groovy bean
####2. Inheriting constructors
####3. Adding the cloning functionality to Groovy Beans
####4. Closures
####5. Defining data structures as code in Groovy
####6. Implementing multiple inheritance in Groovy
####7. Adding a functionality to the existing Java/Groovy classes
####8. Defining type-checking rules for dynamic code
####9. Adding automatic logging to Groovy classes


####1. Groovy bean
```
class Student {  
Long id  
String name  
String lastName
}
```

```
def student = new Student()
student.setName('Charlie')
student.lastName = 'Parker'
assert student.name == 'Charlie'
assert student.lastName == 'Parker'

def student2 = new Student(id: 100, name: 'Jack', lastName: 'Shepard')
assert student2.name == 'Jack'
assert student2.id == 100
assert student2.lastName == 'Shepard'
```

Note:
```
The Map parameters can be set in any order and do not need to set all the properties of the bean:
def customer2 = new Customer(id:100)
def customer3 = new Customer(id:100, lastName:'Shepard')
```

#####1.1 Immutable field and class
```
class Student {  
final Long id  
String name  
String lastName  
Student(Long id) {
this.id = id  
}
}

def c = new Student(100)
```

```
import groovy.transform.Immutable
@Immutable
class Student {  
String name, lastName  
Long id
}
def student = new Student(lastName: 'Hogan', id: 200, name: 'Mark')
student.name = 'John'
<!--Executing the previous code yields the following exception:groovy.lang.ReadOnlyPropertyException:
Cannot set read-only property: namefor class: Customer-->

Note: The @Immutable annotation makes the class final, all the fields become final, and also the default equals, hashCode, and toString methods are provided based on the property values
```

#####1.2 ToString

Add a toString method to  a bean
```
@ToString
@ToString(includeNames = true)
@ToString(includeNames = ture, excludes = 'lastName,age')
```

#####1.3 @EqualsAndHashCode
```
import groovy.transform.EqualsAndHashCode
@EqualsAndHashCode
class Student {  
String name  
String lastName
}
def s1 = new Student(name: 'John', lastName: 'Ross')
def s2 = new Student(name: 'Rob', lastName: 'Bell')
assert !s1.equals(s2)
def copyOfS2 = new Student(name: 'Rob', lastName: 'Bell')
Set students = [:] as Set
students.add c1
students.add c2
students.add copyOfC2

assert users.size() == 2
```

#####1.4 @TupleConstructor

The @TupleConstructor annotation deals with Groovy Bean constructors and creates constructors that do not require named parameters (Java-style constructors). For each property in the bean, a parameter with a default value is created in the constructor in the same order as the properties are declared. As the constructor is using default values,  we don't have to set all the properties when we build the bean.

```
//includeSuperProperties
//includeSuperFields
//callSuper=true
<!--If the class annotated with @TupleConstructor extends another class and we wish to include the properties or fields of the superclass, we can use the attributes includeSuperProperties and includeSuperFields. Finally, the callSuper=trueattribute instructs the annotation to create a code in the constructor to call the super constructor of the superclass with the properties.-->

import groovy.transform.TupleConstructor
@TupleConstructor
class Student {  
String name  
String lastName  
Long age  
List favouriteSubjects
}
def s1 = new Student('Mike','Wells',20,['math','phisics'])
def s2 = new Student('Joe','Garland',22)
assert s1.name == 'Mike'

assert !s2.favouriteSubjects
```

#####1.5 @Canonical

The @Canonical annotation instructs the compiler to execute an AST transformation which adds positional constructors, hashCode, equals, and a pretty print toString to a class.

```
includes / excludes
```

####2. Inheriting constructors 
```
import groovy.transform.InheritConstructors
@InheritConstructors
class BusinessException extends Exception { }
println new BusinessException('hello').message == 'hello'
def b1 = new BusinessException('missing resource')
def b2 = new BusinessException('catastrophic failure', b1)
println b2.cause.message == 'missing resource'
```

####3. Adding the cloning functionality to Groovy Beans

```
import groovy.transform.AutoClone
@AutoClone   
class Vehicle {
String brand  
String type  
Long wheelsNumber
}
```
```
def v1 = new Vehicle()
v1.brand = 'Ferrari'
v1.type = 'Testarossa'
v1.wheelsNumber = 4
def v2 = v1.clone()

assert v1 instanceof Cloneable
assert v1.brand == v2.brand
```
The snippet in step 2 shows how easy is to clone an object without having to implement any interface. The default cloning strategy implemented by the annotation will call super.clonebefore calling clone on each Cloneable property of the class. If a field or property is not cloneable, it is simply copied in a bitwise fashion. If some properties don't support cloning, a CloneNotSupportedException is thrown.

```
@AutoClone(style=AutoCloneStyle.COPY_CONSTRUCTOR)
```
If a class already implements Serializable, the @AutoClone annotation can be configured to use the Serialization style. This feature performs a deep copy automatically, attempting to copy the entire tree of objects including array and list elements. The AutoCloneStyle.SERIALIZATION style has some limitations; it is generally slower  and it doesn't support fields marked with final.


####4. Closures

#####4.1 Definition
```
{ -> }
```
* The previous snippet is actually a closure without body. 
* It is, in fact, an object of type groovy.lang.Closure. 
* As with every other object, a closure can be passed to other methods or even to other closures. 
* However, a closure is also a method—a method with no associated class; 
* Therefore, it may have arguments and can return a value (yes, it can also return a closure). 
* A closure always returns the value of the last statement in the body; the return keyword is not needed. 

```
def doubling = { arg1 -> println arg1 * 2 }
        [1,2,3,4].each (doubling)
        
// result
2
4
6
8
```

```
//The it keyword is an implicit variable that works on closures, accepting a single argument.  
[1,2,3].each{println it}
[1,2,3].each{element -> println element}
```

#####4.2 Example
```
class ExpensiveResource {
def open() {println 'opened!' }    
def writeData(data) {println "data written! $data" }    
def close(){println 'closed!'}
}

def e = new ExpensiveResource()
try {  
e.open()  
println e.data
} 
finally {  e.close()}
```

```
// Use closure
def safeResource(Closure closure) {  
def resource = new ExpensiveResource()  
try {    resource.open()    closure(resource)  } 
finally {    resource?.close()  }
}

safeResource { it -> it.writeData('hello world!') }
```

For instance, the method file.eachLineMatch(pattern) { ... } opens a file, iterates over each line trying to find the lines that match the specified pattern, and then invokes some code passed in form of a closure for the matching line(s), if any, before eventually closing the file.


#####4.3 curry

The limitation of the curry method is that arguments are bound from left to right in the argument list. In other words, in two arguments-closures, only the left argument can be used with the curry function.
```
def multiply = {x, y -> x * y}
println(multiply(3,5)) // 15
def c4 = multiply.curry(4)
println(c4(25)) //100
```

```
def filterList = { filter, list -> list.findAll(filter) }
def even = { it % 2 == 0 }
def odd = { !even(it) }

def evenFilterList = filterList.curry(even)
def oddFilterList = filterList.curry(odd)

println evenFilterList(0..8) // 0,2,4,6,8
println oddFilterList(0..8) // 1,3,5,7
```

####5. Defining data structures as code in Groovy
```
* MarkupBuilder
* DOMBuilder
* JsonBuilder
* SwingBuilder
* ObjectGraphBuilder

// java
Teacher t = new Teacher('Steve')
Student s1 = new Student('John')
Student s2 = new Student('Richard')
t.addStudent(s1)
t.addStudent(s2)

// Groovy
teacher ('Jones') { 
student ('Bob')  
student ('Sue')
}
```

####6. Implementing multiple inheritance in Groovy

####6.1 @Mixin -- runtime binding

The @Mixin annotation that we placed in our source code informs Groovy to perform a transformation of the target class and add behavior (methods) of mixed-in classes into that. 

The annotation accepts a single class name or an array of class names. 

Mixing in behavior is done exclusively at runtime, since (as we already noted) under the hood, JVM does not allow multiple parent classes. This is also the reason that the instanceof operator will not work for mixed-in classes. For example, you can't check that groovyMag is an instance of the PeriodicPulication class. But you can still use the groovyMag and other variables to call the features of the mixed-in classes at runtime.

In the test class, we used the Groovy Bean constructor syntax to refer to the fields that are available in the base class, but the fields we mixed-in from different sources can only be accessed in a dynamic way. In fact, they are not just fields but also a pair of getter and setter automatically defined by Groovy for all the bean classes. So, we really mix the behavior (that is, methods) in, not the state of the class. That's also the reason we used another Groovy feature here—the with method. This method is defined for all objects in Groovy; it takes a closure as a parameter, and the object itself is passed to the closure's context. That's why you can easily refer to an object's methods and properties in a short form. In fact, you could have used the withmethod to define all the properties of our object:

```
def groovyMag = new OnlineMagazine().with {
                                           id = 'GRMAG'
                                           title = 'GroovyMag'
                                           url = new URL('http://grailsmag.com/')
                                           issuePeriod = monthly
                                           it               
                                           }
```

#####6.2 @Delegate -- compile time binding

```
class Book extends Publication {
    @Delegate  
    PrintedPublication printedFeatures = new PrintedPublication()
    }
```

```
def groovy2cookbook = new Book(
                              id: 'GR2CBOOK',
                              title: 'Groovy 2 Cookbook',
                              pageCount: 384   
                              )
```

Note: It's hard to vote for any of these approaches and decide which one would be better to handle your multiple inheritance needs. The @Mixin definition is less verbose and automatically creates instances of all mixed-in classes, but on the other hand, managing the state of those instances may not be so obvious. The @Delegate transformation has a more verbose definition due to an additional variable declaration, but it also gives your target class direct access and control over the way your mixed-in state is created.


####7. Adding a functionality to the existing Java/Groovy classes


####8. Defining type-checking rules for dynamic code

In this recipe, we will present how Groovy can be instructed to apply static typing checks to code elements to warn you about potential violations at compiletime; that is, before code  is executed

* 1. Easier to spot programming mistakes (for example, preventing assigning a Boolean to an integer).
* 2. Self-documenting code thanks to type signatures.
* 3. The compiler has more opportunities to optimize the code (for example, replacing a virtual call with a direct one when the receiver's type is statically known).
* 4. More efficient development tools (for example, autocompletion of members of  a class).


Defenders of dynamic typing return fire by claiming that static typing is inflexible and has a negative impact on prototyping systems with volatile or unknown requirements. Furthermore, writing code in dynamic languages requires more discipline than writing in statically typed languages. This is often translated into a greater awareness for testing—and unit testing in particular—among developers who decide to produce code with dynamic languages.


@groovy.transform.TypeChecked, forcing complier to enable static type-checking.

@TypeChecked, if we place it on a method, the type-checking is performed only on the members of the target method, including closures.

####9. Adding automatic logging to Groovy classes

@groovy.util.logging.Log
