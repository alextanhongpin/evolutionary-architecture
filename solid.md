# SOLID Principles

- Single responsibility principle. A class should have only a single responsibility.
- Open/Closed principle. Software entities should be open for extension, but closed for modifications.
- Liskov substitution principle. Objects in a program should be replaceable with instances of their subtypes without altering the correctness of that program.
- Interface segregration principle. Many client-specific interfaces are better than one general-purpose-interface.
- Dependency inversion principle. One should depend upon abstraction, not concretions.

Find related examples/use-cases to the above principles.


## Open/Closed Principle


The function is open from extension, but it is also open for modifications (getArea method).
```ts
class Rectangle {
  width: number
  height: number
}

class Circle {
  radius: number
}

function getArea(shapes: (Rectangle | Circle)[]) {
  return shapes.reduce((acc, shape) => {
    if (shape instanceof Rectangle) {
      return acc + (shape.width * shape.height)
    } else if (shape instanceof Circle) {
      return acc + (shape.radius * shape.radius * Math.PI)
    } else {
      throw new Error('unknown shape')
    }
  }, 0)
}
```

Better. Take advantage of polymorphism. The new solution allows us to add new shapes (open for extension) without modifying the existing source code (closed for modification).

```ts
interface Shape {
  area(): number
}

class Rectangle implements Shape {
  width: number
  height: number
  area(): number {
    return this.width * this.height
  }
}

class Circle implements Shape {
  radius: number
  area(): number {
    return this.radius * this.radius * Math.PI
  }
}

function getArea(shapes: Shape[]) {
  return shapes.reduce((acc, shape) => {
    return acc + shape.area()
  }, 0)
}
```

## Liskov substitution principle

## Interface segregation principle


All shapes needs to implement both `area` and `serialize` method. What if one of the shape does not require the `serialize` method?
```ts
interface Shape {
  area(): number
  serialize(): string
}
```


Split the interface to its respective responsibility.

```ts
interface Shape {
  area(): number
}

interface Serializable {
  serialize(): string
}
```
