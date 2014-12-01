

####1. Groovy bean
####2. Inheriting constructors
####3. Adding the cloning functionality to Groovy Beans
####4. Defining code as data in Groovy
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
