```ts
// Composite interface
interface ProductComponent {
  getPrice(): number;
}

// Leaf
class Product implements ProductComponent {
  private price: number;

  constructor(price: number) {
    this.price = price;
  }

  getPrice(): number {
    return this.price;
  }
}

// Leaf
class DiscountedProduct implements ProductComponent {
  private price: number;
  private discount: number;

  constructor(price: number, discount: number) {
    this.price = price;
    this.discount = discount;
  }

  getPrice(): number {
    return this.price - this.discount;
  }
}

// Composite
class ProductContainer implements ProductComponent {
  private children: ProductComponent[] = [];

  add(component: ProductComponent): void {
    this.children.push(component);
  }

  getPrice(): number {
    return this.children.reduce(
      (totalPrice, child) => totalPrice + child.getPrice(),
      0
    );
  }
}

// Client
const laptop = new Product(300);
const phone = new DiscountedProduct(200, 50);

const smallBox = new ProductContainer();
smallBox.add(laptop);

// Output: Total price of small Box: 300
console.log("Total price of small Box:", smallBox.getPrice());

const bigBox = new ProductContainer();
bigBox.add(phone);
bigBox.add(smallBox);

// Output: Total price of big Box: 450
console.log("Total price of big Box:", bigBox.getPrice());
```
