### Modulo 1: Fondamenti di Angular e Configurazione dell'Ambiente

**Lezione 1: Introduzione ad Angular**

In questa lezione, getteremo le basi per comprendere cos'è Angular, perché è una scelta popolare per lo sviluppo web moderno e quali sono i suoi concetti fondamentali.

**1. Cos'è Angular e la sua evoluzione (SPA, framework basato su componenti)**

*   **Cos'è Angular?**
    *   Angular è un **framework open-source per lo sviluppo di applicazioni web**, basato su **TypeScript** (un superset di JavaScript che aggiunge la tipizzazione statica).
    *   È stato creato e viene attivamente mantenuto da **Google** e da una vasta community di sviluppatori.
    *   L'obiettivo principale di Angular è semplificare lo sviluppo di **Single Page Applications (SPA)** complesse e performanti.
    *   È una soluzione "opinionated", il che significa che fornisce una struttura ben definita e un set di best practice su come costruire le applicazioni, il che può essere molto utile per i team e per la manutenibilità a lungo termine.

*   **Single Page Application (SPA):**
    *   Una SPA è un'applicazione web che carica una **singola pagina HTML** e poi **aggiorna dinamicamente il contenuto** di quella pagina man mano che l'utente interagisce con l'applicazione.
    *   A differenza delle applicazioni web tradizionali multi-pagina (MPA) che richiedono un ricaricamento completo della pagina per ogni nuova vista, le SPA offrono un'esperienza utente più fluida e reattiva, simile a quella delle applicazioni desktop o mobile.
    *   Angular gestisce il routing (la navigazione tra diverse "viste" o "pagine" virtuali) e la manipolazione del DOM (Document Object Model) per creare questa esperienza dinamica.

*   **Framework basato su Componenti:**
    *   Angular adotta un'architettura basata su **componenti**. Questo è uno dei concetti più importanti da capire.
    *   Un **componente** è un blocco di costruzione autonomo e riutilizzabile che controlla una porzione dell'interfaccia utente (UI). Ogni componente è composto da:
        *   Un **template HTML** che definisce la struttura della vista.
        *   Una **classe TypeScript** che contiene la logica e i dati per quella vista.
        *   Opzionalmente, degli **stili CSS** specifici per quel componente.
    *   Le applicazioni Angular sono essenzialmente un albero di componenti che lavorano insieme. Ad esempio, potresti avere un componente `NavbarComponent`, un `SidebarComponent`, e un `ArticleListComponent`, tutti orchestrati da un componente principale `AppComponent`.
    *   Questo approccio modulare rende le applicazioni più facili da sviluppare, testare, manutenere e scalare.

*   **Evoluzione di Angular:**
    *   La prima versione, conosciuta come **AngularJS** (o Angular 1.x), è stata rilasciata nel 2010 e ha rivoluzionato lo sviluppo front-end introducendo concetti come il data binding bidirezionale e la dependency injection.
    *   Nel 2016, Google ha rilasciato **Angular 2**, che è stata una **riscrittura completa** di AngularJS. Questa nuova versione (a cui ci si riferisce semplicemente come "Angular", senza il "JS") ha introdotto TypeScript come linguaggio principale, un'architettura basata su componenti più robusta, miglioramenti significativi nelle performance e un focus sulla compatibilità mobile.
    *   Da Angular 2 in poi, il team di Angular ha adottato un ciclo di rilascio regolare (circa ogni 6 mesi per le major release) introducendo nuove funzionalità, miglioramenti e ottimizzazioni. Ogni nuova versione (Angular 4, 5, ..., 17, 18, e la futura Angular 19) si basa sulle fondamenta della precedente, garantendo una certa continuità ma anche evolvendo per rimanere al passo con le esigenze dello sviluppo web moderno. Le versioni recenti hanno posto grande enfasi su:
        *   **Miglioramento delle performance** (es. Ivy renderer, Signals).
        *   **Riduzione della dimensione del bundle** (es. tree-shaking).
        *   **Miglioramento della Developer Experience (DX)** (es. Standalone Components, build più veloci).

**2. Vantaggi dell'utilizzo di Angular**

Scegliere Angular per un progetto offre diversi vantaggi significativi:

*   **Struttura Completa e "Opinionated":** Fornisce una struttura chiara e strumenti integrati per molte esigenze comuni (routing, gestione form, client HTTP), riducendo la necessità di scegliere e integrare molte librerie di terze parti. Questo porta a una maggiore coerenza, specialmente in team di grandi dimensioni.
*   **TypeScript:** L'uso di TypeScript offre i benefici della tipizzazione statica, come:
    *   Migliore leggibilità e manutenibilità del codice.
    *   Rilevamento degli errori in fase di compilazione (prima che arrivino in produzione).
    *   Migliore autocompletamento e refactoring negli IDE.
*   **Architettura basata su Componenti:** Promuove la modularità, la riusabilità del codice e la separazione delle preoccupazioni, facilitando lo sviluppo e la manutenzione di applicazioni complesse.
*   **Ecosistema Potente:**
    *   **Angular CLI (Command Line Interface):** Uno strumento a riga di comando estremamente potente che semplifica la creazione di progetti, la generazione di componenti, servizi, moduli, l'esecuzione di test, il build per la produzione e molto altro.
    *   **Testing Integrato:** Angular è progettato pensando alla testabilità, con supporto per unit test (con Jasmine e Karma) e test end-to-end.
    *   **Supporto per PWA (Progressive Web Apps) e Mobile:** Facilita la creazione di PWA e può essere combinato con framework come Ionic per creare app mobile ibride.
*   **Performance:**
    *   **Ahead-of-Time (AOT) Compilation:** Compila i template HTML e TypeScript in codice JavaScript efficiente durante la fase di build, anziché nel browser, migliorando i tempi di rendering.
    *   **Tree Shaking:** Rimuove il codice non utilizzato durante il processo di build, riducendo la dimensione finale del bundle.
    *   **Lazy Loading:** Permette di caricare parti dell'applicazione (moduli) solo quando sono effettivamente necessarie, migliorando il tempo di caricamento iniziale.
    *   **Signals (introdotti più recentemente):** Un nuovo sistema di reattività che permette aggiornamenti più granulari e performanti della UI.
*   **Scalabilità:** L'architettura modulare e la struttura ben definita rendono Angular adatto a sviluppare applicazioni di grandi dimensioni e complesse che devono evolvere nel tempo.
*   **Supporto da Google e Ampia Community:** Essendo supportato da Google, Angular beneficia di uno sviluppo continuo e di un supporto a lungo termine. La vasta community offre abbondanti risorse, tutorial, librerie di terze parti e supporto sui forum.

**3. Architettura di un'applicazione Angular (Moduli, Componenti, Servizi, ecc.) - Panoramica Iniziale**

Un'applicazione Angular è composta da diversi blocchi di costruzione principali. Li esploreremo in dettaglio nelle lezioni successive, ma ecco una prima introduzione:

*   **Componenti (Components):** Come già detto, sono i mattoni fondamentali della UI. Controllano una porzione dello schermo. Ogni componente ha un template (HTML), una classe (TypeScript per la logica) e stili (CSS).
*   **Moduli (NgModules - `NgModule`):** Tradizionalmente, i moduli erano contenitori che raggruppavano componenti, direttive, pipe e servizi correlati. Aiutavano a organizzare l'applicazione in blocchi funzionali.
    *   *Nota importante:* Con l'introduzione e la crescente adozione dei **Standalone Components** (a partire da Angular 14 e diventati lo standard in Angular 17+), l'uso esplicito di `NgModule` per ogni feature è diventato opzionale e spesso non necessario per i nuovi progetti. I componenti standalone dichiarano le proprie dipendenze direttamente. Tuttavia, comprendere i `NgModule` è ancora utile per lavorare su codebase esistenti e per capire alcuni concetti di base dell'organizzazione e della DI.
*   **Standalone Components, Directives, and Pipes:** Sono elementi che non necessitano di essere dichiarati in un `NgModule`. Possono gestire le proprie dipendenze direttamente, semplificando la struttura dell'applicazione. Questo è l'approccio preferito per i nuovi sviluppi.
*   **Templates:** È l'HTML che definisce la vista di un componente. Angular estende l'HTML con una sintassi speciale (es. `{{ data }}`, `*ngIf`, `(click)`) per il data binding, la logica condizionale e la gestione degli eventi.
*   **Direttive (Directives):** Sono istruzioni nel DOM. Ci sono due tipi principali:
    *   **Direttive Strutturali:** Modificano la struttura del DOM aggiungendo, rimuovendo o manipolando elementi (es. `*ngIf`, `*ngFor`).
    *   **Direttive di Attributo:** Modificano l'aspetto o il comportamento di un elemento, componente o un'altra direttiva (es. `ngStyle`, `ngClass`).
*   **Servizi (Services):** Sono classi TypeScript che incapsulano logica di business, accesso ai dati o altre funzionalità riutilizzabili che non sono direttamente legate a una vista specifica. Esempi comuni includono servizi per recuperare dati da un server, logging, o gestione dello stato.
*   **Dependency Injection (DI):** È un design pattern e un meccanismo fondamentale in Angular. Permette alle classi (come i componenti) di ricevere le dipendenze (come i servizi) di cui hanno bisogno, invece di crearle direttamente. Questo promuove codice più modulare, testabile e manutenibile. Angular ha un suo sistema di DI gerarchico.
*   **Routing:** Il modulo `RouterModule` di Angular permette di definire la navigazione tra diverse viste (associate a componenti) all'interno della SPA.

**4. Panoramica dell'ecosistema Angular (CLI, Material Design, ecc.)**

Oltre al framework principale, l'ecosistema Angular include diversi strumenti e librerie importanti:

*   **Angular CLI (Command Line Interface):**
    *   Uno strumento a riga di comando essenziale (`ng`).
    *   Utilizzato per:
        *   Creare nuovi progetti (`ng new`).
        *   Generare componenti, servizi, moduli, direttive, pipe, ecc. (`ng generate component my-component` o `ng g c my-component`).
        *   Avviare il server di sviluppo locale con live-reloading (`ng serve`).
        *   Eseguire unit test e test end-to-end (`ng test`, `ng e2e`).
        *   Creare build ottimizzate per la produzione (`ng build`).
        *   Aggiornare l'applicazione e le sue dipendenze (`ng update`).
*   **Angular Material:**
    *   Una libreria di componenti UI di alta qualità, pronti all'uso, che implementano le linee guida del Material Design di Google.
    *   Include una vasta gamma di componenti come bottoni, form, tabelle, dialoghi, menu, ecc.
    *   Aiuta a costruire interfacce utente professionali e consistenti rapidamente.
*   **RxJS (Reactive Extensions for JavaScript):**
    *   Una libreria per la programmazione reattiva che utilizza gli **Observables**.
    *   Angular utilizza ampiamente RxJS per gestire operazioni asincrone, come le richieste HTTP, gli eventi del router e gli eventi DOM.
    *   Comprendere i concetti base di RxJS (Observables, Observers, Operators, Subscriptions) è molto importante per lavorare efficacemente con Angular.
*   **TypeScript:** Come già menzionato, è il linguaggio principale per lo sviluppo Angular. La sua integrazione è uno dei punti di forza del framework.
*   **Strumenti di Test:**
    *   **Jasmine:** Un framework di behavior-driven development (BDD) per scrivere test JavaScript. È il framework di testing predefinito per gli unit test in Angular.
    *   **Karma:** Un test runner che esegue i test Jasmine in diversi browser.
    *   **Protractor (deprecato per nuovi progetti):** Era il framework ufficiale per i test end-to-end. La community si sta spostando verso alternative come **Cypress**, **Playwright** o **WebDriverIO**. Angular CLI offre ora opzioni per integrare queste alternative.
*   **Angular Universal:** Per il **Server-Side Rendering (SSR)**, che può migliorare le performance percepite iniziali e l'ottimizzazione per i motori di ricerca (SEO).
*   **Supporto i18n (Internationalization):** Angular fornisce strumenti integrati per tradurre le applicazioni in diverse lingue.
*   **Angular DevTools:** Un'estensione per browser (Chrome ed Edge) che aiuta a ispezionare e debuggare le applicazioni Angular, visualizzando la struttura dei componenti e il loro stato.

---

Questa prima lezione dovrebbe averti dato una buona idea di cosa sia Angular e del perché sia una tecnologia così rilevante. Nelle prossime lezioni, inizieremo a mettere le mani in pasta configurando l'ambiente di sviluppo e creando la nostra prima applicazione Angular!

