## До

```ts
class Truck {
  deliver() {
    console.log('truck ship');
  }
  refuel() {
    console.log('truck refuel');
  }
}

class Ship {
  transport() {
    console.log('Ship transport');
  }
  refuel() {
    console.log('Ship refuel');
  }
}

// CREATOR
class Logistics {
  private truck: Truck;
  private ship: Ship;

  public constructor(private type: string) {
    if (type === 'truck') {
      this.truck = new Truck();
    } else {
      this.ship = new Ship();
    }
  }

  doThis() {
    if (this.type === 'truck') {
      this.truck.deliver();
    } else {
      this.ship.transport();
    }
  }

  doThat() {
    const truck = new Truck();
    this.truck.refuel();
  }
}

class Application {
  public constructor() {
    const config = 'ship';
    const logistic = new Logistics(config);
    logistic.doThis();
  }
}
```

## После

```ts
interface Transport {
  deliver(): void
  refuel(): void
}

class Truck implements Transport {
  deliver() {
    console.log('truck ship');
  }

  refuel() {
    console.log('truck refuel');
  }
}

class Ship implements Transport {
  deliver() {
    console.log('Ship deliver');
  }

  refuel() {
    console.log('Ship refuel');
  }
}

class Logistics {
  private transport: Transport;

  public constructor() {
    this.transport = this.createTransport()
  }

  doThis() {
    this.transport.deliver();
  }

  doThat() {
    this.transport.refuel();
  }

  // ФАБРИЧНЫЙ МЕТОД
  createTransport(): Transport {
    // Дефолтная реализация. Можно сделать метод абстрактным 
    return new Truck();
  }
}

// ПОДКЛАСС БАЗОВОГО КЛАССА с переопределением ФАБРИЧНОГО МЕТОДА
class LogisticsTruck extends Logistics {
  createTransport(): Transport {
    return new Truck();
  }
}

class LogisticShip extends Logistics {
  createTransport(): Transport {
    return new Ship();
  }
}

class Application {
  public constructor() {
    const config = 'ship';
    let logistic: Logistics;
    if (config === 'ship') {
      logistic = new LogisticShip();
    } else {
      logistic = new LogisticsTruck();
    }
    logistic.doThis();
  }
}
```

## Вопросы

> Чем отличается от **простой фабрики**?
>> Предполагается что у *CREATOR* есть подклассы со своей логикой. Благодаря выносу создания некого объекта в фабричный метод, подклассы могут его переопределять,
>> возвращая другой объект. Позволяет не менять метод в базовом классе: алгоритм остается прежний, но за счет выноса создания объекта в фабричный метод менять поведение.
>> ...


