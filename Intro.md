//def list = [2,3,4, "cat"]
//
////list.add()
//list << 1
//
//println list.class.name
//println list
//println 2 in list
//println "cat" in list
//
//
////hash map
//def map = [second: 100]
////map.put[]
//
//map["first"] = 10
//println map
//
//println "first" in map


def var = 1

// 0, null, false are false
// 1, true are true
if (var)
println "is true"
else 
println "is false"




//       == is identical to .equal in java
def list = [1, 2,3]
println list == [1, 2,3]


String s = null
def s1 = "something"
def s2 = 'something'
def s3 = "testing 3 and $s2"
def s4 = "testing 4 and ${s2.length()}"
def s5 = """ soemthing for multi
FFF
line
"""
def s6 = ["one", "two", "three"]
println s?.length()?.method()
println s1.class.name
println s2.class.name
println s3
println s4
println s5
println s6.join(",")



def c = {
     string ->
 println "$string Hello, groovy"}

c("Bryan")


def listTest = [1,2,3,4]

listTest.each{ println it}

def number = 3
number.times{ println "Hello"}


println listTest.collect{ it>2? it : null}
println listTest.findAll{ it>2? it : false}
println listTest.findResults{ it>2?"Number $it": null}
println listTest.inject{accumulated, value -> accumulated + value}



def printArgs(arg1, arg2="three") { println "$arg1 $arg2"}

printArgs("one")
printArgs("one", null)
printArgs("one", "two")

// if no return, the last line is return
// or you can explicitly define a return



// class vs scripts

public class testClass{
    def test(){ println "constructor"}
    def method(){ println "method"}
    
    public static void main(args) {
        def t = new testClass()
        t.method()
    }
}




public class try1{
    def function() { println 'function called'}
}

def tt = new try1()
tt.function()

def someString = 'function'
tt."$someString"()
tt.'function'()


def e = new Expando()
e.expandoFunction ={println 'expando function called'}
e.expandoFunction()

tt.metaClass.expandoFunction = { println 'expando function called'}
tt.expandoFunction()

tt.metaClass.getState = {println "the state is good "}
tt.state





@Grab('commons-codec:commons-codec:1.8')
import org.apache.commons.codec.digest.DigestUtils
// String.metaClass.shal << {DigestUtils.shalHex(delegate)}
String.metaClass.shal = {DigestUtils.shalHex(delegate)}
println DigestUtils.shalHex('test')
println 'test'.shal()
println "${'test'.shal()} = ${DigestUtils.shalHex('test')}"
