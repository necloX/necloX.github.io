# Programming language concepts, big or small
Considers the folowing piece of Lean code :
```
inductive Nat:Type
| Zero : Nat
| Succ : Nat->Nat
def Add: Nat -> Nat -> Nat
| Nat.Zero m := m
| (Nat.Succ n) m := Nat.Succ (Add n m)
```
It is a minimal exemple of what people do in Lean.
Programmers refer to this style of programming as "functional". If you are interested in formalization, you are potentially already well verse in the world of functional programing (FP).  Here I am interested in introducing you to some programming languages (PL) concepts that are, right now, less likely to find their way in a proof assistant. If it is for better or worse is for you to judge.

Disclaimer : here formalization of mathematics refers particularly to formalization in dependtly typed programming languages (Lean, Agda, Coq…). Also I am neither a type theorist or a PL design professional, just a mathematician enthousiast about these topics !

# Classes
It is often assumed that for multiples reason, FP is the natural paradigm toward mathematicians should gravitate. However, I think 
objects, and especially classes could be quiet useful. Classes allows you to bundle an essential set of functionality (*methods*) with a type. For instance, in C#, one could write a pretty similar program that our Lean exemple that way :

'''
abstract class Nat
{
    public abstract Nat Add(Nat other);
}
public class Zero : Nat
{
    public Zero() { }
    public override Nat Add(Nat other)
    {
        return other;
    }

}
public class Succ : Nat
{
    public Nat prec;
    public Succ(Nat prec) { this.prec = prec; }
    public override Nat Add(Nat other)
    {
        return new Succ(prec.Add(other));
    }
}    
'''
Now, the former Lean inductive type Nat become modelized through an abstract class and *inheritance* relation ship. That way we require in the "Nat" class that natural number suport an "Add" operation. Remark that here, pattern matching is replaced with *ad hoc polymorphism*, through the "override" keyword.

Inheritance allow the user to specify an "is a" relationship. This result in a world of difference with an inductive type : users can always create new classes that inherit from Nat, as long as one provide an implementation of the "Add" methode. This makes pattern matching harder to work with in OOP languages and can mess with the original semantics you had in mind for youre type. However, current trends hints toward an increase integration of pattern matching in mainstream languages. In C# one can use the *sealed* keyword to prevent future users to inherited from a class. One can also simply add a private constructor in the abstract class to require that a class can be inherited inside its own scope, as such :

'''
abstract class Nat
{
    public abstract Nat Add(Nat other);
    public class Zero : Nat
    {
        public override Nat Add(Nat other)
        {
            return other;
        }
        public override Nat Mult(Nat other)
        {
            return new Zero();
        }

        public Zero() { }
    }
    public class Succ : Nat
    {
        public Nat prec;
        public override Nat Add(Nat other)
        {
            return new Succ(prec.Add(other));
        }
        public Succ(Nat prec) { this.prec = prec; }
    }
    private Nat() { }
}
'''
With this kind of hack, together with an abstract generic "Match<Nat>" method, one can reproduce pretty acuratly the semantics of our lean code.

# Interfaces and type classes
One of the goal of object oriented programing (OOP) is to ensure *substitution* and *encapsulation*. Among other things this means that you should design youre (OOP) code in such way one could swap out youre classes to other ones as long as the new one provide the same functionality. 
I think this is a kind of mathematical mindset. Mathematicians are always looking to understand what particular features of the stuffs they work with gave them their properties, in the hope of generalization.
In Lean, this is achievable with *type classes*. In other languages, the sames space is occupied with *interfaces* or *traits*. Scala in particular offers a powerfull trait system that provide the best of both.

Another problem of inheritance is that one often could want to inherit from multiples classes. For instance the real numbers are both a field and of a topological space. Worse, some mathematical object can be the same things, but in different way, for instance they posses two operations for which they are a group.
One way to overcome this problem is the composition over inheritance principle. Rather than inheriting from topological spaces, or fields, the real numbers have a field pointing toward an interface or a typeclass that ensure that they are these things.
Scala 3 propose the export keyword that allow one to use the methods b.m() of a field B in a class a as if they were methods of the class a.m().
  
# Syntax design

Syntax designs is often considers as less interesting that semantics for instance, and regarding programing in genereal, this is more or less for good reasons. However, there has been some research on the matter https://dl.acm.org/doi/10.1145/2534973. I our case, where education is at the core of our considerations, it could be more important. For instance in Lean, the type of an identier comes after the identifier itself
'''
>a:int=2 
In a lot of mainstream languages, it's the opposite
>int a=2;
'''
Some argue that having the identifier coming first is better for readability, because types matter less. Of course types matters a lot more to mathematicians.
  
# Mutability
In the world of FP languages, value are often immutable by default, which means that once an a value is assigned to an identifier, this identifier cannot be update with another value. Regarding formalization, it is almost a no brainer. If we have
 '''
 inductive Eq : Nat -> Nat → Prop
| Refl (n:Nat) : Eq n n
def AddZeroLeft : ∀ n:Nat, Eq (Add Nat.Zero n) n
| Nat.Zero := Eq.Refl Nat.Zero
| (Nat.Succ m) := Eq.Refl (Nat.Succ m)
'''
The "Eq" would lost all its semantic if we could simply update the value it depends on. However one could argue that a small amount of mutability could be useful; for instance, for epsilon-delta proof, one often has to change update the value of epsilon to epsilon/2 to obtain a desired property. This can actually be done safely in regard to dependant type with some constraints https://drops.dagstuhl.de/opus/volltexte/2018/9218/pdf/LIPIcs-ECOOP-2018-13.pdf.
  
# Other interesting features
Scala propose that if a class B is declared in the scope of another class A, the type a1.B and a2.B are not the same. Those types are called *path-dependant types*, because they depend on the path of inheritance. This is extremelly use full if we have a type "Graph" and a type "Node", the type system ensures that when one work with a instances "node:Node" of "Node", they belong to a single instance "graph:Graph". However, one cannot pass these type to functions, they have to be projected to "Graph#Node". If we could, those types woud dependant on a value "graph", which somewhat provide another path toward dependant type in an OOP language.
