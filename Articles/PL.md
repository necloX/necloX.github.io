# Programming language concepts, big or small
Considers the folowing piece of Lean code :
```
inductive Nat:Type
| Zero : Nat
| Succ : Nat->Nat
def Add: Nat -> Nat -> Nat
| Nat.Zero m := m
| (Nat.Succ n) m := Nat.Succ (Add n m)
inductive Eq : Nat -> Nat → Prop
| Refl (n:Nat) : Eq n n
def AddZeroLeft : ∀ n:Nat, Eq (Add Nat.Zero n) n
| Nat.Zero := Eq.Refl Nat.Zero
| (Nat.Succ m) := Eq.Refl (Nat.Succ m)
```
It is a minimal exemple of what people do in Lean.
Programmers refer to this style of programming as "functional". If you are interested in formalization, you are potentially already well verse in the wordl of functional programing (FP).  Here I am interested in introducing you to some programming languages (PL) concepts that are, right now, less likely to find their way in a proof assistant. If it is for better or worse is for you to judge.

Disclaimer : here formalization of mathematics refers particularly to formalization in dependtly typed programming languages (Lean, Agda, Coq…) There are other way people erg. 
Also I am neither a type theorist or a PL design professional, just a mathematician enthousiast about these topics !

# Classes
Objects are the obvious absents in the world dependently typed programming, which is based on functional programming (FP).
It is often assumed that for multiples reason, FP is the natural paradigm toward mathematicians should gravitate. However, I think 
objects, and especially classes, belong … In functional programming you gives ways to map into a type (the constructors)
and the system infers how you can map out of the type (the destructors). Classes allows you to specify a set of operations with esf one could interact with your object. I think it could be usefull we often think of mathematical constructs via how we use them. If I have a real bounded sequence, I can obtain a lesser superior bound, I can obtain a converging subsequences, etc… I can always create a function that map a bounded real sequence to one these, but they are such fundamental things that they could be encoded as methods from a bounded real sequence object.
# Case classes
# Type classes
One of the goal of object oriented programing (OOP) is to ensure substitution and encapsulation. Among other things this means that you should design youre (OOP) code in such way one could swap out youre classes to other one as long as one provide the same set of operations. 
This is a mathematical mindset. We are always looking to understand what particular features of the stuffs we work with gave them their property, in the hope of generalization.
In Lean, this is achievable with type classes. In other languages, the sames space is occupied with interfaces or traits. Scala in particular offer a powerfull trait system that provide the best of both.
# Inheritance
Contrary to OOP languages, what you don't have with Lean's typeclasses is inheritance. Inheritance is often critized to be responsable of hard to maintain code due to multiple levels.
# Export keyword
Another problem of inheritance is that one often could want to inherit from multiples classes. For instance the real numbers are both a field and of a topological space. Worse, some mathematical object can be the same things, but in different way, for instance they posses two operations for which they are a group.
One way to overcome this problem is the composition over inheritance principle. Rather than inheriting from topological spaces, or fields, the real numbers have a field pointing toward an interface or a typeclass that ensure that they are these things.
Scala 3 propose the export keyword that allow one to use the methods b.m() of a field B in a class a as if they were methods of the class a.m().
# Putting the name of the type before the variables
In Lean, the type of an identier comes after the identifier itself
>a:int=2 
In a lot of widely used languages, it's the opposite
>int a=2;
It is essentially a matter of preference but some argue that having the identifier coming first is better for readability, because types matter less. Of course types matters a lot more to mathematicians.
# Path dependant types
Scala propose that a if a class B is declared in the scope of another class A, the type a1.B and a2.B is not the same. It is particularly useful
