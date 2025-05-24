Assolutamente! Proseguiamo con la **Lezione 26: Integrazione dei Signals con il Framework**. Dopo aver compreso i concetti base di `signal`, `computed`, ed `effect`, vediamo come queste nuove primitive di reattività si integrano e interagiscono con le altre parti del framework Angular.

---

### Modulo 10: Signals in Angular

**Lezione 26: Integrazione dei Signals con il Framework**

I Signals non sono un sistema isolato; sono profondamente integrati nell'ecosistema Angular per migliorare la developer experience e le performance. Questa integrazione si manifesta in diverse aree chiave.

**1. Utilizzo dei Signals nei Componenti e nei Servizi**

Come abbiamo visto negli esempi precedenti, puoi definire e utilizzare `signal`, `computed`, ed `effect` direttamente all'interno delle classi dei tuoi componenti e servizi.

*   **Nei Componenti:**
    *   I `signal` e i `computed` sono usati per mantenere lo stato del componente e i valori derivati che verranno visualizzati nel template.
    *   Gli `effect` possono essere usati per reagire ai cambiamenti di questi signal ed eseguire side effects (es. logging, interazioni con `localStorage`, chiamate a servizi se non strettamente legate alla derivazione di un altro stato).
    *   **Template Binding:** Quando usi un signal nel template (es. `{{ mioSignal() }}`), Angular traccia automaticamente questa dipendenza. Se `mioSignal` cambia, Angular sa che quella specifica parte del template deve essere aggiornata, portando a una change detection più granulare.

    ```typescript
    // Esempio in un componente
    import { Component, signal, computed, effect } from '@angular/core';

    @Component({
      selector: 'app-signal-component-example',
      standalone: true,
      template: `
        <p>Contatore: {{ count() }}</p>
        <p>Doppio del contatore (computed): {{ doubleCount() }}</p>
        <button (click)="increment()">Incrementa</button>
      `
    })
    export class SignalComponentExample {
      count = signal(0); // Stato scrivibile
      doubleCount = computed(() => this.count() * 2); // Stato derivato

      constructor() {
        effect(() => {
          console.log(`Il contatore è cambiato in: ${this.count()}`);
        });
      }

      increment() {
        this.count.update(c => c + 1);
      }
    }
    ```

*   **Nei Servizi:**
    *   I servizi possono esporre stato reattivo usando `signal` e `computed`. Questo permette ai componenti di sottoscriversi (indirettamente, tramite lettura nel template o in un `computed`/`effect`) a questo stato in modo efficiente.
    *   Questo può essere un'alternativa più leggera a `BehaviorSubject` per alcuni scenari di state management basati su servizi.

    ```typescript
    // user-settings.service.ts
    import { Injectable, signal, computed } from '@angular/core';

    @Injectable({
      providedIn: 'root'
    })
    export class UserSettingsService {
      private _theme = signal<'light' | 'dark'>('light');
      private _fontSize = signal<number>(16);

      // Espone i signal come read-only (sebbene siano WritableSignal internamente)
      // o direttamente se si vuole permettere la modifica solo tramite metodi del servizio.
      // Per esporli read-only, si potrebbe fare:
      // public readonly theme = this._theme.asReadonly();
      // public readonly fontSize = this._fontSize.asReadonly();
      // Ma per semplicità, esponiamo direttamente i WritableSignal qui,
      // assumendo che le modifiche avvengano tramite metodi del servizio.

      public readonly theme = this._theme; // Espone il WritableSignal
      public readonly fontSize = this._fontSize; // Espone il WritableSignal

      public readonly themeSettingsDescription = computed(() => {
        return `Tema corrente: ${this.theme()}, Dimensione Font: ${this.fontSize()}px`;
      });

      constructor() {
        effect(() => {
          console.log(`Impostazioni Utente Cambiate: ${this.themeSettingsDescription()}`);
          // Potresti salvare le impostazioni in localStorage qui
        });
      }

      setTheme(newTheme: 'light' | 'dark') {
        this._theme.set(newTheme);
      }

      increaseFontSize() {
        this._fontSize.update(size => size + 1);
      }

      decreaseFontSize() {
        this._fontSize.update(size => Math.max(10, size - 1)); // Non scendere sotto 10px
      }
    }
    ```
    Un componente potrebbe poi iniettare `UserSettingsService` e leggere `theme()` o `fontSize()` nel suo template o in un `computed` signal.

**2. Interoperabilità con RxJS (`toSignal`, `toObservable`)**

Angular riconosce che RxJS è una parte potente e consolidata dell'ecosistema e che molte applicazioni esistenti (e future) lo utilizzeranno. Pertanto, fornisce utility per convertire facilmente tra Signals e Observables. Queste utility si trovano in `@angular/core/rxjs-interop`.

*   **`toSignal(source: Observable<T>, options?: ToSignalOptions)`**:
    *   Converte un `Observable` in un `Signal`.
    *   Il signal restituito avrà il valore dell'ultima emissione dell'Observable.
    *   Richiede un contesto di iniezione per gestire la sottoscrizione e l'unsubscribe automaticamente.
    *   **`options`**:
        *   `initialValue`: (Obbligatorio se l'Observable potrebbe non emettere subito, o se vuoi un valore prima della prima emissione). Il valore che il signal avrà prima che l'Observable emetta il suo primo valore. Se l'Observable emette sincronicamente alla sottoscrizione, questo valore potrebbe non essere mai visto.
        *   `requireSync`: (Booleano, default `false`). Se `true`, l'Observable sorgente *deve* emettere un valore sincronicamente al momento della sottoscrizione, altrimenti viene lanciato un errore. Utile se sai che l'Observable è "hot" o si comporta come un `BehaviorSubject`.
        *   `injector`: (Opzionale) Per specificare un contesto di iniezione se `toSignal` viene chiamato al di fuori di uno.

    ```typescript
    // data-loader.service.ts
    import { Injectable, inject } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    import { toSignal } from '@angular/core/rxjs-interop'; // Importa toSignal
    import { User } from './user.model'; // Assumiamo un'interfaccia User
    import { Observable, of } from 'rxjs';
    import { startWith } from 'rxjs/operators';

    @Injectable({ providedIn: 'root' })
    export class DataLoaderService {
      private http = inject(HttpClient);

      // Esempio: caricare dati utente da un'API
      private user$: Observable<User | null> = this.http.get<User>('/api/user');

      // Converte l'Observable in un signal.
      // Forniamo un initialValue perché la chiamata HTTP è asincrona.
      public currentUser = toSignal(this.user$, { initialValue: null });

      // Esempio con un Observable che emette subito (come un BehaviorSubject)
      private countSource = new BehaviorSubject<number>(0);
      public currentCount = toSignal(this.countSource); // Non serve initialValue perché BehaviorSubject ha un valore iniziale

      // Esempio con startWith per garantire un valore iniziale all'Observable
      private status$: Observable<string> = of('Loaded').pipe(startWith('Loading...'));
      public currentStatus = toSignal(this.status$); // startWith assicura un valore iniziale
    }
    ```
    Nel componente:
    ```typescript
    // user-display.component.ts
    // import { Component, inject } from '@angular/core';
    // import { DataLoaderService } from './data-loader.service';
    //
    // @Component({
    //   selector: 'app-user-display',
    //   standalone: true,
    //   template: `
    //     <div *ngIf="dataLoader.currentUser() as user; else loadingUser">
    //       <p>Utente: {{ user.name }}</p>
    //     </div>
    //     <ng-template #loadingUser><p>Caricamento utente...</p></ng-template>
    //     <p>Conteggio: {{ dataLoader.currentCount() }}</p>
    //     <p>Stato: {{ dataLoader.currentStatus() }}</p>
    //   `
    //   // Assicurati di importare CommonModule per *ngIf
    //   imports: [CommonModule]
    // })
    // export class UserDisplayComponent {
    //   dataLoader = inject(DataLoaderService);
    // }
    ```

*   **`toObservable(source: Signal<T>, options?: ToObservableOptions)`**:
    *   Converte un `Signal` (writable o computed) in un `Observable`.
    *   L'Observable emetterà il valore corrente del signal al momento della sottoscrizione e poi ogni volta che il valore del signal cambia.
    *   Richiede un contesto di iniezione per creare l'effetto che traccia il signal.
    *   **`options`**:
        *   `injector`: (Opzionale) Per specificare un contesto di iniezione.

    ```typescript
    // settings.service.ts
    import { Injectable, signal, Injector } from '@angular/core';
    import { toObservable } from '@angular/core/rxjs-interop';
    import { Observable } from 'rxjs';
    import { debounceTime, distinctUntilChanged } from 'rxjs/operators';

    @Injectable({ providedIn: 'root' })
    export class SettingsService {
      searchTerm = signal<string>('');

      // Converte il signal searchTerm in un Observable
      // Utile se vuoi applicare operatori RxJS come debounceTime
      public searchTerm$: Observable<string>;

      constructor(private injector: Injector) {
        this.searchTerm$ = toObservable(this.searchTerm, { injector: this.injector }).pipe(
          debounceTime(300), // Esempio: aspetta 300ms dopo l'ultimo cambiamento
          distinctUntilChanged() // Emetti solo se il valore è effettivamente cambiato
        );

        // Un componente o un altro servizio potrebbe sottoscriversi a searchTerm$
        // per reagire ai cambiamenti debounced del termine di ricerca.
        this.searchTerm$.subscribe(term => {
          console.log(`Termine di ricerca (debounced dall'Observable): ${term}`);
          // Esegui una ricerca API qui...
        });
      }

      updateSearchTerm(term: string) {
        this.searchTerm.set(term);
      }
    }
    ```

**3. Input Basati su Signal (`input()`, `model()`)**

Angular v16 ha introdotto `input()` per definire gli input dei componenti come signal, e Angular v17.1 ha introdotto `model()` per il two-way binding basato su signal.

*   **`input<T>(initialValue?: T, options?: InputSignalOptions<T>)`**:
    *   Crea un `Signal` read-only che riceve il suo valore dalla proprietà di input del componente.
    *   Puoi fornire un valore iniziale.
    *   **`options`**:
        *   `alias`: Per specificare un alias pubblico per l'input (come con `@Input()`).
        *   `required`: (Booleano, introdotto in v16.1) Se `true`, l'input deve essere fornito, altrimenti viene lanciato un errore a runtime (o un errore di compilazione se `strictTemplates` è attivo).
    *   Sostituisce la necessità di `@Input()` per molti casi d'uso e si integra meglio con la reattività basata su signal.

    ```typescript
    // child.component.ts
    import { Component, input, InputSignal, computed, Signal } from '@angular/core';

    @Component({
      selector: 'app-child',
      standalone: true,
      template: `
        <p>Messaggio dal genitore: {{ message() }}</p>
        <p>Messaggio trasformato: {{ transformedMessage() }}</p>
        <p>ID Utente (obbligatorio): {{ userId() }}</p>
      `
    })
    export class ChildComponent {
      // Definisce un input 'message' come signal
      message: InputSignal<string> = input<string>(''); // Valore iniziale ''
      // Definisce un input 'userId' obbligatorio
      userId: InputSignal<number> = input.required<number>();

      // Puoi usare i signal di input in computed signals
      transformedMessage: Signal<string> = computed(() => `CHILD: ${this.message().toUpperCase()}`);
    }

    // parent.component.ts
    // <app-child [message]="parentMessage" [userId]="currentUserId"></app-child>
    ```

*   **`model<T>(initialValue?: T, options?: ModelSignalOptions<T>)`**:
    *   Crea un `WritableSignal` speciale che supporta il two-way binding `[( )]` in modo simile a `[(ngModel)]` ma basato su signal.
    *   Puoi fornire un valore iniziale.
    *   **`options`**:
        *   `alias`: Per l'alias (come con `@Input()`).
        *   `required`: Per input obbligatori.
    *   Quando usi `[(modelProperty)]="parentSignal"`:
        *   Il valore di `parentSignal` viene passato a `modelProperty`.
        *   Quando `modelProperty` viene aggiornato nel figlio (usando `.set()` o `.update()`), il cambiamento viene propagato automaticamente a `parentSignal`.

    ```typescript
    // custom-input.component.ts
    import { Component, model, ModelSignal } from '@angular/core';

    @Component({
      selector: 'app-custom-input',
      standalone: true,
      template: `
        <label>{{ label() }}:</label>
        <input
          type="text"
          [value]="value()"
          (input)="onInput($event)">
        <button (click)="clearValue()">Pulisci (interno)</button>
      `
    })
    export class CustomInputComponent {
      value: ModelSignal<string> = model<string>(''); // Two-way binding signal
      label: ModelSignal<string> = model<string>('Default Label'); // Può anche essere usato come input normale

      onInput(event: Event) {
        // Aggiorna il model signal, che propagherà il cambiamento al genitore
        this.value.set((event.target as HTMLInputElement).value);
      }

      clearValue() {
        this.value.set('');
      }
    }

    // app.component.ts
    import { Component, signal, WritableSignal } from '@angular/core';
    import { CustomInputComponent } from './custom-input/custom-input.component';

    @Component({
      selector: 'app-root',
      standalone: true,
      imports: [CustomInputComponent],
      template: `
        <h1>App Two-Way Binding con Model Signal</h1>
        <app-custom-input [(value)]="appInputValue" [label]="'Nome App'"></app-custom-input>
        <p>Valore nel genitore: {{ appInputValue() }}</p>
        <button (click)="changeAppValue()">Cambia Valore dal Genitore</button>
      `
    })
    export class AppComponent {
      appInputValue: WritableSignal<string> = signal('Test Iniziale');

      changeAppValue() {
        this.appInputValue.set('Valore cambiato dal genitore!');
      }
    }
    ```

**4. Query Basate su Signal (`viewChild`, `contentChild`, `viewChildren`, `contentChildren`)**

Angular v17 ha introdotto versioni basate su signal per le query di vista e di contenuto, fornendo un modo più reattivo e semplice per accedere a elementi o direttive figlio.

*   **`viewChild<T>(selector, options?: ViewChildSignalOptions)`**:
    *   Restituisce un `Signal<T | undefined>` che contiene un riferimento al primo elemento, componente o direttiva che matcha il selettore nella vista del componente.
    *   Il signal si aggiorna se l'elemento appare o scompare (es. a causa di `*ngIf`).
    *   **`options`**:
        *   `read`: Per leggere una dependency injection token specifica dall'elemento.
        *   `static`: (Deprecato e non necessario con i signal query).

*   **`viewChildren<T>(selector, options?: ViewChildrenSignalOptions)`**:
    *   Restituisce un `Signal<readonly T[]>` che contiene un array di riferimenti a tutti gli elementi, componenti o direttive che matchano il selettore nella vista.

*   **`contentChild<T>(...)` e `contentChildren<T>(...)`**:
    *   Funzionano in modo simile ma per il contenuto proiettato (elementi passati tramite `<ng-content>`).

    ```typescript
    // parent-query.component.ts
    import { Component, viewChild, viewChildren, ElementRef, AfterViewInit, Signal, effect } from '@angular/core';
    import { ChildComponent } from './child.component'; // Assumiamo che esista ChildComponent

    @Component({
      selector: 'app-parent-query',
      standalone: true,
      imports: [ChildComponent], // Assumiamo che ChildComponent sia importabile
      template: `
        <app-child #first></app-child>
        <app-child></app-child>
        <div #myDiv>Un div</div>
      `
    })
    export class ParentQueryComponent {
      // Query per un singolo componente figlio
      firstChild: Signal<ChildComponent | undefined> = viewChild('first', { read: ChildComponent });
      // Alternativa: viewChild(ChildComponent) se c'è un solo ChildComponent senza riferimento locale

      // Query per un ElementRef
      myDivElement: Signal<ElementRef<HTMLDivElement> | undefined> = viewChild('myDiv');

      // Query per tutti i ChildComponent
      allChildren: Signal<readonly ChildComponent[]> = viewChildren(ChildComponent);

      constructor() {
        effect(() => {
          if (this.firstChild()) {
            console.log('Primo figlio trovato (signal query):', this.firstChild());
            // this.firstChild()?.someMethodInChild(); // Puoi chiamare metodi
          } else {
            console.log('Primo figlio non ancora disponibile o rimosso.');
          }

          console.log('Tutti i figli (signal query):', this.allChildren());
          this.allChildren().forEach(child => { /* ... */ });

          if (this.myDivElement()) {
            console.log('Div trovato:', this.myDivElement()?.nativeElement.textContent);
          }
        });
      }
    }
    ```
    Queste query basate su signal non richiedono `static: true` o `static: false` e si aggiornano reattivamente. Non hai bisogno di `AfterViewInit` per accedervi la prima volta (anche se l'effetto che li usa potrebbe essere eseguito dopo `AfterViewInit` a seconda di quando il DOM è pronto).

---

L'integrazione dei Signals con il resto del framework Angular è un passo importante per rendere lo sviluppo più reattivo, performante e intuitivo. La possibilità di usare input come signal, il two-way binding con `model()`, l'interoperabilità con RxJS e le query di vista basate su signal semplificano molti pattern comuni.

Il prossimo modulo, "Test e Debugging", esplorerà come testare le applicazioni Angular, inclusi i componenti che usano i Signals.
