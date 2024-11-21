```ts
/**
 * Интерфейс для различных состояний документа.
 * Каждое состояние должно реализовывать метод `handle`,
 * который выполняет логику для данного состояния.
 */
interface IDocumentState {
  handle(document: DocumentManagement): void;
}

/**
 * Класс для состояния "Черновик".
 * Реализует логику перехода из состояния черновика в модерацию.
 */
class DraftDocumentState implements IDocumentState {
  handle(document: DocumentManagement): void {
    // Действия, выполняемые для черновика (например, публикация черновика).
    console.log("Черновик опубликован пользователем");
    // Перевод документа в состояние "Модерация".
    document.setState(new ModerationDocumentState());
  }
}

/**
 * Класс для состояния "Модерация".
 * Реализует логику проверки документа и перехода к публикации.
 */
class ModerationDocumentState implements IDocumentState {
  handle(document: DocumentManagement): void {
    // Действия, выполняемые при модерации документа.
    console.log("Проводится модерация");
    // Перевод документа в состояние "Публикация".
    document.setState(new PublicationDocumentState());
  }
}

/**
 * Класс для состояния "Публикация".
 * Реализует логику завершения работы с документом и возврат к черновику.
 */
class PublicationDocumentState implements IDocumentState {
  handle(document: DocumentManagement): void {
    // Действия, выполняемые при публикации документа.
    console.log("Публикация одобрена");
    // Перевод документа обратно в состояние "Черновик".
    document.setState(new DraftDocumentState());
  }
}

/**
 * Класс для управления документом.
 * Содержит текущее состояние документа и метод для выполнения действий
 * в зависимости от текущего состояния.
 */
class DocumentManagement {
  private state: IDocumentState; // Текущее состояние документа.

  constructor() {
    // Установка начального состояния документа — "Черновик".
    this.state = new DraftDocumentState();
  }

  /**
   * Метод для изменения текущего состояния документа.
   * @param state Новое состояние документа.
   */
  public setState(state: IDocumentState) {
    this.state = state;
  }

  /**
   * Метод для выполнения действия, соответствующего текущему состоянию документа.
   */
  public doSmth(): void {
    this.state.handle(this);
  }
}

// Создаём экземпляр класса управления документом.
const documentManagement = new DocumentManagement();

// Последовательно выполняем действия, изменяя состояние документа.
documentManagement.doSmth(); // Черновик опубликован пользователем
documentManagement.doSmth(); // Проводится модерация
documentManagement.doSmth(); // Публикация одобрена
```
