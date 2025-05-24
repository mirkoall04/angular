Assolutamente! Procediamo con la **Lezione 9: Pipe**. I pipe sono un altro strumento molto utile per la trasformazione dei dati direttamente nei template Angular.

---

### Modulo 3: Direttive e Pipe

**Lezione 9: Pipe**

I **Pipe** in Angular sono funzioni semplici che puoi usare nei tuoi template HTML per trasformare dati. Prendono un input (come una stringa, un numero, un array, una data), lo trasformano secondo una logica definita, e restituiscono l'output trasformato per la visualizzazione.

Sono chiamati "pipe" perché si usano con la sintassi del "pipe operator" (`|`), simile ai pipe nelle shell Unix.

**1. Cosa sono i Pipe e come trasformano i dati nei template**

L'idea principale dietro i pipe è di separare la logica di formattazione dalla logica del componente. Invece di formattare i dati direttamente nel file TypeScript del componente, puoi delegare questa operazione a un pipe nel template. Questo rende i componenti più puliti e la logica di formattazione riutilizzabile.

*   **Sintassi:** `{{ espressioneInput | nomePipe : argomento1 : argomento2 ... }}`

Esempio:
```html
<p>Data di nascita: {{ dataDiNascita | date:'dd/MM/yyyy' }}</p>
<p>Titolo del libro: {{ titoloLibro | uppercase }}</p>
<p>Prezzo: {{ prezzo | currency:'EUR':'symbol':'1.2-2' }}</p>
```

**Vantaggi dell'uso dei Pipe:**

*   **Leggibilità del Template:** Mantengono i template puliti e focalizzati sulla struttura.
*   **Riusabilità:** La stessa logica di formattazione può essere applicata a dati diversi in vari punti dell'applicazione.
*   **Localizzazione:** I pipe come `DatePipe`, `CurrencyPipe`, `DecimalPipe`, `PercentPipe` supportano la localizzazione (i18n), formattando i dati secondo le convenzioni di una specifica lingua/regione.
*   **Performance:** Molti pipe integrati sono "puri" (pure pipes), il che significa che Angular li riesegue solo se i loro input cambiano.

**2. Pipe Integrati (Built-in Pipes)**

Angular fornisce una serie di pipe integrati molto utili, pronti all'uso. Questi pipe sono generalmente parte di `CommonModule`. Se stai usando componenti standalone (default da Angular 17), `CommonModule` (o i pipe specifici se sono esportati come standalone) deve essere importato nell'array `imports` del tuo componente.

Ecco alcuni dei pipe integrati più comuni:

*   **`UpperCasePipe`**: Trasforma una stringa in MAIUSCOLO.
    ```html
    <p>{{ 'ciao mondo' | uppercase }}</p> <!-- Output: CIAO MONDO -->
    ```

*   **`LowerCasePipe`**: Trasforma una stringa in minuscolo.
    ```html
    <p>{{ 'CIAO MONDO' | lowercase }}</p> <!-- Output: ciao mondo -->
    ```

*   **`TitleCasePipe`**: Trasforma una stringa in "Title Case" (ogni parola inizia con una maiuscola).
    ```html
    <p>{{ 'titolo del mio articolo' | titlecase }}</p> <!-- Output: Titolo Del Mio Articolo -->
    ```

*   **`DatePipe`**: Formatta un valore data secondo un formato specificato e opzionalmente un fuso orario e una locale.
    *   **Input:** Può essere un oggetto `Date`, un numero (millisecondi da epoch), o una stringa data ISO.
    *   **Argomenti:**
        1.  `format` (stringa, opzionale): Definisce il formato dell'output (es. `'short'`, `'medium'`, `'longDate'`, `'dd/MM/yyyy HH:mm'`). Se omesso, usa un formato di default ('mediumDate').
        2.  `timezone` (stringa, opzionale): Specifica il fuso orario.
        3.  `locale` (stringa, opzionale): Specifica la locale da usare per la formattazione.
    ```html
    <p>Data: {{ dataAttuale | date }}</p> <!-- Output: May 23, 2025 (o formato simile di default) -->
    <p>Data corta: {{ dataAttuale | date:'shortDate' }}</p> <!-- Output: 5/23/25 (o formato locale) -->
    <p>Data e ora personalizzata: {{ dataAttuale | date:'dd/MM/yyyy HH:mm:ss' }}</p> <!-- Output: 23/05/2025 13:41:55 (esempio) -->
    ```
    ```typescript
    // nel componente.ts
    // dataAttuale = new Date();
    ```

*   **`CurrencyPipe`**: Formatta un numero come valore di valuta.
    *   **Argomenti:**
        1.  `currencyCode` (stringa, opzionale): Il codice ISO della valuta (es. `'USD'`, `'EUR'`, `'JPY'`). Default è 'USD'.
        2.  `display` (stringa o booleano, opzionale): Come visualizzare la valuta:
            *   `'code'`: mostra il codice (es. EUR).
            *   `'symbol'` (default): mostra il simbolo (es. €).
            *   `'symbol-narrow'`: a volte un simbolo più stretto.
            *   `stringa personalizzata`: usa la stringa fornita.
            *   `boolean`: `true` per il simbolo, `false` per il codice.
        3.  `digitsInfo` (stringa, opzionale): Definisce il numero di cifre intere e decimali (es. `'1.2-2'` significa min 1 cifra intera, min 2 cifre decimali, max 2 cifre decimali).
        4.  `locale` (stringa, opzionale).
    ```html
    <p>Prezzo: {{ 1234.56 | currency }}</p> <!-- Output: $1,234.56 (default USD) -->
    <p>Prezzo in EUR: {{ 1234.56 | currency:'EUR' }}</p> <!-- Output: €1,234.56 -->
    <p>Prezzo in JPY (codice): {{ 12345 | currency:'JPY':'code' }}</p> <!-- Output: JPY12,345 -->
    <p>Prezzo con formattazione cifre: {{ 1234.5 | currency:'USD':'symbol':'1.2-4' }}</p> <!-- Output: $1,234.5000 -->
    ```

*   **`DecimalPipe` (o `NumberPipe`)**: Formatta un numero con un certo numero di cifre decimali e separatori di migliaia, secondo le regole della locale.
    *   **Argomenti:**
        1.  `digitsInfo` (stringa, opzionale): Simile a `CurrencyPipe` (es. `'1.2-2'`).
        2.  `locale` (stringa, opzionale).
    ```html
    <p>Numero: {{ 3.14159265 | number:'1.2-4' }}</p> <!-- Output: 3.1416 -->
    <p>Numero intero: {{ 12345.67 | number:'3.0-0' }}</p> <!-- Output: 12,346 -->
    ```

*   **`PercentPipe`**: Formatta un numero come percentuale.
    *   **Argomenti:**
        1.  `digitsInfo` (stringa, opzionale).
        2.  `locale` (stringa, opzionale).
    ```html
    <p>Percentuale: {{ 0.75 | percent }}</p> <!-- Output: 75% -->
    <p>Percentuale con decimali: {{ 0.1234 | percent:'1.1-2' }}</p> <!-- Output: 12.34% -->
    ```

*   **`JsonPipe`**: Converte un oggetto o array JavaScript in una stringa JSON. Utile per il debugging.
    ```html
    <pre>{{ { nome: 'Mario', eta: 30, attivo: true } | json }}</pre>
    <!-- Output:
    {
      "nome": "Mario",
      "eta": 30,
      "attivo": true
    }
    -->
    ```

*   **`SlicePipe`**: Crea un nuovo array o stringa contenente un sottoinsieme (una "fetta") degli elementi originali.
    *   **Argomenti:**
        1.  `start` (number): Indice di inizio (incluso).
        2.  `end` (number, opzionale): Indice di fine (escluso).
    ```html
    <p>{{ [1, 2, 3, 4, 5, 6] | slice:1:4 }}</p> <!-- Output: [2, 3, 4] -->
    <p>{{ 'Hello World' | slice:0:5 }}</p>      <!-- Output: Hello -->
    ```

*   **`KeyValuePipe`**: Trasforma un oggetto o una `Map` in un array di oggetti `{key, value}`. Utile per iterare su oggetti con `*ngFor`.
    ```html
    <div *ngFor="let item of {a: 1, b: 'due', c: true} | keyvalue">
      {{ item.key }}: {{ item.value }}
    </div>
    <!-- Output:
    a: 1
    b: due
    c: true
    -->
    ```

*   **`AsyncPipe`**: Si sottoscrive a un `Observable` or `Promise` e restituisce l'ultimo valore emesso. Gestisce automaticamente la sottoscrizione e l'unsubscribe quando il componente viene distrutto, aiutando a prevenire memory leak. È estremamente utile quando si lavora con dati asincroni (es. da `HttpClient`).
    ```typescript
    // my-async-component.component.ts
    import { Component } from '@angular/core';
    import { CommonModule } from '@angular/common';
    import { Observable, interval } from 'rxjs';
    import { map, take } from 'rxjs/operators';

    @Component({
      selector: 'app-my-async',
      standalone: true,
      imports: [CommonModule], // AsyncPipe è in CommonModule
      template: `
        <h2>AsyncPipe con Observable</h2>
        <p>Conteggio (Observable): {{ count$ | async }}</p>

        <h2>AsyncPipe con Promise</h2>
        <p>Messaggio (Promise): {{ messagePromise | async }}</p>
      `
    })
    export class MyAsyncComponent {
      count$: Observable<number>;
      messagePromise: Promise<string>;

      constructor() {
        this.count$ = interval(1000).pipe(
          take(10), // Prende solo i primi 10 valori
          map(i => i + 1)
        );

        this.messagePromise = new Promise((resolve) => {
          setTimeout(() => {
            resolve('Messaggio dalla Promise risolto!');
          }, 3000);
        });
      }
    }
    ```

**3. Creare Pipe Personalizzati**

Puoi creare i tuoi pipe personalizzati per incapsulare logiche di trasformazione specifiche del tuo progetto.

**Passaggi per creare un Pipe personalizzato:**

1.  **Creare la classe del Pipe** e decorarla con `@Pipe`.
2.  **Specificare il `name` del pipe** nel decoratore `@Pipe`. Questo è il nome che userai nel template (es. `name: 'mioPipePersonale'`).
3.  **Implementare l'interfaccia `PipeTransform`**. Questa interfaccia richiede di definire un metodo `transform()`.
4.  **Definire il metodo `transform(value: any, ...args: any[]): any`**:
    *   `value`: L'input che il pipe riceve (il valore a sinistra del `|`).
    *   `...args`: Un array opzionale di argomenti passati al pipe nel template (i valori a destra dei due punti `:`).
    *   Il metodo deve restituire il valore trasformato.
5.  **Specificare se il pipe è `pure`** (default `true`) o `impure` (`pure: false`) nel decoratore `@Pipe`.
    *   **Pure Pipes:** Angular esegue un pipe puro solo quando rileva un cambiamento "puro" al valore di input (un nuovo primitivo, un nuovo riferimento a un oggetto/array) o un cambiamento negli argomenti del pipe. I pipe puri sono efficienti.
    *   **Impure Pipes:** Angular esegue un pipe impuro durante ogni ciclo di change detection, indipendentemente dal fatto che il valore di input o gli argomenti siano cambiati. I pipe impuri possono avere un impatto significativo sulle performance e dovrebbero essere usati con cautela e solo quando necessario (es. se il pipe dipende da uno stato interno o da qualcosa al di fuori dei suoi input diretti). `AsyncPipe` è un esempio di pipe impuro.
6.  **Registrare il Pipe:**
    *   **Per pipe standalone:** Il pipe deve avere `standalone: true` nel suo decoratore `@Pipe` e poi essere importato nell'array `imports` del componente standalone che lo utilizza.
    *   **Per pipe basati su NgModule:** Il pipe deve essere dichiarato nell'array `declarations` di un `NgModule` e poi quel `NgModule` deve essere importato dove necessario.

**Esempio: Creare un Pipe `truncate`**

Questo pipe troncherà una stringa a una lunghezza massima specificata e aggiungerà dei puntini di sospensione.

*   **Generare il Pipe con Angular CLI:**
    ```bash
    ng generate pipe truncate
    # o ng g p truncate
    ```
    Questo creerà `truncate.pipe.ts` e `truncate.pipe.spec.ts`. Il pipe generato sarà standalone per default.

*   **`truncate.pipe.ts`:**
    ```typescript
    import { Pipe, PipeTransform } from '@angular/core';

    @Pipe({
      name: 'truncate',
      standalone: true // Pipe standalone
    })
    export class TruncatePipe implements PipeTransform {

      transform(value: string, maxLength: number = 50, ellipsis: string = '...'): string {
        if (!value) {
          return '';
        }

        if (value.length <= maxLength) {
          return value;
        }

        return value.substring(0, maxLength) + ellipsis;
      }
    }
    ```

*   **Usare il Pipe in un componente:**
    ```typescript
    // some-text-component.component.ts
    import { Component } from '@angular/core';
    import { TruncatePipe } from '../truncate.pipe'; // Importa il pipe standalone

    @Component({
      selector: 'app-some-text',
      standalone: true,
      imports: [TruncatePipe], // Aggiungi il pipe agli imports
      template: `
        <h2>Pipe Personalizzato: truncate</h2>
        <p>{{ longText | truncate }}</p>
        <p>{{ longText | truncate:20 }}</p>
        <p>{{ shortText | truncate:30:'---' }}</p>
        <p>{{ anotherShortText | truncate }}</p>
      `
    })
    export class SomeTextComponent {
      longText: string = 'Questo è un testo molto lungo che ha bisogno di essere troncato per non occupare troppo spazio.';
      shortText: string = 'Testo corto.';
      anotherShortText: string = null; // Test per input null
    }
    ```

**4. Pipe con Parametri e Chaining di Pipe**

*   **Pipe con Parametri:**
    Come visto negli esempi (`DatePipe`, `CurrencyPipe`, `TruncatePipe`), puoi passare parametri a un pipe usando i due punti (`:`) dopo il nome del pipe, separando i parametri successivi con altri due punti.
    `{{ valore | nomePipe : param1 : param2 }}`

*   **Chaining di Pipe (Concatenazione):**
    Puoi applicare più pipe in sequenza allo stesso valore di input. L'output di un pipe diventa l'input del pipe successivo.
    ```html
    <p>Data e ora in maiuscolo: {{ dataAttuale | date:'fullDate' | uppercase }}</p>
    <!-- Prima formatta la data, poi converte la stringa risultante in maiuscolo -->

    <p>{{ '   testo con spazi   ' | slice:3:-3 | titlecase }}</p>
    <!-- Prima rimuove gli spazi iniziali e finali (slice), poi applica TitleCase -->
    <!-- NOTA: non c'è un TrimPipe integrato, slice qui è un esempio rozzo.
         Un pipe 'trim' personalizzato sarebbe più appropriato. -->
    ```

**Considerazioni sui Pipe Puri e Impuri:**

*   La maggior parte dei pipe che creerai saranno **puri**. Questo è buono per le performance. Un pipe puro deve sempre restituire lo stesso output per lo stesso input e non deve avere side-effect.
*   Usa un **pipe impuro** (`pure: false`) solo se il pipe deve aggiornarsi in base a cambiamenti che Angular non rileverebbe normalmente per un pipe puro (es. cambiamenti interni al pipe, o uno stato globale a cui il pipe accede). `AsyncPipe` è impuro perché deve ascoltare attivamente le emissioni da un Observable/Promise.

---

I pipe sono uno strumento elegante ed efficiente per gestire la formattazione e la trasformazione dei dati nei tuoi template Angular. Insieme alle direttive, ti offrono una grande flessibilità per creare interfacce utente dinamiche e ben strutturate.

Questo conclude il Modulo 3. Nel prossimo modulo, "Servizi e Dependency Injection (DI)", esploreremo come organizzare la logica di business e condividere dati tra i componenti.

Hai domande sui Pipe?