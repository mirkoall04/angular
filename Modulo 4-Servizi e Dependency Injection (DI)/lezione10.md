Certamente! Iniziamo il **Modulo 4: Servizi e Dependency Injection (DI)**. Questo modulo è cruciale per capire come strutturare applicazioni Angular complesse in modo manutenibile e testabile. Partiamo con la **Lezione 10: Introduzione ai Servizi**.

---

### Modulo 4: Servizi e Dependency Injection (DI)

**Lezione 10: Introduzione ai Servizi**

Nei componenti Angular, la logica dovrebbe concentrarsi principalmente sulla gestione della vista e sull'interazione con l'utente. Compiti come il recupero di dati da un server, la validazione complessa, il logging, o la condivisione di stato tra componenti non "appartengono" direttamente a un singolo componente. Per queste finalità, Angular introduce il concetto di **Servizi**.

**1. Perché usare i Servizi (logica di business, condivisione dati)**

Un **Servizio** in Angular è tipicamente una classe TypeScript con uno scopo ben preciso e limitato. Non è legato a una vista specifica e può essere utilizzato da più componenti o altri servizi.

Ecco i motivi principali per cui i servizi sono fondamentali:

*   **Separazione delle Preoccupazioni (Separation of Concerns):**
    *   I componenti si occupano della presentazione e dell'interazione utente.
    *   I servizi gestiscono la logica di business, l'accesso ai dati, calcoli complessi, o altre funzionalità che non sono direttamente legate alla UI.
    *   Questa separazione rende il codice più organizzato, più facile da capire, da testare e da manutenere.

*   **Riusabilità del Codice:**
    *   La logica incapsulata in un servizio può essere riutilizzata da qualsiasi componente o altro servizio che ne abbia bisogno. Ad esempio, un `AuthService` per gestire l'autenticazione può essere usato dal componente di login, dalla navbar per mostrare/nascondere link, e dai route guards per proteggere le rotte.
    *   Evita la duplicazione di codice.

*   **Condivisione di Dati e Stato tra Componenti:**
    *   I servizi sono un ottimo modo per condividere dati o stato tra componenti che non hanno una relazione diretta padre-figlio.
    *   Un servizio agisce come un "single source of truth". Un componente può aggiornare i dati nel servizio, e un altro componente può leggere quei dati aggiornati dal medesimo servizio.
    *   Esempio: un `ShoppingCartService` che tiene traccia degli articoli nel carrello può essere accessibile da un `ProductListComponent` (per aggiungere articoli) e da un `CartViewComponent` (per visualizzare il carrello).

*   **Migliore Testabilità:**
    *   La logica di business isolata nei servizi è più facile da testare unitariamente. Puoi testare un servizio indipendentemente dai componenti che lo utilizzano.
    *   Quando testi i componenti, puoi "mockare" (simulare) i servizi da cui dipendono, rendendo i test dei componenti più focalizzati e meno fragili.

*   **Dependency Injection:**
    *   Angular ha un sistema di Dependency Injection (DI) integrato. I servizi sono tipicamente resi disponibili ai componenti (o ad altri servizi) attraverso la DI. Questo significa che Angular si occupa di creare e fornire le istanze dei servizi quando sono richieste. Approfondiremo la DI nella prossima lezione.

**Esempi di cosa potrebbe fare un Servizio:**

*   **Accesso ai Dati:**
    *   `ProductService`: Recupera la lista dei prodotti da un'API backend, aggiunge un nuovo prodotto, aggiorna un prodotto esistente.
    *   `UserService`: Gestisce i dati dell'utente, il login, la registrazione.
*   **Logica di Business Specifica:**
    *   `CalculationService`: Esegue calcoli finanziari o scientifici complessi.
    *   `OrderProcessingService`: Gestisce la logica per processare un ordine.
*   **Utility e Funzionalità Trasversali:**
    *   `LoggingService`: Fornisce metodi per loggare messaggi a vari livelli (console, server).
    *   `NotificationService`: Mostra notifiche (toast, popup) all'utente.
    *   `LocalStorageService`: Incapsula l'interazione con il `localStorage` del browser.
*   **Gestione dello Stato (semplice):**
    *   Un servizio può mantenere uno stato interno (es. una lista di preferiti) e fornire metodi per modificarlo e Observables per notificare i cambiamenti. (Per una gestione dello stato più complessa, si usano librerie come NgRx o si adottano pattern più avanzati basati su Signals/RxJS).

**2. Creare un Servizio**

Creare un servizio in Angular è semplice. È una classe TypeScript standard. Per renderlo "iniettabile" (cioè disponibile tramite Dependency Injection), si usa il decoratore `@Injectable`.

*   **Generare un Servizio con Angular CLI:**
    Il modo più semplice per creare un servizio è usare Angular CLI:
    ```bash
    ng generate service nome-del-tuo-servizio
    # o ng g s nome-del-tuo-servizio
    ```
    Ad esempio, per creare un servizio chiamato `logger`:
    ```bash
    ng g s logger
    ```
    Oppure, se vuoi metterlo in una cartella specifica, ad esempio `services`:
    ```bash
    ng g s services/logger
    ```

*   **Cosa genera il CLI:**
    Questo comando crea due file:
    *   `logger.service.ts`: Il file della classe del servizio.
    *   `logger.service.spec.ts`: Il file per gli unit test del servizio.

    Il file `logger.service.ts` generato sarà simile a questo:

    ```typescript
    // logger.service.ts
    import { Injectable } from '@angular/core';

    @Injectable({
      providedIn: 'root' // Opzione di default e spesso la migliore
    })
    export class LoggerService {

      constructor() { }

      log(message: string): void {
        console.log(`[LOG]: ${new Date().toLocaleTimeString()} - ${message}`);
      }

      warn(message: string): void {
        console.warn(`[WARN]: ${new Date().toLocaleTimeString()} - ${message}`);
      }

      error(message: string): void {
        console.error(`[ERROR]: ${new Date().toLocaleTimeString()} - ${message}`);
      }
    }
    ```

**3. Il Decoratore `@Injectable`**

Il decoratore `@Injectable()` (importato da `@angular/core`) contrassegna una classe come servizio che può essere gestito dal sistema di Dependency Injection di Angular.

*   **`providedIn`**:
    *   Questa proprietà metadata di `@Injectable()` è stata introdotta in Angular 6 ed è il modo raccomandato per registrare un provider per il servizio.
    *   **`providedIn: 'root'`**:
        *   Questa è l'opzione più comune e **raccomandata** per la maggior parte dei servizi.
        *   Significa che Angular crea una **singola istanza singleton** del servizio (un'istanza condivisa a livello di applicazione).
        *   Il servizio viene registrato con il provider radice dell'applicazione.
        *   Un grande vantaggio è che i servizi forniti in `'root'` sono **tree-shakable**. Se il servizio non viene mai iniettato (usato) da nessuna parte nella tua applicazione, il compilatore Angular può rimuoverlo dal bundle finale di produzione, riducendo la dimensione dell'applicazione.
    *   **`providedIn: MyModule`** (dove `MyModule` è un modulo specifico):
        *   Puoi fornire un servizio a livello di un modulo specifico (tipicamente un modulo lazy-loaded). In questo caso, il servizio sarà singleton all'interno del contesto di quel modulo. Se il modulo viene caricato più volte (raro), potresti ottenere più istanze.
    *   **`providedIn: 'any'`**:
        *   Ogni modulo lazy-loaded che importa il servizio otterrà la sua istanza privata del servizio.
    *   **`providedIn: 'platform'`**:
        *   Un singleton a livello di piattaforma (raramente usato, utile se hai più applicazioni Angular sulla stessa pagina).
    *   **Omettere `providedIn` (approccio più vecchio):**
        *   Se ometti `providedIn`, devi registrare manualmente il servizio nell'array `providers` di un `@NgModule` o di un `@Component` (per i servizi a livello di componente). Questo approccio non è tree-shakable come `providedIn: 'root'`.

        ```typescript
        // Approccio più vecchio - da evitare per i nuovi servizi a meno di esigenze specifiche
        // logger.service.ts
        import { Injectable } from '@angular/core';

        @Injectable() // Senza providedIn
        export class OldLoggerService { /* ... */ }

        // app.module.ts (o altro modulo)
        import { NgModule } from '@angular/core';
        import { BrowserModule } from '@angular/platform-browser';
        import { AppComponent } from './app.component';
        import { OldLoggerService } from './old-logger.service';

        @NgModule({
          declarations: [AppComponent],
          imports: [BrowserModule],
          providers: [OldLoggerService], // Registrazione manuale del provider
          bootstrap: [AppComponent]
        })
        export class AppModule { }
        ```
        Con i componenti standalone, l'approccio `providedIn: 'root'` è ancora il più comune per i servizi singleton globali. Se un servizio è usato solo da un componente standalone o da un piccolo gruppo di essi, potresti considerare di fornirlo nell'array `providers` del componente, ma questo crea un'istanza del servizio per ogni istanza del componente, quindi usalo con cognizione.

**Struttura di un Servizio Semplice:**

Un servizio è solo una classe. Può avere:

*   **Proprietà:** Per memorizzare dati o stato.
*   **Metodi:** Per esporre la sua API (le funzionalità che offre).
*   **Costruttore:** Per la dependency injection (un servizio può dipendere da altri servizi, come `HttpClient`).

Esempio di un `DataService` che simula il recupero di dati:

```typescript
// data.service.ts
import { Injectable } from '@angular/core';
import { Observable, of, delay } from 'rxjs'; // Per dati asincroni

export interface Item {
  id: number;
  name: string;
}

@Injectable({
  providedIn: 'root'
})
export class DataService {
  private items: Item[] = [
    { id: 1, name: 'Articolo 1' },
    { id: 2, name: 'Articolo 2' },
    { id: 3, name: 'Articolo 3' }
  ];

  constructor() {
    console.log('DataService istanziato!');
  }

  getItems(): Observable<Item[]> {
    console.log('DataService: getItems chiamato');
    // Simula una chiamata API asincrona con un ritardo
    return of(this.items).pipe(delay(1000));
  }

  getItemById(id: number): Observable<Item | undefined> {
    const item = this.items.find(i => i.id === id);
    return of(item).pipe(delay(500));
  }

  addItem(newItemName: string): Observable<Item> {
    const newId = this.items.length > 0 ? Math.max(...this.items.map(i => i.id)) + 1 : 1;
    const newItem: Item = { id: newId, name: newItemName };
    this.items.push(newItem);
    console.log('DataService: addItem chiamato, nuovo item:', newItem);
    return of(newItem).pipe(delay(200));
  }
}
```

Nella prossima lezione, vedremo come questi servizi vengono "iniettati" e utilizzati nei componenti (o in altri servizi) grazie al sistema di **Dependency Injection** di Angular.

---

Abbiamo visto perché i servizi sono utili e come crearne uno di base. Sono il luogo ideale per la logica non legata alla UI, rendendo le tue applicazioni più modulari e gestibili.

Domande sull'introduzione ai servizi?