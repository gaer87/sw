## До

```ts
class Figure {
    draw() {
        console.log('draw')
    }
}

class Circle extends Figure {
    draw() {
        console.log(`circle draw`)
    }
}

class Square extends Figure {
    draw() {
        console.log(`square draw`)
    }
}

// The new requirement: add color
class RedCircle extends Figure {
    draw() {
        const color = 'red';
        console.log(`${color} circle draw`)
    }
}

class RedSquare extends Figure {
    draw() {
        const color = 'red';
        console.log(`${color} square draw`)
    }
}

class BlueCircle extends Figure {
    draw() {
        const color = 'blue';
        console.log(`${color} circle draw`)
    }
}

class BlueSquare extends Figure {
    draw() {
        const color = 'blue';
        console.log(`${color} square draw`)
    }
}

// The new requirement: add triangle
class RedTriangle extends Figure {
    draw() {
        const color = 'red';
        console.log(`${color} triangle draw`)
    }
}

class BlueTriangle extends Figure {
    draw() {
        const color = 'blue';
        console.log(`${color} triangle draw`)
    }
}

// The new requirement: add color
class YellowCircle extends Figure {
    draw() {
        const color = 'yellow';
        console.log(`${color} circle draw`)
    }
}

class YellowSquare extends Figure {
    draw() {
        const color = 'yellow';
        console.log(`${color} square draw`)
    }
}

class YellowTriangle extends Figure {
    draw() {
        const color = 'yellow';
        console.log(`${color} triangle draw`)
    }
}

const redCircle = new RedCircle()
const blueCircle = new BlueCircle()
const yellowCircle = new YellowCircle()

const redSquare = new RedSquare()
const blueSquare = new BlueSquare()
const yellowSquare = new YellowSquare()

const redTriangle = new RedTriangle()
const blueTriangle = new BlueTriangle()
const yellowTriangle = new YellowTriangle()
```

## После

```ts
interface Color {
    getColor(): string;
}

class Red implements Color {
    getColor(): string {
        return 'red'
    }
}

class Blue implements Color {
    getColor(): string {
        return 'blue'
    }
}

class Yellow implements Color {
    getColor(): string {
        return 'yellow'
    }
}

class Figure {
    constructor(private color: Color) {}
}

class Circle extends Figure {
    draw() {
        console.log(`${this.color.getColor()} circle draw`)
    }
}

class Square extends Figure {
    draw() {
        console.log(`${this.color.getColor()} square draw`)
    }
}

class Triangle extends Figure {
    draw() {
        console.log(`${this.color.getColor()} triangle draw`)
    }
}

const red = new Red()
const blue = new Blue()
const yellow = new Yellow()

const redCircle = new Circle(red)
const blueCircle = new Circle(blue)
const yellowCircle = new Circle(yellow)

const redSquare = new Square(blue)
const blueSquare = new Square(blue)
const yellowSquare = new Square(blue)

const redTriangle = new Triangle(blue)
const blueTriangle = new Triangle(blue)
const yellowTriangle = new Triangle(blue)
```

## Вопросы

> ...
>> ...
