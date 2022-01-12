# Objekt-orientierte Programmierung (OOP)

- Was ist ein Objekt?

```lisp
; Ein Funktionsaufruf
(+ 23 42 7 12)

; Eine Variable
(defvar pi 3.1415)

; Ein Objekt (eine "komplexe" Variable, die mehrere zusammengehörige
; Werte speichern kann)
(defvar person '(
  '(:first-name "Donald")
  '(:last-name "Duck")
  '(:get-location (lambda () "Entenhausen"))
))
```

```javascript
const person = {
  firstName: "Donald",
  lastName: "Duck",
  getLocation: () => "Entenhausen"
};
```

```csharp
public class Person
{
  public string FirstName { get; private set; }
  public string LastName { get; private set; }
  private string location;

  public Person (string firstName, string lastName, string location)
  {
    this.firstName = firstName;
    this.lastName = lastName;
    this.location = location;
  }

  public string GetLocation ()
  {
    return this.location;
  }
}

var person = new Person('Donald', 'Duck', 'Entenhausen');
```

- Eigenschaften von Objektorientierung
  - Klassen, Konstruktoren
  - Felder, Eigenschaften, Methoden
  - Zugriffsmodifizierer, Kapselung

```javascript
const Person = function (firstName, lastName, location) {
  this.firstName = firstName;
  this.lastName = lastName;
  this.getLocation = () => {
    return location;
  };
};

const person = new Person('Donald', 'Duck', 'Entenhausen');
```

- Typsysteme
  - C#, Java, C++, …: Nominales Typsystem
  - JavaScript, Go, …: Strukturelles Typsystem

## Typkompatibilität

```csharp
public class Person
{
  public string FirstName { get; private set; }
  public string LastName { get; private set; }

  public Person (string firstName, string lastName, string location)
  {
    this.firstName = firstName;
    this.lastName = lastName;
  }
}

public class User : Person
{
}

public class Administrator : Person
{
  public string EmergencyPhoneNumber { get; private set; }
}

// ...

public void Login (Person who)
{
  Console.WriteLine(who.FirstName);
  Console.WriteLine(who.LastName);

  // Won't work:
  Console.WriteLine(who.EmergencyPhoneNumber);
}
```

```
     Person
    /      \
User        Administrator
```

```csharp
public class Rectangle
{
  public int Width { get; private set; }
  public int Height { get; private set; }

  public Rectangle (int width, int height)
  {
    this.Width = width;
    this.Height = height;
  }

  public int GetArea ()
  {
    return this.Width * this.Height;
  }
}

// Blöde Idee, weil sich das Verhalten der Flächenberechnung
// ändert: Verdoppelt man zB die Höhe, vervierfacht sich die
// Fläche des Quadrats, aber die vom Rechteck verdoppelt sich
// lediglich!
public class Square : Rectangle
{
  public Square (int size) : base(size, size)
  {
  }
}

// Verletzung des Liskovschen Substitutionsprinzips (LSP)!
// Alternativ: Composition over Inheritance (COI)

public interface IWithArea
{
  int GetArea ();  
}

public interface IWithCircumference
{
  int GetCircumference ();
}

public class Rectangle : IWithArea, IWithCircumference
{
  public int Width { get; private set; }
  public int Height { get; private set; }

  public Rectangle (int width, int height)
  {
    this.Width = width;
    this.Height = height;
  }

  public int GetArea ()
  {
    return this.Width * this.Height;
  }

  public int GetCircumference ()
  {
    return 2 * this.Width + 2 * this.Height;
  }
}

public class Square : IWithArea, IWithCircumference
{
  private Rectangle rect;

  public Square (int size)
  {
    this.rect = new Rectangle(size, size);
  }

  public int GetArea ()
  {
    return this.rect.GetArea();
  }

  public int GetCircumference ()
  {
    return this.rect.GetCircumference();
  }
}

public class Line : IWithCircumference
{
  // ...

  public int GetCircumference ()
  {
    // ...
  }
}

// ...

public void DoSomething (IWithArea shape)
{
  var area = shape.GetArea();
  // ...
}
```

```
          IShape
         /      \
Rectangle        Square
```

- Viele, kleine Interfaces
  - Interface Segregation Principle (ISP)

```javascript
// Inheritance
class Rectangle {
  constructor (width, height) {
    this.width = width;
    this.height = height;
  }

  getArea () {
    return this.width * this.height;
  }

  getCircumference () {
    return 2 * this.width + 2 * this.height;
  }
}

class Square extends Rectangle {
  constructor (size) {
    super(size, size);
  }
}

// Composition
const Rectangle = function (width, height) {
  this.width = width;
  this.height = height;
  this.getArea = () => this.width * this.height;
  this.GetCircumference = () => 2 * this.width + 2 * this.height;
};

const Square = function (size) {
  this.rect = new Rectangle(size, size);
  this.getArea = () => this.rect.getArea();
  this.getCircumference = () => this.rect.getCircumference();
};

const doSomething = function (shape) {
  const area = shape.getArea();
  // ...
};
```

```typescript
interface WithArea {
  getArea: () => number;
}

class Rectangle implements WithArea {
  private width: number;
  private height: number;

  public constructor (width: number, height: number) {
    this.width = width;
    this.height = height;
  }

  public getArea (): number {
    return this.width * this.height;
  }

  public getCircumference (): number {
    return 2 * this.width + 2 * this.height;
  }
}

class Square implements WithArea {
  private rect: Rectangle;

  public constructor (size: number) {
    this.rect = new Rectangle(size, size);
  }

  public getArea (): number {
    return this.rect.getArea();
  }

  public getCircumference (): number {
    return this.rect.getCircumference();
  }
}

const doSomething = function (shape: WithArea): void {
  const area = shape.getArea();
  // ...
};
```

## Virtuelle Methoden

```csharp
public class Base
{
  public void NonVirtualFromBase () {}
  public virtual void VirtualFromBase () {}
  public void NonVirtualFromDerived () {}
}

public class Derived : Base
{
  public override void VirtualFromBase () {}
  public new void NonVirtualFromDerived () {}
}

// ...

public void DoSomething (Base obj) {
  // "Normale" Methode aus der Basisklasse, wird immer in der
  // Basisklasse aufgerufen, egal was für `obj` übergeben wird
  obj.NonVirtualFromBase();

  // Virtuelle Methode aus der Basisklasse, wird immer möglichst
  // weit unten in der Typhierarchie aufgerufen, in Abhängigkeit
  // davon, was als `obj` übergeben wird.
  obj.VirtualFromBase();

  // Nicht-virtuelle, aber geshadowte Methode aus der Basisklasse,
  // wird dort aufgerufen, was zum angegebenen Typ von `obj` passt,
  // in dem Fall (wegen der Signatur von DoSomething) also imnmer
  // die aus Base, es sei denn, `obj` würde nach Derived gecastet.
  obj.NonVirtualFromDerived();
}
```
