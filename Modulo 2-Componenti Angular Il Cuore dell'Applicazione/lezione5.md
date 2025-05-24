Il **Data Binding** è uno dei concetti più potenti e fondamentali in Angular. È il meccanismo che crea una **connessione viva** tra la **logica del tuo componente** (il file TypeScript, dove hai i tuoi dati e le tue funzioni) e la **vista del tuo componente** (il file HTML, ciò che l'utente vede). Grazie al data binding, quando i tuoi dati cambiano nel componente, la vista si aggiorna automaticamente, e viceversa, quando l'utente interagisce con la vista (es. scrive in un campo di testo), i dati nel componente possono essere aggiornati.

Immagina di avere due stanze: la "Stanza dei Dati" (il tuo componente TypeScript) e la "Stanza della Visualizzazione" (il tuo template HTML). Il data binding costruisce dei "ponti" o "canali di comunicazione" tra queste due stanze.

Angular offre quattro tipi principali di data binding, che si distinguono per la **direzione del flusso dei dati**:

1.  **Interpolation `{{ }}`** (Flusso: Dalla Stanza dei Dati **➔** alla Stanza della Visualizzazione)
2.  **Property Binding `[property]`** (Flusso: Dalla Stanza dei Dati **➔** alla Stanza della Visualizzazione)
3.  **Event Binding `(event)`** (Flusso: Dalla Stanza della Visualizzazione **➔** alla Stanza dei Dati)
4.  **Two-Way Data Binding `[(ngModel)]`** (Flusso: Bidirezionale, Dati **↔** Visualizzazione)

Analizziamoli uno per uno con più dettagli:

---

**1. Interpolation: `{{ espressione }}`**

*   **Scopo:** È il modo più semplice per visualizzare un valore (che di solito è una stringa o un numero) dal tuo componente nel template HTML.
*   **Come Funziona:** Prendi una proprietà (una variabile) dal tuo componente TypeScript o il risultato di una semplice funzione, e la "inserisci" direttamente nel testo del tuo HTML. Angular sostituirà `{{ espressione }}` con il valore reale di quell'espressione, convertito in stringa.
*   **Direzione del Flusso:** **Unidirezionale**, dai dati del componente verso il template. Se il valore della proprietà nel componente cambia, l'HTML si aggiorna automaticamente. Ma non puoi usare l'interpolazione per cambiare il valore nel componente dall'HTML.

    **Esempio Chiarito:**

    ```typescript
    // Il tuo componente TypeScript (my-component.component.ts)
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-my-component',
      standalone: true,
      imports: [],
      template: `
        <h1>Ciao, {{ userName }}!</h1>
        <p>Hai {{ age }} anni.</p>
        <p>Il tuo messaggio del giorno: {{ getWelcomeMessage() }}</p>
        <p>Domani avrai {{ age + 1 }} anni.</p>
      `
    })
    export class MyComponent {
      userName: string = 'Alice';
      age: number = 30;

      getWelcomeMessage(): string {
        return 'Speriamo tu abbia una buona giornata!';
      }
    }
    ```

    **Cosa succede qui:**
    *   `{{ userName }}` verrà sostituito con "Alice".
    *   `{{ age }}` verrà sostituito con "30".
    *   `{{ getWelcomeMessage() }}` chiamerà la funzione `getWelcomeMessage()` e il suo risultato ("Speriamo tu abbia una buona giornata!") verrà visualizzato.
    *   `{{ age + 1 }}` eseguirà il calcolo (30 + 1) e visualizzerà "31".

    **Cosa puoi interpolare:**
    *   Proprietà del componente (es. `userName`).
    *   Risultati di metodi del componente (es. `getWelcomeMessage()`). **Attenzione:** Questi metodi dovrebbero essere semplici e veloci, perché potrebbero essere chiamati spesso da Angular. Non dovrebbero modificare lo stato (avere "side effects").
    *   Semplici espressioni JavaScript (es. `age + 1`, `10 * 5`, `condizione ? 'vero' : 'falso'`).

    **Cosa NON puoi fare (o dovresti evitare) con l'interpolazione:**
    *   Assegnare valori a variabili (es. `{{ x = 5 }}` non funziona).
    *   Usare operatori come `new`, `typeof`, `instanceof` direttamente nell'interpolazione.
    *   Accedere a variabili globali come `window` o `console`.

---

**2. Property Binding: `[proprietaHTML]="proprietaComponente"`**

*   **Scopo:** Per impostare il valore di una **proprietà** di un elemento HTML (come `src` di un'immagine, `disabled` di un bottone, `value` di un input) o di una direttiva/componente Angular.
*   **Come Funziona:** Metti il nome della proprietà dell'elemento HTML tra parentesi quadre `[]`. Il valore a destra del segno `=` è una proprietà o un'espressione del tuo componente TypeScript. Angular valuterà questa espressione e assegnerà il risultato alla proprietà dell'elemento HTML.
*   **Direzione del Flusso:** **Unidirezionale**, dai dati del componente verso la proprietà dell'elemento nel template.

    **Esempio Chiarito:**

    ```typescript
    // Il tuo componente TypeScript (my-component.component.ts)
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-my-component',
      standalone: true,
      imports: [],
      template: `
        <h2>Logo Angular:</h2>
        <img [src]="angularLogoUrl" [alt]="imageAltText" width="150">

        <br><br>

        <button [disabled]="isButtonDisabled">
          {{ isButtonDisabled ? 'Bottone Disabilitato' : 'Cliccami!' }}
        </button>

        <br><br>
        Valore dell'input (sola lettura):
        <input type="text" [value]="initialInputValue">
      `
    })
    export class MyComponent {
      angularLogoUrl: string = 'https://angular.io/assets/images/logos/angular/angular.svg';
      imageAltText: string = 'Il logo di Angular';
      isButtonDisabled: boolean = true; // Prova a cambiarlo in false!
      initialInputValue: string = 'Testo iniziale';
    }
    ```

    **Cosa succede qui:**
    *   `[src]="angularLogoUrl"`: La proprietà `src` dell'elemento `<img>` viene impostata al valore della variabile `angularLogoUrl` del componente.
    *   `[alt]="imageAltText"`: Similmente per l'attributo `alt`.
    *   `[disabled]="isButtonDisabled"`: La proprietà `disabled` del bottone viene impostata al valore booleano di `isButtonDisabled`. Se `true`, il bottone è disabilitato; se `false`, è abilitato.
    *   `[value]="initialInputValue"`: La proprietà `value` dell'input viene impostata, ma questo è solo per visualizzazione; l'utente può modificarlo, ma il cambiamento non tornerà automaticamente a `initialInputValue` nel componente (per quello serve il two-way binding).

    **Interpolazione vs. Property Binding per attributi stringa:**
    Per gli attributi che si aspettano una stringa, come `src` o `alt`, potresti pensare di usare anche l'interpolazione:
    `<img src="{{ angularLogoUrl }}" alt="{{ imageAltText }}">`
    Questo funziona, ma il **property binding (`[src]`) è generalmente preferito e più potente** perché:
    1.  **Valuta l'espressione:** L'espressione a destra di `[src]=` viene valutata come codice TypeScript. Se `angularLogoUrl` fosse `null` o `undefined`, `[src]` non imposterebbe l'attributo, mentre `src="{{ angularLogoUrl }}"` lo imposterebbe alla stringa "null" o "undefined", il che potrebbe causare errori.
    2.  **Funziona con tipi non stringa:** Il property binding è essenziale quando il valore che vuoi passare non è una stringa, come un booleano per `[disabled]` o un oggetto per input di componenti più complessi.

---

**3. Event Binding: `(eventoHTML)="metodoComponente()"`**

*   **Scopo:** Per ascoltare gli eventi che accadono sugli elementi HTML (come un click del mouse, un tasto premuto, il mouse che passa sopra) e rispondere a questi eventi eseguendo una funzione (metodo) nel tuo componente TypeScript.
*   **Come Funziona:** Metti il nome dell'evento HTML tra parentesi tonde `()`. Il valore a destra del segno `=` è solitamente una chiamata a un metodo del tuo componente.
*   **Direzione del Flusso:** **Unidirezionale**, dalla vista (template) verso il componente. L'utente fa qualcosa nella vista, e questo scatena un'azione nel componente.

    **Esempio Chiarito:**

    ```typescript
    // Il tuo componente TypeScript (my-component.component.ts)
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-my-component',
      standalone: true,
      imports: [],
      template: `
        <p>{{ message }}</p>
        <button (click)="onButtonClick()">Cambia Messaggio</button>

        <br><br>

        <input type="text" placeholder="Scrivi qualcosa e premi Invio" (keyup.enter)="onEnterPressed($event)">
        <p>Hai scritto (dopo Invio): {{ inputText }}</p>
      `
    })
    export class MyComponent {
      message: string = 'Messaggio Iniziale';
      inputText: string = '';

      onButtonClick(): void {
        this.message = 'Messaggio Cambiato dopo il Click!';
        console.log('Il bottone è stato cliccato!');
      }

      onEnterPressed(event: Event): void {
        // 'event' è un oggetto speciale che contiene informazioni sull'evento
        const inputElement = event.target as HTMLInputElement; // Diciamo a TypeScript che target è un input HTML
        this.inputText = inputElement.value;
        console.log('Testo inserito:', this.inputText);
        inputElement.value = ''; // Pulisce l'input dopo Invio (opzionale)
      }
    }
    ```

    **Cosa succede qui:**
    *   `(click)="onButtonClick()"`: Quando il bottone viene cliccato, il metodo `onButtonClick()` nel componente viene eseguito. Questo metodo cambia il valore di `this.message`. Poiché `message` è usato con l'interpolazione, la vista si aggiorna automaticamente!
    *   `(keyup.enter)="onEnterPressed($event)"`: Questo ascolta l'evento "rilascio di un tasto" (`keyup`). Il `.enter` è un "pseudo-evento" di Angular che filtra solo per il rilascio del tasto Invio.
        *   `$event`: È una variabile speciale che Angular fornisce nel template quando gestisci un evento. Contiene l'oggetto evento standard del DOM. Lo passiamo al nostro metodo `onEnterPressed`.
        *   Nel metodo `onEnterPressed`, accediamo a `event.target` (che è l'elemento `input` che ha scatenato l'evento) e poi al suo valore (`.value`) per ottenere ciò che l'utente ha scritto.

---

**4. Two-Way Data Binding: `[(ngModel)]="proprietaComponente"`**

*   **Scopo:** Questo è un tipo speciale di binding che combina il Property Binding e l'Event Binding per creare una **sincronizzazione a due vie** tra un elemento di input del form (come `<input>`, `<select>`, `<textarea>`) e una proprietà del componente.
*   **Come Funziona:**
    *   La sintassi `[( )]` è spesso chiamata "banana in a box" (parentesi quadre `[]` per il property binding e parentesi tonde `()` per l'event binding, che circondano `ngModel`).
    *   Quando il valore della proprietà nel componente cambia, il valore dell'input nel template si aggiorna (property binding).
    *   Quando l'utente modifica il valore nell'input nel template, la proprietà nel componente si aggiorna automaticamente (event binding).
*   **Requisito Fondamentale:** Per usare `[(ngModel)]`, devi **importare `FormsModule`** nel tuo componente standalone (nell'array `imports` di `@Component`) o nel `NgModule` che dichiara il tuo componente.

    **Esempio Chiarito:**

    ```typescript
    // Il tuo componente TypeScript (my-component.component.ts)
    import { Component } from '@angular/core';
    import { FormsModule } from '@angular/forms'; // <-- IMPORTANTE!

    @Component({
      selector: 'app-my-component',
      standalone: true,
      imports: [FormsModule], // <-- AGGIUNGI FormsModule qui!
      template: `
        <h2>Two-Way Data Binding con [(ngModel)]</h2>

        <label for="nameInput">Il tuo Nome:</label>
        <input type="text" id="nameInput" [(ngModel)]="currentName">

        <p>Ciao, {{ currentName }}!</p>

        <hr>

        <label for="feedback">Il tuo Feedback:</label>
        <textarea id="feedback" [(ngModel)]="feedbackText" rows="3"></textarea>
        <p>Feedback inserito: {{ feedbackText }}</p>
      `
    })
    export class MyComponent {
      currentName: string = 'Ospite';
      feedbackText: string = '';
    }
    ```

    **Cosa succede qui:**
    *   `[(ngModel)]="currentName"` sull'input di testo:
        *   Inizialmente, l'input mostrerà "Ospite" (valore di `currentName`).
        *   Se cambi `this.currentName = 'Nuovo Nome';` nel codice TypeScript (magari in un'altra funzione), l'input nel browser si aggiornerà a "Nuovo Nome".
        *   Se scrivi "Mario" nell'input nel browser, la variabile `this.currentName` nel componente diventerà automaticamente "Mario", e l'interpolazione `{{ currentName }}` si aggiornerà di conseguenza.
    *   Lo stesso vale per `[(ngModel)]="feedbackText"` sulla `<textarea>`.

    **Come funziona `[(ngModel)]` dietro le quinte?**
    `[(ngModel)]` è una scorciatoia sintattica per una combinazione di property binding e event binding su una direttiva chiamata `NgModel`:
    *   Property Binding: `[ngModel]="proprietaComponente"` (imposta il valore dell'input dalla proprietà del componente).
    *   Event Binding: `(ngModelChange)="proprietaComponente = $event"` (aggiorna la proprietà del componente quando il valore dell'input cambia; `$event` qui è il nuovo valore dell'input).

---

**Riepilogo Semplificato dei Flussi:**

| Sintassi          | Cosa Fa                                 | Direzione Flusso Dati                    | Esempio d'Uso                                     |
| :---------------- | :-------------------------------------- | :--------------------------------------- | :------------------------------------------------ |
| `{{ dato }}`      | Mostra un dato                          | Componente **➔** Template (Vista)        | Visualizzare un nome, un numero, un titolo        |
| `[prop]="dato"`   | Imposta una proprietà di un elemento HTML | Componente **➔** Template (Vista)        | Impostare `src` di un'immagine, disabilitare un bottone |
| `(evento)="azione()"` | Reagisce a un'azione dell'utente      | Template (Vista) **➔** Componente        | Rispondere a un click, all'input da tastiera    |
| `[(ngModel)]="dato"` | Sincronizza un input del form         | Componente **↔** Template (Vista)        | Campi di testo, checkbox, select in un form       |

