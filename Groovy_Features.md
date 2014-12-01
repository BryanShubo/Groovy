

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
