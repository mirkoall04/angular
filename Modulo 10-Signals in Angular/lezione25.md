Assolutamente! Entriamo nel **Modulo 10: Signals in Angular**. I Signals sono una delle aggiunte più significative e trasformative ad Angular degli ultimi anni, introducendo un nuovo paradigma per la gestione della reattività e dello stato all'interno del framework.

---

### Modulo 10: Signals in Angular

I Signals sono stati introdotti in Angular v16 come developer preview e sono diventati stabili nelle versioni successive (v17+). Rappresentano un nuovo sistema di reattività che mira a migliorare le performance della change detection e a semplificare il modo in cui gli sviluppatori ragionano sui cambiamenti di stato e sulla loro propagazione.

**Lezione 25: Introduzione ai Signals**

**1. Cosa sono i Signals e perché sono stati introdotti (reattività granulare, performance)**

*   **Cosa è un Signal?**
    Un **Signal** è un wrapper attorno a un valore che può notificare le parti interessate (i "consumer") quando quel valore cambia. È una fonte di stato che traccia esplicitamente dove viene utilizzato e può notificare in modo efficiente solo quei consumer quando il suo valore viene aggiornato.

    Pensa a un signal come a una cella di un foglio di calcolo:
    *   Contiene un valore.
    *   Altre celle (o formule) possono dipendere da questa cella.
    *   Quando il valore della cella originale cambia, solo le celle dipendenti vengono ricalcolate automaticamente.

*   **Perché sono stati introdotti i Signals?**
    L'introduzione dei Signals in Angular è guidata da diversi obiettivi chiave:

    1.  **Reattività Granulare e Fine-Grained Change Detection:**
        *   Il sistema di change detection tradizionale di Angular (basato su Zone.js) a volte può controllare più parti dell'applicazione del necessario per rilevare i cambiamenti. Anche se efficiente, per applicazioni molto complesse, può portare a controlli non ottimali.
        *   I Signals permettono ad Angular di sapere esattamente quali parti della vista dipendono da un specifico pezzo di stato (un signal). Quando un signal cambia, Angular può aggiornare **solo** quelle specifiche parti del DOM, invece di dover controllare un intero componente o un sottoalbero di componenti. Questo porta a una change detection più precisa e potenzialmente molto più performante.

    2.  **Miglioramento delle Performance:**
        *   Riducendo la quantità di lavoro che la change detection deve fare, i Signals possono portare a miglioramenti significativi nelle performance, specialmente in applicazioni con molti binding e aggiornamenti di stato frequenti.
        *   Eliminano la necessità di Zone.js in futuro (o la rendono opzionale), il che può ridurre la dimensione del bundle e semplificare l'integrazione con altre librerie.

    3.  **Semplificazione della Developer Experience (DX):**
        *   I Signals offrono un modello mentale più semplice per la gestione dello stato e della reattività rispetto a pattern RxJS complessi per alcuni casi d'uso.
        *   La propagazione dei cambiamenti è più esplicita e facile da tracciare.
        *   Riducono la necessità di usare alcuni operatori RxJS (come `async` pipe in molti casi, o `ChangeDetectorRef.markForCheck()`) per triggerare manualmente la change detection.

    4.  **Migliore Interoperabilità con RxJS:**
        *   Anche se i Signals offrono un nuovo modello di reattività, sono progettati per coesistere e interoperare bene con RxJS. Puoi facilmente convertire tra Signals e Observables.

**2. Creare e Aggiornare i `signal` (`writableSignal`)**

Il tipo più basilare di signal è un `writableSignal` (segnale scrivibile). Puoi leggerne il valore e puoi anche aggiornarlo direttamente.

*   **Creazione di un `writableSignal`:**
    Si usa la funzione `signal()` (importata da `@angular/core`).
    ```typescript
    import { Component, signal, WritableSignal } from '@angular/core'; // Importa signal e WritableSignal

    @Component({
      selector: 'app-counter',
      standalone: true,
      template: `
        <div>
          Conteggio: {{ count() }}  <!-- Leggi il valore del signal chiamandolo come una funzione -->
        </div>
        <button (click)="increment()">Incrementa</button>
        <button (click)="decrement()">Decrementa</button>
        <button (click)="reset()">Resetta</button>
        <button (click)="setCustomValue()">Imposta a 100</button>
      `
    })
    export class CounterComponent {
      // Crea un writableSignal con un valore iniziale di 0
      count: WritableSignal<number> = signal(0);

      constructor() {
        // Puoi anche accedere al valore iniziale subito dopo la creazione
        console.log('Valore iniziale del conteggio:', this.count());
      }

      increment(): void {
        // Metodo 1: .update() - per aggiornare il valore basandosi su quello precedente
        this.count.update(currentValue => currentValue + 1);
        console.log('Dopo incremento:', this.count());
      }

      decrement(): void {
        this.count.update(val => val - 1);
        console.log('Dopo decremento:', this.count());
      }

      reset(): void {
        // Metodo 2: .set() - per impostare un valore completamente nuovo
        this.count.set(0);
        console.log('Dopo reset:', this.count());
      }

      setCustomValue(): void {
        this.count.set(100);
      }
    }
    ```

*   **Leggere il Valore di un Signal:**
    Per ottenere il valore corrente di un signal, lo chiami come se fosse una funzione: `mySignal()`.
    ```typescript
    let currentValue = this.count();
    ```
    Questa sintassi è intenzionale: segnala che stai accedendo a un valore che potrebbe cambiare e che Angular sta tracciando questa lettura per scopi di reattività.

*   **Aggiornare un `writableSignal`:**
    Ci sono due metodi principali per aggiornare un `writableSignal`:
    1.  **`set(newValue)`**: Imposta il signal a un valore completamente nuovo.
        ```typescript
        this.count.set(10); // Il conteggio è ora 10
        ```
    2.  **`update(updateFn)`**: Accetta una funzione che riceve il valore corrente del signal e restituisce il nuovo valore. È utile quando il nuovo valore dipende da quello precedente.
        ```typescript
        this.count.update(currentValue => currentValue * 2); // Raddoppia il conteggio
        ```
    Esiste anche un metodo `.mutate(mutatorFn)` per i casi in cui vuoi modificare direttamente un oggetto o un array contenuto in un signal senza cambiarne il riferimento. Questo è per scenari più avanzati e dovrebbe essere usato con cautela perché modifica lo stato "in-place". Per la maggior parte dei casi, `set` e `update` (che promuovono l'immutabilità) sono preferibili.

**3. `computed` signals per Valori Derivati**

Un `computed` signal (segnale calcolato) è un signal il cui valore è derivato da altri signal(s). È **read-only** (non puoi chiamare `set` o `update` su di esso). Il suo valore viene ricalcolato automaticamente solo quando uno dei signal da cui dipende cambia. I `computed` signals sono anche memoizzati: se i signal dipendenti non cambiano, un `computed` signal restituisce il suo ultimo valore calcolato senza rieseguire la funzione di calcolo.

*   **Creazione di un `computed` signal:**
    Si usa la funzione `computed()` (importata da `@angular/core`), che accetta una funzione di derivazione.
    ```typescript
    import { Component, signal, computed, WritableSignal, Signal } from '@angular/core';

    @Component({
      selector: 'app-derived-values',
      standalone: true,
      template: `
        <div>
          Nome: <input type="text" (input)="onFirstNameChange($event)"> {{ firstName() }} <br>
          Cognome: <input type="text" (input)="onLastNameChange($event)"> {{ lastName() }} <br>
          <hr>
          Nome Completo (computed): <strong>{{ fullName() }}</strong> <br>
          Lunghezza Nome Completo (computed): {{ fullNameLength() }}
        </div>
      `
    })
    export class DerivedValuesComponent {
      firstName: WritableSignal<string> = signal('Mario');
      lastName: WritableSignal<string> = signal('Rossi');

      // fullName è un computed signal che dipende da firstName e lastName
      fullName: Signal<string> = computed(() => {
        console.log('Calcolo fullName...'); // Verrà loggato solo quando firstName o lastName cambiano
        return `${this.firstName()} ${this.lastName()}`;
      });

      // fullNameLength è un computed signal che dipende da fullName (un altro computed signal)
      fullNameLength: Signal<number> = computed(() => {
        console.log('Calcolo fullNameLength...');
        return this.fullName().length;
      });

      onFirstNameChange(event: Event): void {
        this.firstName.set((event.target as HTMLInputElement).value);
      }

      onLastNameChange(event: Event): void {
        this.lastName.set((event.target as HTMLInputElement).value);
      }
    }
    ```
    Nell'esempio sopra:
    *   `fullName()` viene ricalcolato solo se `firstName()` o `lastName()` cambiano.
    *   `fullNameLength()` viene ricalcolato solo se `fullName()` cambia (che a sua volta dipende da `firstName` e `lastName`).
    *   Se nessuna delle dipendenze cambia, chiamare `fullName()` o `fullNameLength()` più volte restituirà il valore memoizzato senza rieseguire le funzioni di calcolo.

**4. `effect` per Eseguire Side Effect in Risposta a Cambiamenti dei Signal**

Un `effect` (effetto) è un'operazione che viene eseguita ogni volta che uno dei signal(s) a cui "legge" (da cui dipende) cambia valore. Gli effetti sono utili per eseguire side effects (effetti collaterali) in risposta a cambiamenti di stato, come:

*   Logging.
*   Interazioni con API del browser (es. `localStorage`).
*   Aggiornamenti manuali del DOM (anche se questo dovrebbe essere raro con Angular).
*   Chiamate a servizi per salvare dati.

**Importante:** Gli effetti sono pensati per i side effects e **non dovrebbero modificare altri signal al loro interno** (per evitare loop infiniti o comportamenti imprevedibili). Se hai bisogno di derivare un nuovo stato da altri signal, usa `computed`.

*   **Creazione di un `effect`:**
    Si usa la funzione `effect()` (importata da `@angular/core`). Un `effect` viene eseguito almeno una volta all'inizio (quando viene creato) e poi ogni volta che i signal letti al suo interno cambiano.
    Gli `effect` registrano automaticamente le loro dipendenze (i signal che leggono).
    `effect()` deve essere chiamato all'interno di un **contesto di iniezione** (come il `constructor` di un componente/servizio, o usando `runInInjectionContext`).

    ```typescript
    import { Component, signal, effect, Injector, runInInjectionContext } from '@angular/core';

    @Component({
      selector: 'app-logger-effect',
      standalone: true,
      template: `
        <p>Valore: {{ myValue() }}</p>
        <button (click)="changeValue()">Cambia Valore</button>
      `
    })
    export class LoggerEffectComponent {
      myValue: WritableSignal<number> = signal(0);

      constructor(private injector: Injector) { // Inietta Injector
        // Crea un effetto nel costruttore (che è un contesto di iniezione)
        effect(() => {
          // Questo effetto verrà eseguito ogni volta che myValue() cambia
          console.log(`Effect: Il valore di myValue è cambiato in: ${this.myValue()}`);
          // Esempio di side effect: salvare in localStorage
          localStorage.setItem('myValue', JSON.stringify(this.myValue()));
        });

        // Esempio di creazione di un effetto fuori dal costruttore usando runInInjectionContext
        // this.initializeLoggingEffect();
      }

      // initializeLoggingEffect(): void {
      //   runInInjectionContext(this.injector, () => {
      //     effect(() => {
      //       console.log(`Effect (runInInjectionContext): Il valore è ${this.myValue()}`);
      //     });
      //   });
      // }

      changeValue(): void {
        this.myValue.update(v => v + 10);
      }

      // Gli effetti vengono puliti automaticamente quando il contesto in cui sono stati creati
      // (es. il componente) viene distrutto. Non è necessario un unsubscribe manuale per gli effect.
    }
    ```

*   **Pulizia (Cleanup) degli `effect`:**
    Gli effetti registrano automaticamente una funzione di cleanup che viene eseguita prima della prossima esecuzione dell'effetto o quando l'effetto viene distrutto (es. quando il componente viene distrutto). Puoi definire questa funzione di cleanup passandola come valore di ritorno dalla funzione dell'effetto.
    ```typescript
    // effect(() => {
    //   const timerId = setInterval(() => {
    //     console.log(`Valore corrente: ${this.myValue()}`);
    //   }, 1000);
    //
    //   // Funzione di cleanup
    //   return () => {
    //     clearInterval(timerId);
    //     console.log('Effetto e timer puliti.');
    //   };
    // });
    ```

**Confronto Rapido:**

| Caratteristica      | `signal(initialValue)` (WritableSignal)      | `computed(() => ...)` (Signal Read-only)        | `effect(() => ...)`                                    |
| :------------------ | :------------------------------------------- | :---------------------------------------------- | :----------------------------------------------------- |
| **Scopo**           | Contenere stato scrivibile                  | Derivare stato da altri signal (read-only)    | Eseguire side effects in risposta a cambiamenti di signal |
| **Aggiornabile?**   | Sì (`set()`, `update()`, `mutate()`)         | No (si aggiorna automaticamente)              | No (non dovrebbe modificare altri signal)            |
| **Valore**          | Primitivo o Oggetto/Array                    | Calcolato, memoizzato                           | Non restituisce un valore "di stato"                  |
| **Dipendenze**      | Nessuna dipendenza intrinseca                | Dipende da altri signal letti nella funzione   | Dipende da altri signal letti nella funzione          |
| **Esecuzione**      | -                                            | Pigra (solo quando necessario), memoizzata     | Eager (almeno una volta, poi su cambiamento dipendenze) |
| **Cleanup**         | -                                            | -                                               | Automatica, con funzione di cleanup opzionale         |

---

I Signals rappresentano un cambiamento significativo e positivo per la gestione della reattività in Angular. Offrono un modo più semplice e performante per tracciare e reagire ai cambiamenti di stato. Comprendere `signal`, `computed`, ed `effect` è il primo passo per sfruttare appieno questo nuovo paradigma.

Nella prossima lezione, "Integrazione dei Signals con il Framework", vedremo come i Signals si integrano con altre parti di Angular come input dei componenti, query di vista, e l'interoperabilità con RxJS.
