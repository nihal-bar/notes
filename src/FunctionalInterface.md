# Functional Interface (explained with a lil' Java)

###### note: if this was even a little useful to you I am glad, good luck with your learnings!

What is a functional Interface?

An interface that contains EXACTLY one method that is neither static, nor default.

```
public interface MyInterface {
    int N = 1;
    void m1(); //the functional method of this functional interface
    static void m2();
    default int m3();
```

Above, the method ```void m1()``` is the functional method.
Of course, as Interfaces do not contain concrete Implementation,
the Implementation is passed on to the Class which implements the Interface.
___
## Lambda Function
Suppose I have a functional Interface as below, and the implementing concrete Class.
As usual, the concrete class needs to implement the method that was declared in the Interface.

```
public interface IntToDoubleFunction {
    double applyAsDouble ( int n );
  }

____________________________________________________
public class Mult implements IntToDoubleFunction {
    private double x;

    public Mult ( double factor ) {
        x = factor;
    }

    public double applyAsDouble ( int m ) {
        return m * x;
    }
}
```

Now to access this functionality, I guess I'll create an Object which can then run the method.

```
    IntToDoubleFunction fct1 = new Mult ( 10 );
    double y = fct1.applyAsDouble ( 11 );
```

We set up a variable of the IntToDoubleFunction interface and assign it the address of a newly set up object of type Mult.
The call to applyAsDouble returns the product of 10 and 11.

Seems good. But it's such a simple fucking function. Why the hell do I need a whole-ass class for it?

This is where Lambda Expressions come in. Check this out:

```
    IntToDoubleFunction fct2 = x -> x * 10;
    double z = fct2.applyAsDouble ( 11 );
```

This is a Lambda Expression in its simplest form.
We have a <b>Parameter</b>, here named ```x```, then an arrow, and
then the <i>Result</i> that we get after running the Lambda expression,
in this case ```x * 10```.

Another way to look at it is this

```
           x -> x * 10;
    (int n) -> (return n * 10)
```

Internally in the Compiler, one can imagine that the following
implementation takes place. Keep in mind, this internal working does not concern us,
this is made just for representation purposes.

### Source Code:

```
    IntToDoubleFunction fct2 = x -> x * 10;
    double z = fct2.applyAsDouble ( 11 );
```

### What the compiler does internally

```
    class <<AnonymousClass>> implements IntToDoubleFunction {
        public double applyAsDouble(int x){
            return x * 10;
        }
    }
________________________________________________________________

    IntToDoubleFunction fct = new <<AnonymousClass>>();

```

The compiler looks at the source code, and does the following things:

1. It creates an anonymous class, pretty much with the same functionality as ```Mult``` from the previous example. The lambda expression becomes the body for our method ```applyAsDouble```.
2. Remember the Functional Interface, that contains EXACTLY ONE functional method? Since ```IntToDoubleFunction``` is a
   <i>Functional Interface</i>, there is no ambiguity: <b>only the ```applyAsDouble``` method can be meant by the lambda
   expression.</b>

We don't really care what the true name for the AnonymousClass is.
Primarily because this class is NO EXPLICITLY DEFINED.

What if we've declared a variable before our Lambda Expression,
and we're using it in the Expression?

```
    double y = someObject.returnsTrue() ? 10 : 12
    .
    .
    IntToDoubleFunction fct2 = x -> x * y;
    double z = fct2.applyAsDouble ( 11 );
```

The compiler would interpret it as follows:

```
    class <<AnonymousClass>> implements IntToDoubleFunction {
        int a;

        public <<AnonymousClass>> ( int a ) {
            this.a = a;
        }

        public double applyAsDouble ( int x ) {
            return x * a;
        }
    }
________________________________________________________
IntToDoubleFunction fct2 = new <<AnonymousClass>> ( y );
```

The new variable declaration ```y```, must not be tampered with ie.
no new values, no changes should be made. Remember that the current value of ```y```  is referenced, _NOT copied_. To avoid further complications, think of this variable as final or constant, in a term <i>effectively
final</i>.

___

##  Predicate 
A predicate, in math or computer science, is a boolean function.
Meaning it gives either true or false as ```return```.

You could combine the workings of a Lambda Function with as such(they both mean the same thing):

```
    IntPredicate pred1 = new isEven();
    IntPredicate pred2 = x -> x % 2 == 0;
```

You can also combine predicates with other predicates. Absolute tomfoolery happening here:

```
    IntPredicate pred1 = n -> n % 2 == 1;
    IntPredicate pred2 = n -> n > 0;
    IntPredicate pred3 = n -> n * n < 100;
    IntPredicate pred4 = pred1.negate();
    IntPredicate pred5 = pred2.and ( pred3 );
    IntPredicate pred6 = pred4.or ( pred5 );
```

Do you wanna make an array of these things? 
No? I don't care! 

Here's one anyway: (the Array Component type is that of
the Interface)

```
    IntPredicate [ ] predicates = new IntPredicate [ 6 ];

    predicates [ 0 ] = n -> n % 2 == 1;
    predicates [ 1 ] = n -> n > 0;
    predicates [ 2 ] = n -> n * n < 100;
    predicates [ 3 ] = predicates[0].negate();
    predicates [ 4 ] = predicates[1].and ( predicates[2] );
    predicates [ 5 ] = predicates[3].or ( predicates[4] );
```

___

## Standard and Short Form 
Coming back to Lambda Functions, lets look at our two old implementations.

```
           x -> x * 10;
    (int n) -> (return x * 10)
```

The second line of code is the true Standard form for the Lambda expression.
The one above it is the Short form.

Again: Standard Form (including semicolon!)--

```
(int x, int y) -> (return x * 256 + Math.pow(y,6000) - 42;);
```

Short form --

 ```
   (x , y) -> x * 256 + Math.pow(y,6000) - 42
 ```



