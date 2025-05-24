Perfetto! Passiamo al **Modulo 11: Test e Debugging**. Scrivere test robusti e saper debuggare efficacemente sono competenze cruciali per costruire e mantenere applicazioni Angular di alta qualità.

---

### Modulo 11: Test e Debugging

**Lezione 27: Unit Testing con Jasmine e Karma**

Lo **Unit Testing** (Test Unitario) si concentra sul testare le singole "unità" di codice in isolamento, come singoli metodi di una classe, componenti, servizi o pipe. L'obiettivo è verificare che ogni unità funzioni correttamente secondo le aspettative.

Angular è progettato pensando alla testabilità e fornisce strumenti e utility per facilitare gli unit test.

*   **Jasmine:**
    *   È un framework di Behavior-Driven Development (BDD) per testare codice JavaScript. È il framework di testing predefinito per gli unit test in Angular.
    *   Fornisce funzioni per definire suite di test (`describe`), singoli casi di test (`it`), e per fare asserzioni (`expect`).
    *   Funzioni comuni di Jasmine:
        *   `describe('Descrizione della suite', () => { ... });`: Raggruppa test correlati.
        *   `it('Descrizione del caso di test (specifica cosa dovrebbe fare)', () => { ... });`: Definisce un singolo test.
        *   `expect(valoreEffettivo).toBe(valoreAtteso);`: Verifica se due valori sono uguali (usando `===`).
        *   `expect(obj).toEqual(objAtteso);`: Verifica se due oggetti hanno lo stesso contenuto (confronto profondo).
        *   `expect(valore).toBeTruthy();` / `.toBeFalsy();`
        *   `expect(fn).toThrow();` / `.toThrowError(MessaggioErrore);`
        *   `expect(spy).toHaveBeenCalled();` / `.toHaveBeenCalledWith(args);` (per gli spy)
        *   `beforeEach(() => { ... });`: Esegue codice prima di ogni test `it` nella suite `describe`. Utile per il setup.
        *   `afterEach(() => { ... });`: Esegue codice dopo ogni test `it`. Utile per la pulizia.
        *   `beforeAll(() => { ... });`: Esegue codice una volta prima di tutti i test nella suite.
        *   `afterAll(() => { ... });`: Esegue codice una volta dopo tutti i test nella suite.

*   **Karma:**
    *   È un test runner. Il suo compito è eseguire i tuoi file di test Jasmine (o di altri framework) in browser reali o headless.
    *   Angular CLI configura Karma per te quando crei un nuovo progetto.
    *   Esegue i test, raccoglie i risultati e li mostra nel terminale. Può anche rieseguire i test automaticamente quando i file cambiano.
    *   Il file di configurazione di Karma è `karma.conf.js` nella root del progetto.

**1. Scrivere Unit Test per Componenti**

Testare i componenti è una parte fondamentale. Solitamente vuoi testare:

*   La creazione corretta del componente.
*   L'interazione con il template (es. se i dati vengono visualizzati, se i metodi vengono chiamati al click di un bottone).
*   La logica dei metodi del componente.
*   L'interazione con i servizi iniettati (spesso usando "mock" o "spy").

*   **`TestBed`**:
    *   `TestBed` (da `@angular/core/testing`) è la utility principale per scrivere unit test per componenti, direttive, pipe e servizi Angular.
    *   Permette di creare un ambiente di test che simula un `@NgModule` (o un ambiente per componenti standalone), configurando il componente da testare, le sue dipendenze (componenti figli, direttive, pipe), e i provider di servizi (reali o mockati).

*   **Struttura di un Test di Componente:**
    Angular CLI genera un file `.spec.ts` per ogni componente, che contiene uno scheletro di test.

    ```typescript
    // my-sample.component.ts
    import { Component, EventEmitter, Input, Output, inject } from '@angular/core';
    import { DataService } from '../services/data.service'; // Un servizio di esempio

    @Component({
      selector: 'app-my-sample',
      standalone: true,
      imports: [],
      template: `
        <h1>{{ title() }}</h1>
        <p>Contatore: {{ counter }}</p>
        <button (click)="increment()">Incrementa</button>
        <button (click)="loadDataAndEmit()">Carica Dati</button>
      `
    })
    export class MySampleComponent {
      @Input() title = signal('Titolo Default'); // Input basato su signal
      @Output() dataLoaded = new EventEmitter<string>();

      counter = 0;
      private dataService = inject(DataService); // Iniezione di servizio

      increment() {
        this.counter++;
      }

      async loadDataAndEmit() {
        try {
          const data = await this.dataService.fetchSomething(); // Metodo asincrono nel servizio
          this.dataLoaded.emit(data);
        } catch (error) {
          this.dataLoaded.emit('Errore caricamento dati');
        }
      }
    }

    // my-sample.component.spec.ts
    import { ComponentFixture, TestBed, fakeAsync, tick } from '@angular/core/testing';
    import { MySampleComponent } from './my-sample.component';
    import { DataService } from '../services/data.service'; // Importa il servizio reale
    // Per i test dei signal, potresti non aver bisogno di utility specifiche se testi il comportamento
    // ma per la change detection, TestBed gestisce la sincronizzazione.

    describe('MySampleComponent', () => {
      let component: MySampleComponent;
      let fixture: ComponentFixture<MySampleComponent>; // Fornisce accesso all'istanza del componente e al DOM
      let mockDataService: jasmine.SpyObj<DataService>; // Spy object per DataService

      beforeEach(async () => {
        // Crea uno spy object per DataService.
        // 'fetchSomething' è un metodo che vogliamo spiare (mockare).
        mockDataService = jasmine.createSpyObj('DataService', ['fetchSomething']);

        await TestBed.configureTestingModule({
          imports: [MySampleComponent], // Importa il componente standalone
          providers: [
            // Fornisci il mockDataService invece del DataService reale
            { provide: DataService, useValue: mockDataService }
          ]
        }).compileComponents(); // Compila i template e gli stili del componente (necessario se non usi Webpack)

        fixture = TestBed.createComponent(MySampleComponent); // Crea un'istanza del componente
        component = fixture.componentInstance; // Accedi all'istanza del componente
        // fixture.detectChanges(); // Esegui il primo ciclo di change detection (spesso fatto qui o nel test 'it')
      });

      it('should create', () => {
        expect(component).toBeTruthy();
      });

      it('should display default title se non fornito', () => {
        fixture.detectChanges(); // Triggera la change detection per aggiornare il DOM con il valore del signal
        const h1: HTMLElement = fixture.nativeElement.querySelector('h1');
        expect(h1.textContent).toContain('Titolo Default');
      });

      it('should display input title quando fornito', () => {
        component.title.set('Titolo Test'); // Imposta il valore del signal di input
        fixture.detectChanges(); // Triggera la change detection
        const h1: HTMLElement = fixture.nativeElement.querySelector('h1');
        expect(h1.textContent).toContain('Titolo Test');
      });

      it('should increment counter when increment method is called', () => {
        expect(component.counter).toBe(0);
        component.increment();
        expect(component.counter).toBe(1);
        fixture.detectChanges(); // Aggiorna il template
        const p: HTMLElement = fixture.nativeElement.querySelector('p');
        expect(p.textContent).toContain('Contatore: 1');
      });

      it('should increment counter when increment button is clicked', () => {
        const button = fixture.nativeElement.querySelector('button:nth-of-type(1)'); // Trova il bottone "Incrementa"
        button.click(); // Simula il click
        fixture.detectChanges(); // Lascia che Angular processi l'evento e aggiorni il componente/DOM
        expect(component.counter).toBe(1);
        const p: HTMLElement = fixture.nativeElement.querySelector('p');
        expect(p.textContent).toContain('Contatore: 1');
      });

      it('should call DataService.fetchSomething and emit dataLoaded on loadDataAndEmit', fakeAsync(() => {
        const testData = 'Dati Test Caricati';
        mockDataService.fetchSomething.and.returnValue(Promise.resolve(testData)); // Configura lo spy per restituire una Promise risolta

        spyOn(component.dataLoaded, 'emit'); // Spia l'EventEmitter

        component.loadDataAndEmit(); // Chiama il metodo
        tick(); // Simula il passare del tempo per risolvere le Promise (dentro fakeAsync)

        expect(mockDataService.fetchSomething).toHaveBeenCalled();
        expect(component.dataLoaded.emit).toHaveBeenCalledWith(testData);
      }));

      it('should emit error message if DataService.fetchSomething rejects', fakeAsync(() => {
        mockDataService.fetchSomething.and.returnValue(Promise.reject(new Error('Errore API')));
        spyOn(component.dataLoaded, 'emit');

        component.loadDataAndEmit();
        tick();

        expect(mockDataService.fetchSomething).toHaveBeenCalled();
        expect(component.dataLoaded.emit).toHaveBeenCalledWith('Errore caricamento dati');
      }));
    });
    ```

*   **`ComponentFixture<T>`**:
    *   Un wrapper attorno al componente testato e al suo template.
    *   `fixture.componentInstance`: L'istanza della classe del componente.
    *   `fixture.nativeElement`: L'elemento DOM radice del componente.
    *   `fixture.debugElement`: Un wrapper attorno a `nativeElement` che fornisce API più astratte e sicure per interrogare il DOM (es. `debugElement.query(By.css('selector'))`).
    *   `fixture.detectChanges()`: Triggera manualmente un ciclo di change detection di Angular, aggiornando il DOM con i valori correnti del componente.

*   **Testing Asincrono:**
    *   **`async` e `await` (con `compileComponents`)**: `TestBed.compileComponents()` è asincrono perché potrebbe dover caricare file di template/stili esterni. Si usa `async` nella funzione `beforeEach` e `await` su `compileComponents()`.
    *   **`fakeAsync`, `tick()`, `flush()` (da `@angular/core/testing`)**: Per testare codice asincrono che usa `setTimeout`, `setInterval`, o `Promise` in modo sincrono.
        *   `fakeAsync(() => { ... })`: Esegue il corpo del test in una "zona" `fakeAsync`.
        *   `tick(milliseconds?)`: Simula il passare del tempo.
        *   `flush()`: Svuota la coda di task macro (es. `setTimeout`) e micro (es. `Promise.then`).
    *   **`waitForAsync` (precedentemente `async` da `@angular/core/testing`)**: Per codice asincrono che non può essere facilmente controllato da `fakeAsync` (es. vere chiamate XHR, anche se queste sono solitamente mockate).

**2. Scrivere Unit Test per Servizi**

I servizi sono spesso più facili da testare unitariamente perché non hanno un template o interazioni DOM.

*   **Struttura di un Test di Servizio:**

    ```typescript
    // data.service.ts (esempio base)
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    import { Observable, of } from 'rxjs';
    import { delay } from 'rxjs/operators';

    @Injectable({
      providedIn: 'root'
    })
    export class DataService {
      constructor(private http: HttpClient) {}

      fetchSomething(): Promise<string> {
        return Promise.resolve('Dati Reali').then(val => new Promise(res => setTimeout(() => res(val), 100)));
      }

      getData(): Observable<string[]> {
        // In un caso reale, questo farebbe una chiamata HTTP
        return of(['dato1', 'dato2']).pipe(delay(10));
      }
    }

    // data.service.spec.ts
    import { TestBed } from '@angular/core/testing';
    import { HttpClientTestingModule, HttpTestingController } from '@angular/common/http/testing'; // Per testare HttpClient

    import { DataService } from './data.service';

    describe('DataService', () => {
      let service: DataService;
      let httpTestingController: HttpTestingController; // Controller per mockare le richieste HTTP

      beforeEach(() => {
        TestBed.configureTestingModule({
          imports: [HttpClientTestingModule], // Importa questo modulo per fornire un mock di HttpClient
          providers: [DataService] // Fornisci il servizio reale (o puoi mockarlo se necessario)
        });
        service = TestBed.inject(DataService); // Ottieni un'istanza del servizio
        httpTestingController = TestBed.inject(HttpTestingController); // Ottieni il controller per le richieste HTTP
      });

      afterEach(() => {
        // Verifica che non ci siano richieste HTTP in sospeso non gestite
        httpTestingController.verify();
      });

      it('should be created', () => {
        expect(service).toBeTruthy();
      });

      it('#fetchSomething should return a resolved promise with data', async () => {
        // Non c'è bisogno di mockare HttpClient qui se il metodo non lo usa direttamente,
        // ma se lo usasse, useresti httpTestingController.
        const data = await service.fetchSomething();
        expect(data).toBe('Dati Reali');
      });

      it('#getData should return an Observable with array of strings', (done: DoneFn) => { // Usa done per test asincroni con Observable
        service.getData().subscribe(data => {
          expect(data).toEqual(['dato1', 'dato2']);
          done(); // Chiama done() quando l'Observable completa o hai fatto le tue asserzioni
        });
      });

      // Esempio di test di un metodo che usa HttpClient
      it('should fetch users from API (GET)', () => {
        const mockUsers = [{ id: 1, name: 'Test User' }];
        const testUrl = '/api/users';

        // Fai la chiamata al metodo del servizio che internamente usa this.http.get()
        service.getUsersFromApi(testUrl).subscribe(users => { // Assumiamo che esista getUsersFromApi
          expect(users).toEqual(mockUsers);
        });

        // Aspettati che sia stata fatta una richiesta GET a testUrl
        const req = httpTestingController.expectOne(testUrl);
        expect(req.request.method).toEqual('GET');

        // Rispondi alla richiesta con i dati mockati
        req.flush(mockUsers);
      });
    });
    // Nota: dovresti aggiungere un metodo getUsersFromApi in DataService per il test sopra.
    // export class DataService {
    //   constructor(private http: HttpClient) {}
    //   ...
    //   getUsersFromApi(url: string): Observable<any[]> {
    //     return this.http.get<any[]>(url);
    //   }
    // }
    ```

*   **`HttpClientTestingModule` e `HttpTestingController`**:
    *   Da `@angular/common/http/testing`.
    *   `HttpClientTestingModule`: Fornisce un backend mock per `HttpClient` in modo che le vere chiamate HTTP non vengano fatte durante i test.
    *   `HttpTestingController`: Permette di definire quali richieste ti aspetti, di rispondere a quelle richieste con dati mockati (o errori), e di verificare che le richieste siano state fatte come previsto.

**3. Scrivere Unit Test per Pipe**

I pipe sono generalmente funzioni pure, il che li rende facili da testare.

```typescript
// initial.pipe.ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'initial',
  standalone: true
})
export class InitialPipe implements PipeTransform {
  transform(value: string): string {
    return value ? value.charAt(0).toUpperCase() + '.' : '';
  }
}

// initial.pipe.spec.ts
import { InitialPipe } from './initial.pipe';

describe('InitialPipe', () => {
  let pipe: InitialPipe;

  beforeEach(() => {
    pipe = new InitialPipe(); // Istanzi il pipe direttamente
  });

  it('create an instance', () => {
    expect(pipe).toBeTruthy();
  });

  it('should return the first letter capitalized with a dot for a non-empty string', () => {
    expect(pipe.transform('angular')).toBe('A.');
  });

  it('should return an empty string for an empty string input', () => {
    expect(pipe.transform('')).toBe('');
  });

  it('should return an empty string for null input', () => {
    expect(pipe.transform(null as any)).toBe(''); // Cast a any per testare input non stringa
  });

  it('should handle single character string', () => {
    expect(pipe.transform('B')).toBe('B.');
  });
});
```

**4. Mocking delle Dipendenze**

"Mockare" (o usare "spy") le dipendenze è cruciale per isolare l'unità che stai testando.

*   **`jasmine.createSpyObj()`**: Crea un oggetto con metodi "spiati". Puoi configurare cosa questi metodi restituiscono (`.and.returnValue()`, `.and.callFake()`, `.and.resolveTo()`, `.and.rejectWith()`) e verificare se sono stati chiamati (`expect(spy).toHaveBeenCalled()`). L'abbiamo usato per `DataService` nel test del componente.
*   **`spyOn(object, 'methodName')`**: Spia un metodo esistente su un oggetto reale. Utile se vuoi solo spiare alcuni metodi di un servizio reale ma usare l'implementazione reale per altri.

**5. Code Coverage (Copertura del Codice)**

*   La code coverage misura quale percentuale del tuo codice sorgente è stata eseguita dai tuoi test.
*   Angular CLI può generare un report di code coverage.
    ```bash
    ng test --code-coverage
    # o ng test --no-watch --code-coverage (per una singola esecuzione)
    ```
    Questo crea una cartella `coverage/` nel tuo progetto con un report HTML navigabile.
*   Una buona code coverage (es. >80%) è un indicatore (ma non una garanzia) di test ben scritti. È importante testare non solo il "happy path" ma anche i casi limite e gli errori.

**Comandi Utili per i Test:**

*   `ng test`: Avvia Karma, apre un browser, esegue tutti i test `.spec.ts` e riesegue i test quando i file cambiano.
*   `ng test --no-watch`: Esegue i test una volta e poi esce. Utile per CI/CD.
*   `ng test --browsers ChromeHeadless`: Esegue i test in Chrome headless (senza UI del browser).
*   `ng test --include src/app/nome-componente/nome-componente.spec.ts`: Esegue solo un file di test specifico.

---

Gli unit test sono un investimento che ripaga in termini di qualità del software, manutenibilità e fiducia nel refactoring. `TestBed`, Jasmine e Karma forniscono un ambiente robusto per scrivere unit test efficaci per le tue applicazioni Angular.

Nella prossima lezione, "End-to-End (E2E) Testing", vedremo come testare i flussi utente completi attraverso l'intera applicazione.
