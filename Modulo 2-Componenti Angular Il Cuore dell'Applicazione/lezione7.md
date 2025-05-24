Ottimo! Procediamo con la **Lezione 7: Ciclo di Vita dei Componenti (Lifecycle Hooks)**. Questa lezione è fondamentale per capire come Angular crea, aggiorna e distrugge i componenti, e come puoi intervenire in questi momenti specifici.

---

### Modulo 2: Componenti Angular: Il Cuore dell'Applicazione

**Lezione 7: Ciclo di Vita dei Componenti (Lifecycle Hooks)**

Ogni componente Angular ha un ciclo di vita gestito da Angular stesso. Angular crea il componente, lo renderizza, crea e renderizza i suoi figli, lo controlla per verificare quando le sue proprietà legate ai dati (data-bound properties) cambiano, e lo distrugge prima di rimuoverlo dal DOM.

Angular offre dei **lifecycle hooks** (ganci del ciclo di vita) che forniscono visibilità su questi momenti chiave e la possibilità di agire quando si verificano. Un lifecycle hook è un metodo di interfaccia che puoi implementare nella classe del tuo componente. Angular chiamerà questi metodi hook al momento appropriato.

**Perché usare i Lifecycle Hooks?**

*   **Inizializzazione:** Eseguire logica di inizializzazione dopo che il componente è stato creato o dopo che le sue proprietà di input sono state impostate. Ad esempio, recuperare dati da un server.
*   **Risposta ai Cambiamenti:** Agire quando le proprietà di input (`@Input()`) del componente cambiano.
*   **Interazione con il DOM:** Eseguire operazioni sul DOM dopo che il componente o la sua vista (o quella dei figli) è stata completamente renderizzata.
*   **Pulizia (Cleanup):** Rilasciare risorse prima che il componente venga distrutto per evitare memory leak. Ad esempio, fare l'unsubscribe da Observables, deregistrare event listener.

**Interfacce dei Lifecycle Hooks**

Per usare un lifecycle hook, la classe del tuo componente deve implementare l'interfaccia corrispondente (importata da `@angular/core`) e definire il metodo associato.

Ecco i principali lifecycle hooks, nell'ordine approssimativo in cui vengono chiamati:

1.  **`constructor()` (non è un hook di Angular, ma un costruttore di classe TypeScript/JavaScript)**
    *   **Chiamato:** Quando la classe viene istanziata.
    *   **Scopo:** Principalmente per la **dependency injection** e per l'inizializzazione di base dei membri della classe (proprietà).
    *   **Limitazioni:** Non dovresti mettere logica che accede alle proprietà di input (`@Input()`) qui, perché non sono ancora state inizializzate da Angular.

2.  **`ngOnChanges(changes: SimpleChanges)`**
    *   **Interfaccia:** `OnChanges`
    *   **Chiamato:**
        *   Prima di `ngOnInit()`.
        *   Ogni volta che una o più proprietà di input (`@Input()`) del componente cambiano valore. Se non ci sono input, o se non cambiano, questo hook non viene chiamato (dopo la prima volta se ci sono input).
    *   **Scopo:** Rispondere ai cambiamenti delle proprietà di input. Utile per eseguire azioni quando un input specifico cambia.
    *   **Argomento `changes`:** Un oggetto `SimpleChanges` che contiene le proprietà cambiate, con il loro valore precedente e corrente.
        ```typescript
        import { Component, Input, OnChanges, SimpleChanges } from '@angular/core';

        @Component({ /* ... */ })
        export class MyComponent implements OnChanges {
          @Input() data: string;
          @Input() counter: number;

          ngOnChanges(changes: SimpleChanges): void {
            console.log('ngOnChanges Eseguito:', changes);
            if (changes['data']) { // Controlla se 'data' è tra le proprietà cambiate
              console.log(`  Valore precedente di data: ${changes['data'].previousValue}`);
              console.log(`  Valore corrente di data: ${changes['data'].currentValue}`);
              // Esegui logica basata sul cambiamento di 'data'
            }
            if (changes['counter']) {
              console.log(`  Counter cambiato a: ${changes['counter'].currentValue}`);
            }
          }
        }
        ```

3.  **`ngOnInit()`**
    *   **Interfaccia:** `OnInit`
    *   **Chiamato:** Una sola volta, dopo il primo `ngOnChanges()` (se ci sono input) e dopo che tutte le proprietà di input sono state inizializzate.
    *   **Scopo:** È il posto ideale per la logica di inizializzazione complessa del componente, come il recupero di dati da un servizio, una volta che il componente è pronto.
        ```typescript
        import { Component, OnInit } from '@angular/core';
        // import { DataService } from './data.service'; // Esempio

        @Component({ /* ... */ })
        export class MyComponent implements OnInit {
          // constructor(private dataService: DataService) {} // Iniezione del servizio

          ngOnInit(): void {
            console.log('ngOnInit Eseguito: Componente inizializzato, input pronti.');
            // this.dataService.fetchData().subscribe(...); // Esempio di fetch dati
          }
        }
        ```

4.  **`ngDoCheck()`**
    *   **Interfaccia:** `DoCheck`
    *   **Chiamato:** Durante ogni ciclo di change detection di Angular, dopo `ngOnChanges()` e `ngOnInit()`. Viene chiamato molto frequentemente!
    *   **Scopo:** Permette di implementare una logica di change detection personalizzata, quando Angular da solo non riesce a rilevare o non rileva nel modo desiderato un cambiamento (ad esempio, cambiamenti profondi in un oggetto o array che Angular non traccia per default).
    *   **Attenzione:** Usalo con molta cautela perché viene eseguito spesso e una logica inefficiente qui può impattare gravemente le performance.

5.  **`ngAfterContentInit()`**
    *   **Interfaccia:** `AfterContentInit`
    *   **Chiamato:** Una sola volta, dopo la prima esecuzione di `ngDoCheck()` e dopo che Angular ha proiettato contenuto esterno (usando `<ng-content>`) nella vista del componente.
    *   **Scopo:** Agire sul contenuto proiettato dopo che è stato completamente inizializzato. Utile per interagire con i `@ContentChild` e `@ContentChildren`.

6.  **`ngAfterContentChecked()`**
    *   **Interfaccia:** `AfterContentChecked`
    *   **Chiamato:** Dopo `ngAfterContentInit()` e dopo ogni successiva esecuzione di `ngDoCheck()`. Chiamato ogni volta che il contenuto proiettato è stato controllato per cambiamenti.
    *   **Scopo:** Rispondere ai cambiamenti nel contenuto proiettato dopo che è stato controllato.

7.  **`ngAfterViewInit()`**
    *   **Interfaccia:** `AfterViewInit`
    *   **Chiamato:** Una sola volta, dopo la prima esecuzione di `ngAfterContentChecked()` e dopo che la vista del componente (e le viste dei suoi figli) sono state completamente inizializzate e renderizzate.
    *   **Scopo:** Eseguire logica che richiede che la vista sia completamente costruita. È il posto giusto per interagire con gli elementi della vista del componente (es. usando `@ViewChild` e `@ViewChildren` per accedere a elementi DOM o componenti figli) o per inizializzare librerie JavaScript di terze parti che manipolano il DOM.
        ```typescript
        import { Component, AfterViewInit, ViewChild, ElementRef } from '@angular/core';

        @Component({
          selector: 'app-view-example',
          standalone: true,
          template: `<input #myInput type="text" value="Ciao">`
        })
        export class ViewExampleComponent implements AfterViewInit {
          @ViewChild('myInput') myInputElementRef: ElementRef<HTMLInputElement>;

          ngAfterViewInit(): void {
            console.log('ngAfterViewInit Eseguito: Vista del componente inizializzata.');
            // Ora è sicuro accedere a myInputElementRef
            console.log(this.myInputElementRef.nativeElement.value);
            this.myInputElementRef.nativeElement.focus();
          }
        }
        ```

8.  **`ngAfterViewChecked()`**
    *   **Interfaccia:** `AfterViewChecked`
    *   **Chiamato:** Dopo `ngAfterViewInit()` e dopo ogni successiva esecuzione di `ngAfterContentChecked()`. Chiamato ogni volta che la vista del componente (e dei suoi figli) è stata controllata per cambiamenti.
    *   **Scopo:** Rispondere ai cambiamenti nella vista del componente (o dei suoi figli) dopo che è stata controllata.
    *   **Attenzione:** Simile a `ngDoCheck()`, viene chiamato frequentemente. Evita di modificare le proprietà del componente qui in modo che possa causare un altro ciclo di aggiornamento, il che potrebbe portare a un loop infinito.

9.  **`ngOnDestroy()`**
    *   **Interfaccia:** `OnDestroy`
    *   **Chiamato:** Una sola volta, immediatamente prima che Angular distrugga il componente o la direttiva.
    *   **Scopo:** È il posto cruciale per eseguire operazioni di pulizia (cleanup) per evitare memory leak.
        *   **Unsubscribe da Observables:** Se il componente si è sottoscritto a Observables (es. da servizi, eventi del router, `HttpClient`), è fondamentale fare l'unsubscribe qui per evitare che le callback continuino a essere eseguite dopo che il componente è stato distrutto.
        *   **Deregistrare event listeners:** Rimuovere event listener globali (es. su `window` o `document`) che sono stati aggiunti dal componente.
        *   **Annullare timers:** Fermare `setInterval` o `setTimeout`.
        *   Rilasciare altre risorse che il componente potrebbe aver allocato.
        ```typescript
        import { Component, OnInit, OnDestroy } from '@angular/core';
        import { Subscription, interval } from 'rxjs';

        @Component({ /* ... */ })
        export class MyCleanupComponent implements OnInit, OnDestroy {
          private timerSubscription: Subscription;

          ngOnInit(): void {
            const myInterval = interval(1000);
            this.timerSubscription = myInterval.subscribe(val => {
              console.log(`Timer tick: ${val} nel componente MyCleanupComponent`);
            });
          }

          ngOnDestroy(): void {
            console.log('ngOnDestroy Eseguito: Pulizia in corso...');
            if (this.timerSubscription) {
              this.timerSubscription.unsubscribe();
              console.log('  Timer subscription annullata.');
            }
          }
        }
        ```

**Ordine di Esecuzione (Semplificato):**

Per un componente con figli:

1.  `constructor` (Padre)
2.  `ngOnChanges` (Padre)
3.  `ngOnInit` (Padre)
4.  `ngDoCheck` (Padre)
5.  `ngAfterContentInit` (Padre)
6.  `ngAfterContentChecked` (Padre)
    1.  `constructor` (Figlio)
    2.  `ngOnChanges` (Figlio)
    3.  `ngOnInit` (Figlio)
    4.  `ngDoCheck` (Figlio)
    5.  `ngAfterContentInit` (Figlio)
    6.  `ngAfterContentChecked` (Figlio)
    7.  `ngAfterViewInit` (Figlio)
    8.  `ngAfterViewChecked` (Figlio)
7.  `ngAfterViewInit` (Padre)
8.  `ngAfterViewChecked` (Padre)

Quando un input cambia:

1.  `ngOnChanges` (Padre o Figlio, a seconda di dove è l'input)
2.  `ngDoCheck` (Padre)
3.  `ngAfterContentChecked` (Padre)
    1.  `ngDoCheck` (Figlio)
    2.  `ngAfterContentChecked` (Figlio)
    3.  `ngAfterViewChecked` (Figlio)
4.  `ngAfterViewChecked` (Padre)

Quando un componente viene distrutto:

1.  `ngOnDestroy` (Padre)
    1.  `ngOnDestroy` (Figlio) - i figli vengono distrutti prima del padre.

**Sintesi e Quando Usarli:**

*   **`constructor`**: Dependency Injection, inizializzazione base.
*   **`ngOnChanges`**: Quando un `@Input()` cambia e hai bisogno di reagire a quel cambiamento specifico.
*   **`ngOnInit`**: Logica di inizializzazione principale (es. fetch dati) dopo che gli input sono disponibili. Eseguito una volta.
*   **`ngDoCheck`**: Per change detection personalizzata (usare con cautela).
*   **`ngAfterContentInit`**: Dopo che il contenuto esterno (`<ng-content>`) è stato inizializzato. Eseguito una volta.
*   **`ngAfterContentChecked`**: Dopo ogni controllo del contenuto esterno.
*   **`ngAfterViewInit`**: Dopo che la vista del componente (e dei figli) è stata inizializzata. Ottimo per manipolazioni DOM dipendenti dalla vista (`@ViewChild`). Eseguito una volta.
*   **`ngAfterViewChecked`**: Dopo ogni controllo della vista del componente (e dei figli).
*   **`ngOnDestroy`**: Pulizia! Unsubscribe da Observables, rimuovi event listener, ecc.

Non è necessario implementare tutti gli hook in ogni componente. Implementa solo quelli di cui hai effettivamente bisogno per la logica del tuo componente.

---

Comprendere il ciclo di vita dei componenti è essenziale per scrivere codice Angular robusto, performante e privo di memory leak. Man mano che costruirai componenti più complessi, l'uso corretto di questi hook diventerà una seconda natura.

Con questo si conclude il Modulo 2 sui Componenti Angular. Abbiamo coperto l'anatomia, il data binding, le direttive strutturali e ora il ciclo di vita. Sei pronto per passare al Modulo 3, dove parleremo di Direttive di Attributo e Pipe?