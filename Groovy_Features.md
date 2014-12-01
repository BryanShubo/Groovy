

####1. Groovy bean


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

<!--Executing the previous code yields the following exception:groovy.lang.ReadOnlyPropertyException:Cannot set read-only property: namefor class: Customer-->
```
