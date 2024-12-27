## До

```ts
// Business logic
class App {
    public accept(): void {}
    public decline(): void {}
    public printDocuments(documentId: string): void {}
}

// UI
abstract class Button {
    public constructor(public name: string = 'Ok') {}

    public render(): string {
        const el = document.createElement('button')
        el.innerText = this.name

        el.addEventListener('click', this.onClick)

        return el
    }

    abstract onClick(e: Event): void
}

class OkButton extends Button {
    public constructor(app: Applicaion) {
        super();
        this.app = app;
    }

    public onClick(e: Event): void {
        this.app.accept();
    }
}

class CancelButton extends Button {
    public constructor(app: Applicaion) {
        super();
        this.app = app;
    }

    public onClick(e: Event): void {
        this.app.decline();
    }
}

class PrintButton extends Button {
    public constructor(app: Applicaion, documentId: string) {
        super();
        this.app = app;
        this.documentId = documentId;
    }

    public onClick(e: Event): void {
        this.app.printDocuments(this.documentId);
    }
}

// Client code
class Application {
    public constructor(): void {
        this.app = new App()

        this.okButton = new OkButton(this.app)
        this.cancelButton = new CancelButton(this.app)
        this.printButton = new PrintButton(this.app, this.getCheckedDoc())

        document.addEventListener('keydown', this.onKeyDown)
    }

    public render(): HTMLElement {
        return document.body.append(
            this.okButton,
            this.cancelButton,
            this.printButton,
        )
    }

    public getCheckedDoc(): string {
        return 'doc1'
    }
    
    public onKeyDown = (e: Event): void => {
        // print
        if (e.code === 'p') {
            this.app.printDocuments(this.getCheckedDoc());
        }
    }
}

new Applicaion()
```

## После

```ts
// Business logic
class App {
    public accept(): void {}
    public decline(): void {}
    public printDocuments(documentId: string): void {}
}

interface Command {
    execute(): void
}

class OkCommand implements Command {
    public constructor(public app: App) {}

    public execute(): void {
        this.app.accept();
    }
}

class CancelCommand implements Command {
    public constructor(public app: App) {}

    public execute(): void {
        this.app.decline();
    }
}

class PrintCommand implements Command {
    public constructor(app: App, application: Application) {
        this.app = app;
        this.documentId = documentId;
    }

    public execute(): void {
        const documentId = application.getCheckedDoc()

        this.app.decline(documentId);
    }
}

// UI
class Button {
    public constructor(public name: string = 'Ok', private command: Command) {}

    public render(): string {
        const el = document.createElement('button')
        el.innerText = this.name

        el.addEventListener('click', this.onClick)

        return el
    }

    onClick(e: Event): void {
        this.command.execute();
    }
}

// Client code
class Application {
    public constructor(): void {
        this.app = new App()

        const ok = new OkCommand(this.app)
        this.okButton = new OkButton(ok)
        
        const cancel = new CancelCommand(this.app)
        this.cancelButton = new CancelButton(cancel)
        
        this.print = new PrintCommand(this.app, this)
        this.printButton = new PrintButton(this.print)

        document.addEventListener('keydown', this.onKeyDown)
    }

    public render(): HTMLElement {
        return document.body.append(
            this.okButton,
            this.cancelButton,
            this.printButton,
        )
    }

    public getCheckedDoc(): string {
        return 'doc1'
    }

    public onKeyDown = (e: Event): void => {
        // print
        if (e.code === 'p') {
            this.print.execute()
        }
    }
}

new Applicaion()
```

## Вопросы

> К какому слою относится команда? Инфраструктурный/слой приложения?
>> ...
