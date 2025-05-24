
Ottimo! Proseguiamo con la **Lezione 11: Dependency Injection in Angular**. Questo è un concetto fondamentale che lega insieme componenti e servizi in modo flessibile e testabile.

---

### Modulo 4: Servizi e Dependency Injection (DI)

**Lezione 11: Dependency Injection in Angular**

La **Dependency Injection (DI)** è un design pattern ampiamente utilizzato nello sviluppo software, e Angular ha un suo sistema di DI integrato che è una delle sue caratteristiche principali. La DI permette a una classe (il "client") di ricevere le istanze di altre classi (le "dipendenze" o "servizi") di cui ha bisogno, invece di crearle essa stessa.

**1. Concetto di DI**

Immagina di avere un componente `UserProfileComponent` che ha bisogno di un `UserService` per recuperare i dati dell'utente.

*   **Senza DI (approccio problematico):**
    Il `UserProfileComponent` dovrebbe creare manualmente un'istanza di `UserService`:
    ```typescript
    // user-profile.component.ts (SENZA DI - NON RACCOMANDATO)
    import { Component, OnInit } from '@angular/core';
    import { UserService } from './user.service'; // Assumiamo che esista

    @Component({ /* ... */ })
    export class UserProfileComponent implements OnInit {
      user: any;
      private userService: UserService;

      constructor() {
        this.userService = new UserService(); // Creazione diretta della dipendenza
      }

      ngOnInit(): void {
        this.user = this.userService.getCurrentUser();
      }
    }
    ```
    **Problemi di questo approccio:**
    1.  **Accoppiamento Stretto:** `UserProfileComponent` è strettamente accoppiato a `UserService`. Se la firma del costruttore di `UserService` cambia, o se `UserService` ha a sua volta delle dipendenze, devi modificare `UserProfileComponent`.
    2.  **Difficile da Testare:** Quando testi `UserProfileComponent`, stai implicitamente testando anche `UserService`. È difficile "mockare" (simulare) `UserService` per isolare il test del componente.
    3.  **Mancanza di Flessibilità:** Non puoi facilmente sostituire `UserService` con un'altra implementazione (es. `MockUserService` per i test, o `AdminUserService` per un ruolo specifico) senza modificare il codice del componente.
    4.  **Gestione delle Istanze:** Il componente è responsabile della creazione e della gestione del ciclo di vita dell'istanza del servizio. Questo può portare a più istanze dello stesso servizio se più componenti lo creano, quando invece potresti volere un singleton.

*   **Con DI:**
    Con la DI, il `UserProfileComponent` dichiara semplicemente che *ha bisogno* di un `UserService`, e Angular si occupa di fornirglielo.
    ```typescript
    // user-profile.component.ts (CON DI - RACCOMANDATO)
    import { Component, OnInit } from '@angular/core';
    import { UserService } from './user.service'; // Assumiamo che esista e sia @Injectable

    @Component({ /* ... */ })
    export class UserProfileComponent implements OnInit {
      user: any;

      // 1. Dichiara la dipendenza nel costruttore
      constructor(private userService: UserService) {} // Angular inietterà un'istanza di UserService

      ngOnInit(): void {
        this.user = this.userService.getCurrentUser();
      }
    }
    ```
    **Vantaggi di questo approccio:**
    1.  **Accoppiamento Debole:** `UserProfileComponent` non sa come viene creato `UserService` o da dove proviene. Si fida che Angular gli fornisca un'istanza funzionante.
    2.  **Migliore Testabilità:** Puoi facilmente fornire un `MockUserService` quando testi `UserProfileComponent`.
    3.  **Maggiore Flessibilità:** Angular può essere configurato per fornire diverse implementazioni di `UserService` in contesti diversi.
    4.  **Gestione Centralizzata delle Istanze:** Angular gestisce la creazione e il ciclo di vita delle istanze dei servizi, tipicamente come singleton (se configurato con `providedIn: 'root'`).

**In breve, con la DI:**

*   Le classi **dichiarano** le loro dipendenze (solitamente tramite i parametri del costruttore).
*   Un **"iniettore"** (Injector) è responsabile di:
    *   Creare le istanze delle dipendenze.
    *   Risolvere le dipendenze delle dipendenze stesse (se un servizio A dipende da un servizio B).
    *   Fornire queste istanze al client quando ne ha bisogno.

**2. Come Angular gestisce le dipendenze: L'Injector e i Provider**

Angular ha un sistema di DI gerarchico. Ogni applicazione Angular ha almeno un **injector radice** (root injector), ma possono esistere altri injector a livelli inferiori (es. a livello di modulo lazy-loaded o a livello di componente).

*   **Injector (Iniettore):**
    *   È un oggetto che contiene le istruzioni (chiamate **provider**) su come creare le istanze dei servizi.
    *   Quando un componente (o un altro servizio) richiede una dipendenza nel suo costruttore, l'injector controlla se ha già un'istanza di quel servizio.
        *   Se sì, la restituisce.
        *   Se no, usa il provider per creare una nuova istanza, la memorizza per usi futuri (caching), e la restituisce.
    *   Se l'injector corrente non ha un provider per il servizio richiesto, delega la richiesta all'injector padre nella gerarchia. Se nessun injector nella catena può fornire il servizio, Angular solleva un errore (tipicamente un "NullInjectorError" o "No provider for ServiceName!").

*   **Provider (Fornitore):**
    *   Un provider è una "ricetta" o istruzione che dice all'injector **come creare e fornire** un'istanza di una dipendenza.
    *   Quando registri un servizio (es. con `@Injectable({ providedIn: 'root' })` o nell'array `providers` di un modulo/componente), stai essenzialmente configurando un provider.

    Un provider ha due cose principali:
    1.  **Token (o Chiave di Iniezione):** Un identificatore univoco che l'injector usa per trovare il provider. Solitamente, il token è il tipo (la classe) del servizio stesso (es. `UserService`). Può anche essere una stringa o un `InjectionToken`.
    2.  **Istruzioni di Creazione:** Come creare l'istanza. Ci sono diversi modi per definire un provider:
        *   **`useClass` (il più comune per i servizi):** Dice all'injector di istanziare una classe specifica.
            ```typescript
            // providers: [UserService] // Scorciatoia per:
            // providers: [{ provide: UserService, useClass: UserService }]
            ```
        *   **`useValue`:** Fornisce un valore fisso. Utile per costanti o oggetti di configurazione.
            ```typescript
            // providers: [{ provide: 'API_ENDPOINT', useValue: 'https://api.example.com' }]
            ```
        *   **`useExisting`:** Usa un altro token per risolvere la dipendenza. Crea un alias.
            ```typescript
            // class NewLogger {}
            // class OldLogger {}
            // providers: [NewLogger, { provide: OldLogger, useExisting: NewLogger }]
            // Chiunque richieda OldLogger riceverà un'istanza di NewLogger.
            ```
        *   **`useFactory`:** Usa una funzione factory per creare l'istanza. Questo dà la massima flessibilità, permettendo di usare logica complessa per creare la dipendenza, magari basata su altre dipendenze.
            ```typescript
            // function myServiceFactory(otherService: OtherService, config: AppConfig) {
            //   return new MyService(otherService, config.setting);
            // }
            // providers: [
            //   {
            //     provide: MyService,
            //     useFactory: myServiceFactory,
            //     deps: [OtherService, 'APP_CONFIG_TOKEN'] // Dipendenze della factory
            //   }
            // ]
            ```

**3. Registrare i provider dei Servizi**

Ci sono diversi modi e livelli in cui puoi registrare i provider dei servizi:

*   **A livello di Root (Singleton per l'intera applicazione):**
    *   **Metodo Raccomandato (da Angular 6+):** Usare `providedIn: 'root'` nel decoratore `@Injectable` del servizio stesso.
        ```typescript
        // logger.service.ts
        import { Injectable } from '@angular/core';

        @Injectable({
          providedIn: 'root'
        })
        export class LoggerService { /* ... */ }
        ```
        Questo registra `LoggerService` con l'injector radice. L'istanza sarà un singleton globale e il servizio sarà tree-shakable.
    *   **Metodo più vecchio (ancora valido ma meno comune per nuovi servizi):** Aggiungere il servizio all'array `providers` dell'`AppModule` (o di un modulo principale importato nell'`AppModule`).
        ```typescript
        // app.module.ts (se non usi `providedIn: 'root'`)
        // import { LoggerService } from './logger.service';
        // @NgModule({
        //   providers: [LoggerService],
        //   /* ... */
        // })
        // export class AppModule { }
        ```

*   **A livello di Modulo (Singleton per quel modulo e i suoi figli, o specifico per moduli lazy-loaded):**
    *   **Con `providedIn: MySpecificModule`**:
        ```typescript
        // feature.module.ts
        import { NgModule } from '@angular/core';
        import { FeatureSpecificService } from './feature-specific.service';

        @NgModule({ /* ... */ })
        export class FeatureModule { }

        // feature-specific.service.ts
        @Injectable({
          providedIn: FeatureModule // Il servizio è scoped a FeatureModule
        })
        export class FeatureSpecificService { /* ... */ }
        ```
    *   **Aggiungendo all'array `providers` di un modulo specifico:**
        Se il modulo è caricato eager (all'avvio), il servizio sarà comunque un singleton a livello di root (a meno che non ci sia già un provider per lo stesso token a un livello superiore).
        Se il modulo è **lazy-loaded**, questo crea un injector figlio per il modulo lazy-loaded, e il servizio sarà un singleton *all'interno di quel modulo lazy-loaded e dei suoi componenti*. Questo è utile se vuoi che un servizio sia disponibile solo quando una certa parte dell'applicazione viene caricata, o se vuoi un'istanza separata del servizio per ogni modulo lazy-loaded.
        ```typescript
        // my-lazy.module.ts
        // import { MyLazyScopedService } from './my-lazy-scoped.service';
        // @NgModule({
        //   providers: [MyLazyScopedService], // Sarà un singleton per questo modulo lazy
        //   /* ... */
        // })
        // export class MyLazyModule { }
        ```

*   **A livello di Componente (Nuova istanza per ogni istanza del componente):**
    *   Puoi aggiungere un servizio all'array `providers` del decoratore `@Component`.
    *   Questo crea una **nuova istanza del servizio per ogni istanza di quel componente** (e dei suoi componenti figli, a meno che un figlio non sovrascriva il provider).
    *   Questo è meno comune per i servizi di "business logic" ma può essere utile per servizi che mantengono uno stato strettamente legato a una singola istanza di un componente.
    ```typescript
    // item-details.component.ts
    // import { ItemStateService } from './item-state.service';
    // @Component({
    //   selector: 'app-item-details',
    //   template: `...`,
    //   providers: [ItemStateService] // Nuova istanza di ItemStateService per ogni app-item-details
    // })
    // export class ItemDetailsComponent {
    //   constructor(private itemState: ItemStateService) {}
    // }
    ```
    Per i componenti standalone, questo approccio è ancora valido.

**Gerarchia degli Injector:**

Quando un componente richiede un servizio, Angular cerca un provider in questo ordine:
1.  Nell'injector del componente stesso (se ha `providers`).
2.  Nell'injector del componente padre, e così via, risalendo l'albero dei componenti.
3.  Nell'injector del modulo a cui appartiene il componente (se non è standalone).
4.  Nell'injector radice dell'applicazione.

La prima istanza trovata viene utilizzata. Questo permette di "sovrascrivere" i servizi a livelli inferiori della gerarchia se necessario.

**4. Iniettare Servizi nei Componenti (e in altri Servizi)**

Il modo standard per ricevere un'istanza di un servizio è dichiararlo come dipendenza nel **costruttore** della classe client (componente o altro servizio) usando la sintassi dei parametri TypeScript.

```typescript
// user-list.component.ts
import { Component, OnInit } from '@angular/core';
import { UserService } from '../services/user.service'; // Assumiamo che esista
import { LoggerService } from '../services/logger.service'; // Assumiamo che esista

@Component({
  selector: 'app-user-list',
  standalone: true,
  // Se UserService o LoggerService non fossero 'providedIn: "root"',
  // e fossero standalone, andrebbero importati qui.
  // Ma se sono 'providedIn: "root"', non è necessario importarli negli 'imports' del componente.
  imports: [],
  template: `
    <h3>Lista Utenti</h3>
    <ul>
      <li *ngFor="let user of users">{{ user.name }}</li>
    </ul>
  `
})
export class UserListComponent implements OnInit {
  users: any[] = [];

  // Angular inietterà le istanze di UserService e LoggerService
  constructor(
    private userService: UserService,
    private logger: LoggerService
  ) {}

  ngOnInit(): void {
    this.logger.log('UserListComponent: Inizializzazione...');
    this.users = this.userService.getUsers();
    this.logger.log(`UserListComponent: Caricati ${this.users.length} utenti.`);
  }
}
```
*   Il modificatore di accesso `private` (o `public`, `protected`) davanti ai parametri del costruttore (es. `private userService: UserService`) è una scorciatoia TypeScript che fa due cose:
    1.  Dichiara un parametro del costruttore.
    2.  Crea una proprietà membro con lo stesso nome e tipo nella classe e le assegna automaticamente il valore del parametro iniettato.
    Quindi, `this.userService` è disponibile in tutta la classe `UserListComponent`.

**`InjectionToken`**

A volte, potresti voler iniettare valori che non sono classi, come stringhe di configurazione, oggetti di configurazione, o funzioni. In questi casi, non puoi usare il tipo della classe come token di iniezione. Qui entra in gioco `InjectionToken`.

Un `InjectionToken` crea un token di iniezione opaco che puoi usare per fornire e iniettare questi tipi di valori.

```typescript
// app.config.ts
import { InjectionToken } from '@angular/core';

export interface AppConfig {
  apiUrl: string;
  apiKey: string;
  featureFlags: {
    betaFeatureEnabled: boolean;
  };
}

// Crea un InjectionToken con un tipo generico e una descrizione
export const APP_CONFIG_TOKEN = new InjectionToken<AppConfig>('app.config');

export const DEFAULT_APP_CONFIG: AppConfig = {
  apiUrl: 'https://default.api.com',
  apiKey: 'default_key',
  featureFlags: {
    betaFeatureEnabled: false
  }
};

// app.config.ts o main.ts (o un file di configurazione apposito)
// Per fornire il valore:
// In un array di providers (es. in main.ts per standalone bootstrap, o app.module.ts)
// { provide: APP_CONFIG_TOKEN, useValue: someAppSpecificConfig }
```

```typescript
// some-feature.service.ts
import { Injectable, Inject } from '@angular/core';
import { AppConfig, APP_CONFIG_TOKEN } from './app.config'; // Importa il token e il tipo

@Injectable({
  providedIn: 'root'
})
export class SomeFeatureService {
  private apiUrl: string;

  // Inietta la configurazione usando @Inject(TOKEN_NAME)
  constructor(@Inject(APP_CONFIG_TOKEN) private config: AppConfig) {
    this.apiUrl = config.apiUrl;
    console.log('SomeFeatureService: API URL dalla configurazione:', this.apiUrl);
    if (config.featureFlags.betaFeatureEnabled) {
      console.log('SomeFeatureService: La Beta Feature è ABILITATA!');
    }
  }

  fetchData() {
    // Usa this.apiUrl per le chiamate API
  }
}
```
L'uso di `@Inject()` è necessario quando il tipo della dipendenza non è sufficiente per identificare il token (come nel caso di `InjectionToken` o quando si iniettano tipi primitivi o interfacce).

---

La Dependency Injection è un pilastro di Angular. Rende le applicazioni modulari, disaccoppiate, più facili da testare e più flessibili. Comprendere come funzionano gli injector e i provider è fondamentale per scrivere codice Angular efficace e scalabile.

Con questo, abbiamo coperto le basi dei Servizi e della Dependency Injection. Nel prossimo modulo, "Routing e Navigazione", vedremo come usare i servizi in congiunzione con il router di Angular.
