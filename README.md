# Access Modifiers and Inheritance

## Learning Goals

- Demonstrate access modifiers within an inheritance hierarchy

## Reviewing Access Modifiers

Recall that an **access modifier** in Java
controls the visibility of a class, field, constructor, or method.
When an access modifier is not specified, the visibility defaults
to the `package` level.

| Modifier        | Class | Package  | Subclass In<br>Another Package | World |
|-----------------|-------|----------|--------------------------------|-------|
| public          | Y     | Y        | Y                              | Y     | 
| protected       | Y     | Y        | Y  (within current object)     | N     | 
| default/package | Y     | Y        | N                              | N     | 
| private         | Y     | N        | N                              | N     | 

We'll explore how inheritance affects visibility in subclasses.
Let's work with the sample class hierarchy shown below.
Member access is denoted using one of the following symbols:

- `-` private member
- `+` public member
- `#` protected member
- `~` package/default member

![uml access modifier](https://curriculum-content.s3.amazonaws.com/6677/pillars/uml_access_modifier.png)

The classes are contained in two packages `p1` and `p2`. 

- Package `p1` contains classes `Main`, `Superclass`, `ClassA`, and `SubclassB`.
- Package `p2` contains classes `ClassC` and `SubclassD`.

![directory structure](https://curriculum-content.s3.amazonaws.com/6677/pillars/directory_structure.png)

The `Main` driver creates an instance of each class to show the object state due to inheritance.

```java
package p1;

import p2.ClassC;
import p2.SubclassD;

public class Main {

    public static void main(String[] args) {
        Superclass s = new Superclass();
        ClassA a = new ClassA();
        SubclassB b = new SubclassB();
        ClassC c = new ClassC();
        SubclassD d = new SubclassD();
        System.out.println();  //set breakpoint here to see object state
    }
}
```

The instances of `ClassA` and `ClassC` do not inherit fields from `Superclass`,
while instances of `SubclassB` and `SubclassD` do:


![debugger state access modifier](https://curriculum-content.s3.amazonaws.com/6677/pillars/debugger_inherited_state.png)


## Member Access Within Same Class

`Superclass` contains 4 instance variables with different access modifiers.

The method `testAccess()` shows that all four instance variables can be accessed
within the current class.

```java
package p1;

public class Superclass {
    private String privateField;  //accessible in current class
    public String publicField;    //accessible in any class
    protected String protectedField; //accessible in current package and in any subclass
    //package is default
    String packageField;  //accessible in current package

    public void testAccess() {
        this.privateField = "can access in current class";
        this.publicField = "can access in any class";
        this.protectedField = "can access in package or any subclass in another package (current object)";
        this.packageField = "can access in package";
    }
}
```

### Member Access Within Same Package

`ClassA` is contained in the same package as `Superclass`.

The method `testAccess()` creates an instance of `Superclass` to demonstrate
all but the `private` instance variable of class `Superclass`
can be accessed within `ClassA`, since it is in the same package. 

```java
package p1;

public class ClassA  {

    public void testAccess() {
        Superclass o = new Superclass();
        o.publicField = "can access in any class";
        o.protectedField = "can access in package or any subclass in another package (current object)";
        o.packageField = "can access in package";
    }

}
```

`SubclassB` is contained in the same package as `Superclass`.

The method `testAccess()` shows that all but the `privateField` instance variable
can be accessed within `SubclassB`, since it is in the same package as `Superclass`.

Note that each `SubclassB` object inherits
the `privateField` instance variable as we saw in the debugger view above, but
the field is not accessible in the subclass methods.

```java
package p1;

public class SubclassB extends Superclass {

    public void testAccess() {
        //Test inherited field access on current object
        this.publicField = "can access in any class";
        this.protectedField = "can access in package or any subclass in another package (current object)";
        this.packageField = "can access in package";
    }
}
```

### Member Access In Different Package

`ClassC` is contained in a different package as `Superclass` and is not a subclass.
Notice the `import` statement is used to specify the path to class `Superclass`. 

The method `testAccess()` shows that only the `public` instance variable
can be accessed within `ClassC`.

```java
package p2;
import p1.Superclass;

public class ClassC {

    public void testAccess() {
        Superclass o = new Superclass();
        o.publicField = "can access in any class";
    }
}
```

`SubclassD` is contained in a different package as `Superclass` and is a subclass.
The `import` statement is again used to specify the path to class `Superclass`.

Like all the other classes, we can access the `public` field.
However, the `protected` field can only be accessed within the same object,
but not other objects as shown in the code below.

```java
package p2;

import p1.Superclass;

public class SubclassD extends Superclass {

    public void testAccess() {
        //Test field access on another object
        Superclass o = new Superclass();
        o.publicField = "can access in any class";

        //Test inherited field access on current object
        this.publicField = "can access in any class";
        this.protectedField = "can access in package or any subclass in another package (current object)";
    }
}
```

## Resources

[Controlling access to a member of a class](https://docs.oracle.com/javase/tutorial/java/javaOO/accesscontrol.html)
