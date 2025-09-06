(equations)=
# Interactive Manipulation of Equations
If you want to do or record step-by-step algebraic manipulations of an equation
that consists of a left-hand-side (lhs) and right-hand-side (rhs) connected by
the relation operator `=`, use the `Equation` class. Currently,
inequalities are not supported.

**Warning:** Sympy also has the [`Equality` class](../../modules/core.rst#sympy.core.relational.Equality).
However, `Equality` should not be used for interactive algebra because
`Equality` is actually a Boolean and will return `True` or `False` if possible.

The `Equation` class provides the ability to use the mathematical tools in
SymPy to rearrange equations and perform algebra in a stepwise fashion using
as close to standard mathematical notation as possible. Thus, operations
applied to an equation are applied to both sides of the equation, just as
students are taught to do when attempting to isolate (solve for) a variable.

As a simple example consider starting with the van der Waals gas equation in
the form analogous to the ideal gas law (`P*V = n*R*T`) and doing the steps to
isolate P on the lhs.
```commandline
>>> from sympy import *
>>> var('P V n R T a b')
(P, V, n, R, T, a, b)
>>> eq1 =  Eqn((P - a**2*n**2/V**2)*(V - b*n), R*T*n)
>>> eq1
Eqn((P - a**2*n**2/V**2)*(V - b*n), R*T*n)
```
In an interactive environment such as
[Jupyter notebooks](https://jupyter.org) the equation would be depicted in
standard mathematical notation with an `=` sign using latex rendering. As
shown below:

![van der Waals in Jupyter](../pics/vdW_in_Jupyter.png)

For the rest of the examples we will turn on pretty_printing to approximate
this.

**Note**: More extensive control of output formatting, display of math
notation and code simultaneously, plus additional convenience tools
useful during interactive sessions are available by using this class via the
[Algebra_with_Sympy extension](https://gutow.github.io/Algebra_with_Sympy).
```commandline
>>> init_printing(pretty_print=True)
>>> eq1
⎛     2  2⎞
⎜    a ⋅n ⎟
⎜P - ─────⎟⋅(V - b⋅n) = R⋅T⋅n
⎜      2  ⎟
⎝     V   ⎠
```
The first step is to divide both sides by `(V - n*b)`. To achieve this we
simply divide the equation by the quantity:
```commandline
>>> eq2=eq1/(V-n*b)
>>> eq2
     2  2
    a ⋅n     R⋅T⋅n
P - ───── = ───────
      2     V - b⋅n
     V
```
The last step is just as easy:
```commandline
>>> eq3 = eq2+(a*n/V)**2
>>> eq3
               2  2
     R⋅T⋅n    a ⋅n
P = ─────── + ─────
    V - b⋅n     2
               V
```
Redefining an equation overwrites the previous version.
```commandline
>>> var('theta t c')
>>> eq1=Eqn(theta, a*t + b/c)
>>> eq1
          b
θ = a⋅t + ─
          c
```
Sympy functions, operations and python functions are applied to both sides,
just like (+, -, \* , /, \**, etc...). Some examples:
```commandline

>>> sin(eq1)
            ⎛      b⎞
sin(θ) = sin⎜a⋅t + ─⎟
            ⎝      c⎠
```
```commandline
>>> eq2=Eqn(t, b/c)
>>> eq2
    b
t = ─
    c
>>> sqrt(eq2)
         ___
        ╱ b
√t =   ╱  ─
     ╲╱   c
```
```commandline
>>> log(eq2)
            ⎛b⎞
log(t) = log⎜─⎟
            ⎝c⎠
>>> exp(log(eq2))
    b
t = ─
    c
```
Note:this example illustrates why you may need to pay attention
to the domain in which you are working. Reversing the order of operations
in this case does not return eq2 because the values of the variables may
not all be positive.
```commandline
>>> log(exp(eq2))
             ⎛ b⎞
             ⎜ ─⎟
   ⎛ t⎞      ⎜ c⎟
log⎝ℯ ⎠ = log⎝ℯ ⎠
```
For the combination of exp() and log(), insisting on positive values
will return the original equation:
```commandline
>>> var('d e f', positive=True)
(d, e, f)
>>> eq3=Eqn(d,e/f)
>>> log(exp(eq3))
    e
d = ─
    f
```
A few more examples of applying operations or functions to an equation.
```commandline
>>> eq2 = Eqn(t**2 - 1, c)
>>> factor(eq2,t)
(t - 1)⋅(t + 1) = c
```
```commandline
>>> eq3= Eqn(t**2 + 2*t +b*c + b*t, t*a + t + c*t)
>>> eq3
             2
b⋅c + b⋅t + t  + 2⋅t = a⋅t + c⋅t + t
>>> collect(eq3,t)
       2
b⋅c + t  + t⋅(b + 2) = t⋅(a + c + 1)
```
```commandline
>>> def addsquare(expr):
...     return expr+expr**2
...
>>> addsquare(eq1)
                            2
 2             b   ⎛      b⎞
θ  + θ = a⋅t + ─ + ⎜a⋅t + ─⎟
               c   ⎝      c⎠
```
There are times when you might only want to apply an operation to one side
of the equation (e.g. only factor terms on one side). This can be achieved
using `.applyrhs()` and `.applylhs()`:
```commandline
>>> eq4 =  addsquare(eq1)
>>> eq4.applyrhs(factor)
 2       (a⋅c⋅t + b)⋅(a⋅c⋅t + b + c)
θ  + θ = ───────────────────────────
                      2
                     c
```

