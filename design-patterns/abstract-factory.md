```ts
/**
 * Интерфейс для различных видов пицц
 */
interface Pizza {
    bake(): void;
}
/**
 * Интерфейс для различных напитков
 */
interface Drink {
    prepare(): void;
}
/**
 * Интерфейс абстрактной фабрики
 * методы возвращают абстрактные продукты
 */
interface AbstractPizzaStore {
    createPizza(): Pizza;
    createDrink(): Drink;
}
/**
 * Группа конкретных продуктов
 */
type ProductsGroup = {
    pizza: Pizza;
    drink: Drink;
}
/**
 * Класс для создания конкретной пиццы,
 * которая будет относиться к отдельной "московской" группе
 */
class MoscowPizza implements Pizza {
    bake(): void {
        console.log('Bake a Moscow pizza')
    }    
}
/**
 * Класс для создания конкретного напитка,
 * который будет относиться к отдельной "московской" группе
 */
class MoscowDrink implements Drink {
    prepare(): void {
        console.log('Prepare a Moscow drink')
    }
}
/**
 * Класс для создания конкретной группы пиццерии
 * будет производить только "московские" продукты
 */
class MoscowPizzaStore implements AbstractPizzaStore {
    createDrink(): Drink {
        return new MoscowDrink()
    }

    createPizza(): Pizza {
        return new MoscowPizza()
    }
}
/**
 * Класс для создания конкретной пиццы,
 * которая будет относиться к отдельной "питерской" группе
 */
class SPBPizza implements Pizza {
    bake(): void {
        console.log('Bake a SPB pizza')
    }
}
/**
 * Класс для создания конкретного напитка,
 * который будет относиться к отдельной "питерской" группе
 */
class SPBDrink implements Drink {
    prepare(): void {
        console.log('Prepare a SPB drink')
    }
}
/**
 * Класс для создания конкретной группы пиццерии
 * будет производить только "питерские" продукты
 */
class SPBPizzaStore implements AbstractPizzaStore {
    createDrink(): Drink {
        return new SPBDrink()
    }

    createPizza(): Pizza {
        return new SPBPizza()
    }
}
/**
 * Возвращает город пиццерии
 */
function getConfig(): "Moscow" | "SPB" {
    return "Moscow";
}
/**
 * Класс создания продуктов 
 */
class App {
    pizza: Pizza
    drink: Drink

    constructor(pizzaStore: AbstractPizzaStore) {
        this.pizza = pizzaStore.createPizza();
        this.drink = pizzaStore.createDrink();
    }
}
/**
 * Конфигурация для создания нужной нам фабрики
 */
class Configuration {
    products: ProductsGroup;
    
    constructor() {
        const city = getConfig();
        let pizzaStore: AbstractPizzaStore;

        if (city === "Moscow") {
            pizzaStore = new MoscowPizzaStore();
        } else if(city === "SPB") {
            pizzaStore = new SPBPizzaStore();
        } else {
            throw new Error('Пиццерия не найдена')
        }

        this.products = new App(pizzaStore);
    }
}

const store = new Configuration();
store.products.pizza.bake();
store.products.drink.prepare();
```
