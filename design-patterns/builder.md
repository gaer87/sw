## До

### Проблема с перегруженностью конструктора

```typescript
class BankAccount {
    private accountNumber: string;
    private currency: string;
    private overdraftLimit?: number;
    private interestRate?: number;
    private monthlyFee?: number;

    constructor(options: Record<string, string | number>) {
        this.accountNumber = options.accountNumber;
        this.currency = options.currency;
        this.overdraftLimit = options.overdraftLimit;
        this.interestRate = options.interestRate;
        this.monthlyFee = options.monthlyFee;
    }
}

const depositAccount = new BankAccount({
    accountNumber: '111222333',
    currency: 'USD',
    interestRate: 3.5 // <- исключительно для этого счёта
});

const overdraftAccount = new BankAccount({
    accountNumber: '444555666',
    currency: 'EUR',
    overdraftLimit: 1000, // <- исключительно для этого счёта
    monthlyFee: 50 // <- исключительно для этого счёта
});

// ...другие счета
// опции будут "раздуваться" при появлении новый счетов с новыми параметрами
```

### Проблема большого количества подклассов

```typescript
// Базовый класс банковского счёта
class BankAccount {
    constructor(
        public accountNumber: string,
        public currency: string
    ) {}

    public showDetails(): void {
        console.log(`
            Номер счёта: ${this.accountNumber}
            Валюта: ${this.currency}
        `);
    }
}

// 1. Депозитный счёт
class DepositAccount extends BankAccount {
    constructor(
        public accountNumber: string,
        public currency: string,
        public interestRate: number // <- исключительно для этого счёта
    ) {
        super(accountNumber, currency);
    }

    public showDetails(): void {
        super.showDetails();
        console.log(`Процентная ставка: ${this.interestRate}`);
    }
}

// 2. Счёт с овердрафтом
class OverdraftAccount extends BankAccount {
    constructor(
        public accountNumber: string,
        public currency: string,
        public overdraftLimit: number // <- исключительно для этого счёта
    ) {
        super(accountNumber, currency);
    }

    public showDetails(): void {
        super.showDetails();
        console.log(`Лимит овердрафта: ${this.overdraftLimit}`);
    }
}

// ...другие классы
```


## После

```typescript
class BankAccount {
    public accountNumber!: string; // Номер счёта
    public currency!: string; // Валюта счёта
    public overdraftLimit?: number; // Лимит овердрафта (опционально)
    public interestRate?: number; // Процентная ставка (опционально)
    public monthlyFee?: number; // Ежемесячная плата (опционально)

    // Метод для отображения информации о счёте
    public showDetails(): void {
        console.log(`
            Номер счёта: ${this.accountNumber}
            Валюта: ${this.currency}
            Лимит овердрафта: ${this.overdraftLimit ?? 'Нет'}
            Процентная ставка: ${this.interestRate ?? 'Нет'}
            Ежемесячная плата: ${this.monthlyFee ?? 'Нет'}
        `);
    }
}

interface BankAccountBuilder {
    setAccountNumber(accountNumber: string): this;
    setCurrency(currency: string): this;
    setOverdraftLimit(limit: number): this;
    setInterestRate(rate: number): this;
    setMonthlyFee(fee: number): this;
    build(): BankAccount;
}

class ConcreteBankAccountBuilder implements BankAccountBuilder {
    private account: BankAccount;

    constructor() {
        this.account = new BankAccount();
    }

    public setAccountNumber(accountNumber: string): this {
        this.account.accountNumber = accountNumber;
        return this;
    }

    public setCurrency(currency: string): this {
        this.account.currency = currency;
        return this;
    }

    public setOverdraftLimit(limit: number): this {
        this.account.overdraftLimit = limit;
        return this;
    }

    public setInterestRate(rate: number): this {
        this.account.interestRate = rate;
        return this;
    }

    public setMonthlyFee(fee: number): this {
        this.account.monthlyFee = fee;
        return this;
    }

    public build(): BankAccount {
        const result = this.account;
        this.account = new BankAccount(); // Сброс для следующего использования
        return result;
    }
}

// Директор не является обязательной частью данного паттерна
// создается исключительно для удобства (сборник готовых решений)
class BankAccountDirector {
    constructor(private builder: BankAccountBuilder) { }

    public constructStandardAccount(): BankAccount {
        return this.builder
            .setAccountNumber('1234567890')
            .setCurrency('RUB')
            .setMonthlyFee(100)
            .build();
    }

    public constructOverdraftAccount(): BankAccount {
        return this.builder
            .setAccountNumber('9876543210')
            .setCurrency('USD')
            .setOverdraftLimit(500)
            .setMonthlyFee(200)
            .build();
    }
}

const builder = new ConcreteBankAccountBuilder();

// Депозитный счёт
const depositAccount = builder
  .setAccountNumber('111222333')
  .setCurrency('USD')
  .setInterestRate(3.5)
  .build();

// Счёт с овердрафтом
const overdraftAccount = builder
  .setAccountNumber('444555666')
  .setCurrency('EUR')
  .setOverdraftLimit(1000)
  .setMonthlyFee(50)
  .build();

/** Использование директора */
const director = new BankAccountDirector(builder);
// Стандартный счёт
const standardAccount = director.constructStandardAccount();
// Счёт с овердрафтом
const overdraftFromDirector = director.constructOverdraftAccount();
```