

**Obiettivo Generale:** Fornire le conoscenze necessarie per sviluppare applicazioni web moderne, performanti e manutenibili utilizzando Angular 19.

---

### Programma di Studi Angular 19

**Modulo 1: Fondamenti di Angular e Configurazione dell'Ambiente**

*   **Lezione 1: Introduzione ad Angular**
    *   Cos'è Angular e la sua evoluzione (SPA, framework basato su componenti).
    *   Vantaggi dell'utilizzo di Angular.
    *   Architettura di un'applicazione Angular (Moduli, Componenti, Servizi, ecc.).
    *   Panoramica dell'ecosistema Angular (CLI, Material Design, ecc.).
*   **Lezione 2: Configurazione dell'Ambiente di Sviluppo**
    *   Installazione di Node.js e npm/yarn.
    *   Installazione di Angular CLI.
    *   Creazione di un nuovo progetto Angular con Angular CLI.
    *   Struttura di un progetto Angular.
    *   Avvio del server di sviluppo e visualizzazione dell'applicazione.
*   **Lezione 3: Introduzione a TypeScript**
    *   Perché TypeScript in Angular?
    *   Tipi di base (string, number, boolean, array, any, void, null, undefined).
    *   Interfacce e Classi.
    *   Decorators (cenni, verranno approfonditi con i componenti).
    *   Funzioni e tipi di funzione.
    *   Moduli TypeScript.

**Modulo 2: Componenti Angular: Il Cuore dell'Applicazione**

*   **Lezione 4: Anatomia di un Componente**
    *   Decoratore `@Component` (selector, templateUrl, styleUrls, template, styles).
    *   Il file TypeScript del componente (logica).
    *   Il file HTML del componente (template).
    *   Il file CSS del componente (stili).
    *   Creare e registrare un nuovo componente usando Angular CLI.
*   **Lezione 5: Template Syntax - Data Binding**
    *   Interpolation (`{{ }}`).
    *   Property Binding (`[property]`).
    *   Event Binding (`(event)`).
    *   Two-way Data Binding (`[(ngModel)]`) - necessità di `FormsModule`.
*   **Lezione 6: Template Syntax - Direttive Strutturali**
    *   `*ngIf`: Aggiungere o rimuovere elementi dal DOM.
    *   `*ngFor`: Iterare su collezioni.
    *   `ngSwitch`: Mostrare elementi condizionalmente.
*   **Lezione 7: Ciclo di Vita dei Componenti (Lifecycle Hooks)**
    *   Panoramica dei principali hook: `ngOnInit`, `ngOnChanges`, `ngOnDestroy`, `ngAfterViewInit`.
    *   Quando e come utilizzarli.

**Modulo 3: Direttive e Pipe**

*   **Lezione 8: Direttive di Attributo**
    *   Cosa sono e come funzionano (`ngStyle`, `ngClass`).
    *   Creare direttive di attributo personalizzate.
    *   Manipolare lo stile e il comportamento degli elementi DOM.
*   **Lezione 9: Pipe**
    *   Cosa sono i Pipe e come trasformano i dati nei template.
    *   Pipe integrati (es. `DatePipe`, `UpperCasePipe`, `LowerCasePipe`, `DecimalPipe`, `CurrencyPipe`, `JsonPipe`).
    *   Creare Pipe personalizzati.
    *   Pipe con parametri e chaining di Pipe.

**Modulo 4: Servizi e Dependency Injection (DI)**

*   **Lezione 10: Introduzione ai Servizi**
    *   Perché usare i Servizi (logica di business, condivisione dati).
    *   Creare un Servizio.
    *   Il decoratore `@Injectable`.
*   **Lezione 11: Dependency Injection in Angular**
    *   Concetto di DI.
    *   Come Angular gestisce le dipendenze.
    *   Registrare i provider dei Servizi (a livello di root, modulo o componente).
    *   Iniettare Servizi nei Componenti.

**Modulo 5: Routing e Navigazione**

*   **Lezione 12: Configurazione del Routing di Base**
    *   Importare `RouterModule` e configurare le route (`Routes` array).
    *   `<router-outlet>` e `routerLink`.
    *   Navigazione programmatica con il servizio `Router`.
*   **Lezione 13: Parametri di Rotta e Route Wildcard**
    *   Passare dati alle route (parametri obbligatori e opzionali).
    *   Recuperare i parametri di rotta (`ActivatedRoute`).
    *   Configurare una rotta wildcard (`**`) per le pagine 404.
*   **Lezione 14: Route Guards**
    *   Proteggere le route con i Guard (`CanActivate`, `CanDeactivate`, `Resolve`).
    *   Implementare l'autenticazione e l'autorizzazione di base.
*   **Lezione 15: Lazy Loading dei Moduli**
    *   Migliorare le performance caricando i moduli solo quando necessario.
    *   Configurare il lazy loading per le feature modules.

**Modulo 6: Gestione dei Form in Angular**

*   **Lezione 16: Template-Driven Forms**
    *   Utilizzo di `FormsModule` e `ngForm`.
    *   Binding con `ngModel`.
    *   Validazione di base (required, minlength, maxlength, pattern).
    *   Visualizzazione dei messaggi di errore e stati del form.
*   **Lezione 17: Reactive Forms (Model-Driven Forms)**
    *   Utilizzo di `ReactiveFormsModule`.
    *   `FormControl`, `FormGroup`, `FormArray`.
    *   Creare form nel codice TypeScript.
    *   Validatori sincroni e asincroni personalizzati.
    *   Gestione dinamica dei form.

**Modulo 7: Comunicazione con il Backend (HTTP)**

*   **Lezione 18: Introduzione a `HttpClientModule`**
    *   Importare `HttpClientModule`.
    *   Utilizzare il servizio `HttpClient`.
    *   Effettuare richieste GET, POST, PUT, DELETE.
*   **Lezione 19: Gestione delle Risposte e degli Errori HTTP**
    *   Lavorare con gli `Observable` di RxJS per le risposte HTTP.
    *   Operatori RxJS utili (es. `map`, `tap`, `catchError`).
    *   Gestione degli errori e feedback all'utente.
*   **Lezione 20: Interceptors HTTP**
    *   Intercettare e modificare le richieste/risposte HTTP (es. aggiungere token di autenticazione, logging).

**Modulo 8: Moduli Angular e Standalone Components**

*   **Lezione 21: `NgModule` in Profondità (se ancora rilevante o per progetti legacy)**
    *   Organizzare l'applicazione con i moduli (`declarations`, `imports`, `exports`, `providers`, `bootstrap`).
    *   Feature modules, shared modules, core module.
    *   *Nota: Con l'evoluzione verso i Standalone Components, l'enfasi sui NgModule potrebbe diminuire per i nuovi progetti, ma la comprensione rimane utile.*
*   **Lezione 22: Standalone Components, Directives, e Pipes**
    *   Introduzione ai componenti, direttive e pipe standalone (introdotti in v14 e diventati lo standard in v17+).
    *   Come creare e utilizzare componenti standalone.
    *   Vantaggi e impatto sull'architettura dell'applicazione (semplificazione, riduzione del boilerplate).
    *   Come importare dipendenze direttamente nei componenti standalone.
    *   Routing con componenti standalone.

**Modulo 9: State Management Avanzato**

*   **Lezione 23: Introduzione allo State Management**
    *   Perché è necessario uno state management complesso in grandi applicazioni.
    *   Pattern di base per la gestione dello stato (es. servizi con `BehaviorSubject`).
*   **Lezione 24: Panoramica delle Librerie di State Management (es. NgRx, Akita, Elf)**
    *   Principi di base di NgRx (Store, Actions, Reducers, Effects, Selectors) - *se si sceglie di approfondire NgRx*.
    *   Quando e perché utilizzare una libreria di state management.

**Modulo 10: Signals in Angular**

*   **Lezione 25: Introduzione ai Signals**
    *   Cosa sono i Signals e perché sono stati introdotti (reattività granulare, performance).
    *   Creare e aggiornare i `signal` (`writableSignal`).
    *   `computed` signals per valori derivati.
    *   `effect` per eseguire side effect in risposta a cambiamenti dei signal.
*   **Lezione 26: Integrazione dei Signals con il Framework**
    *   Utilizzo dei Signals nei componenti e nei servizi.
    *   Interoperabilità con RxJS (`toSignal`, `toObservable`).
    *   Input basati su Signal.
    *   Query basate su Signal (`viewChild`, `contentChild`).

**Modulo 11: Test e Debugging**

*   **Lezione 27: Unit Testing con Jasmine e Karma**
    *   Scrivere unit test per componenti, servizi e pipe.
    *   Utilizzo di `TestBed` e utility di testing di Angular.
    *   Mocking delle dipendenze.
    *   Code coverage.
*   **Lezione 28: End-to-End (E2E) Testing (es. con Protractor/WebDriverIO/Playwright/Cypress)**
    *   Principi di E2E testing.
    *   Scrivere test E2E di base.
*   **Lezione 29: Debugging di Applicazioni Angular**
    *   Utilizzo dei DevTools del browser.
    *   Angular DevTools (estensione del browser).
    *   Tecniche di logging e breakpoint.

**Modulo 12: Build, Deployment e Novità di Angular 19**

*   **Lezione 30: Build di Produzione e Ottimizzazioni**
    *   Comando `ng build` per la produzione.
    *   Ahead-of-Time (AOT) compilation.
    *   Tree shaking e altre ottimizzazioni.
    *   Bundle-budgets.
*   **Lezione 31: Deployment**
    *   Strategie di deployment (diverse opzioni di hosting).
    *   Configurazioni per diversi ambienti (development, staging, production).
*   **Lezione 32: Approfondimento sulle Novità di Angular 19**
    *   Revisione dettagliata delle nuove funzionalità, API e breaking changes introdotte specificamente in Angular 19 (basata sulla documentazione ufficiale e sui blog post al momento del rilascio).
    *   Best practice per migrare a Angular 19.
    *   Focus sulle performance, Developer Experience (DX) e innovazioni del framework.

**Risorse e Suggerimenti:**

*   **Documentazione Ufficiale Angular:** Sarà la risorsa principale, specialmente per le novità di Angular 19.
*   **Blog Ufficiale Angular:** Per annunci e articoli approfonditi.
*   **Tutorial e Corsi Online:** Piattaforme come Udemy, Coursera, Pluralsight, e canali YouTube dedicati ad Angular.
*   **Comunità Angular:** Forum, Discord, Stack Overflow.
*   **Pratica Costante:** Realizzare piccoli progetti o contribuire a progetti esistenti è fondamentale per consolidare l'apprendimento.
