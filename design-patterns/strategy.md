## До

```ts
class OldPaymentProcessor {
    constructor(
        private unionPayBalance: number,
        private masterCardBalance: number,
        private visaBalance: number,
    ) {}

    // Метод для оплаты покупки, делённой на три части
    processPayment(totalAmount: number): void {
        const partAmount = totalAmount / 3;

        // Логика оплаты с UnionPay
        if (this.unionPayBalance >= partAmount) {
            this.unionPayBalance -= partAmount;
            console.log(`Оплата ${partAmount} с UnionPay прошла успешно. Остаток: ${this.unionPayBalance}`);
        } else {
            console.log(`Недостаточно средств на карте UnionPay.`);
        }

        // Логика оплаты с MasterCard
        if (this.masterCardBalance >= partAmount) {
            this.masterCardBalance -= partAmount;
            console.log(`Оплата ${partAmount} с MasterCard прошла успешно. Остаток: ${this.masterCardBalance}`);
        } else {
            console.log(`Недостаточно средств на карте MasterCard.`);
        }

        // Логика оплаты с Visa
        if (this.visaBalance >= partAmount) {
            this.visaBalance -= partAmount;
            console.log(`Оплата ${partAmount} с Visa прошла успешно. Остаток: ${this.visaBalance}`);
        } else {
            console.log(`Недостаточно средств на карте Visa.`);
        }
    }
}

const oldProcessor = new OldPaymentProcessor(200, 100, 150);
oldProcessor.processPayment(270);
```

#### Проблема:
> Вся логика оплаты находится в одном большом методе, что усложняет понимание кода. Добавление новой карты требует изменения внутри класса (нарушение принципа открытости/закрытости)

> Проблема усугубляется, когда if'ы this.unionPayBalance >= partAmount и т.д встречаются и в других методах. Класс обрастает большим кол-вом if'ов, что усложняет поддержку и читаемость

## После

```ts
interface PaymentStrategy {
    pay(amount: number): boolean;  // Метод возвращает true, если оплата успешна
}

class UnionPay implements PaymentStrategy {
    constructor(private balance: number) {}

    pay(amount: number): boolean {
        if (this.balance >= amount) {
            this.balance -= amount;
            console.log(`Оплата ${amount} с UnionPay прошла успешно. Остаток: ${this.balance}`);
            return true;
        } else {
            console.log(`Недостаточно средств на карте UnionPay.`);
            return false;
        }
    }
}

class MasterCard implements PaymentStrategy {
    constructor(private balance: number) {}

    pay(amount: number): boolean {
        if (this.balance >= amount) {
            this.balance -= amount;
            console.log(`Оплата ${amount} с MasterCard прошла успешно. Остаток: ${this.balance}`);
            return true;
        } else {
            console.log(`Недостаточно средств на карте MasterCard.`);
            return false;
        }
    }
}

class Visa implements PaymentStrategy {
    constructor(private balance: number) {}

    pay(amount: number): boolean {
        if (this.balance >= amount) {
            this.balance -= amount;
            console.log(`Оплата ${amount} с Visa прошла успешно. Остаток: ${this.balance}`);
            return true;
        } else {
            console.log(`Недостаточно средств на карте Visa.`);
            return false;
        }
    }
}

class PaymentProcessor {
    constructor(private strategy: PaymentStrategy) {}

    setStrategy(strategy: PaymentStrategy): void {
        this.strategy = strategy;
    }

    processPayment(amount: number): boolean {
        return this.strategy.pay(amount);
    }
}

// Инициализация карт с определёнными балансами
const unionPayCard = new UnionPay(100);
const masterCard = new MasterCard(150);
const visaCard = new Visa(200);

// Инициализация процессора оплаты
const processor = new PaymentProcessor(unionPayCard);

// Сумма покупки
const totalAmount = 270; // Например, покупка стоит 270 единиц
const partAmount = totalAmount / 3;  // Каждая карта оплатит 1/3

// Оплата первой части UnionPay
console.log("Оплата первой части:");
processor.setStrategy(unionPayCard);
processor.processPayment(partAmount);

// Оплата второй части MasterCard
console.log("Оплата второй части:");
processor.setStrategy(masterCard);
processor.processPayment(partAmount);

// Оплата третьей части Visa
console.log("Оплата третьей части:");
processor.setStrategy(visaCard);
processor.processPayment(partAmount);

```

