Eccellente! Proseguiamo con la **Lezione 15: Lazy Loading dei Moduli**. Questa è una tecnica fondamentale per ottimizzare le performance di caricamento iniziale delle applicazioni Angular più grandi.

---

### Modulo 5: Routing e Navigazione

**Lezione 15: Lazy Loading dei Moduli (e dei Componenti Standalone con Route Figlie)**

Per impostazione predefinita, quando un'applicazione Angular viene caricata, tutti i suoi moduli (o tutti i componenti importati direttamente se l'app è interamente standalone) vengono caricati e compilati, anche se l'utente non visita mai certe sezioni dell'applicazione. Per applicazioni di grandi dimensioni, questo può portare a un tempo di caricamento iniziale (Initial Load Time) più lungo e a un bundle JavaScript più grande.

Il **Lazy Loading** (Caricamento Pigro) è una strategia che permette di caricare parti dell'applicazione (tipicamente feature modules o gruppi di rotte con componenti standalone) solo quando l'utente navigaAss verso una rotta associata a quella parte.

**Vantaggi del Lazy Loading:**

*   **Migliolutamente! Concludiamo il Modulo 5 con la **Lezione 15oramento del Tempo di Caricamento Iniziale:** L'applicazione principale carica solo ciò che è strettamente necessario per: Lazy Loading dei Moduli**. Questa tecnica è fondamentale per ottimizzare le performance di applicazioni Angular di grandi dimensioni.

---

### Modulo 5: Routing e Navigazione

**Lezione 15: Lazy Loading dei Moduli (e Componenti Standalone con Lazy Loading)**

Per impostazione predefinita, quando un'applicazione Angular viene la vista iniziale, risultando in un bundle più piccolo e un avvio più rapido.
*   **Bundle JavaScript più Piccoli per la Pagina Iniziale:** Il codice per le funzionalità "lazy" viene separato in chunk caricata, Angular CLI raggruppa tutto il codice JavaScript dell'applicazione (inclusi tutti i moduli e i componenti) in pochi file (bundles). Per applicazioni piccole, questo va bene. Tuttavia, man mano che l'applicazione cresce, la dimensione di questi bundle iniziali può diventare molto grande, portando a tempi di caricamento iniziali più lunghi e a una peggiore esperienza utente.

Il **Lazy Loading** (Caricamento Pigro) è una tecnica che permette di caricare parti della tua applicazione (tradizionalmente moduli, ma ora anche singoli componenti o gruppi di rot (file JavaScript separati) che vengono scaricati dal browser solo su richiesta.
*   **Migliore Esperienza Utente:** Gli utenti percepiscono l'applicazione come più veloce perché la parte visibile iniziale si carica rapidamente.

**1. Come Funziona il Lazy Loading**

Il lazy loading in Angular si basa sulla configurazione del router. Invece di associare direttamente un `component` a una rotta, associ un meccanismo per caricare un modulo o un insieme di rotte figlie quando quella rotta viene attivata.

**a. Lazy Loading con Feature Modules (approccio tradizionale, ancora valido):**

Se stai organizzando la tua applicazione con `NgModule` per le diverse funzionalità (feature modules), puoi caricare questi moduli in modo pigro.

1.  **Creare un Feature Module:**
    Supponiamo di avere una sezione "customers" nella nostra applicazione.
    ```bash
    ng generate module customers --route customers --module app.module
    # Questo crea un CustomersModule, un CustomersRoutingModule, e un CustomersComponent
    # e configura una rotta 'customers' in AppRoutingModule per caricare pigramente CustomersModule.te) **solo quando sono effettivamente necessarie**, tipicamente quando l'utente naviga verso una specifica rotta associata a quella parte dell'applicazione.

**Vantaggi del Lazy Loading:**

*   **Miglioramento del Tempo di Caricamento Iniziale:** Il bundle iniziale dell'applicazione è più piccolo perché non contiene il codice delle sezioni caricate pigramente. L'applicazione si avvia più velocemente.
*   **Risparmio di Risorse:** Vengono caricate solo le funzionalità che l'utente utilizza, risparmiando banda e memoria.
*   **Migliore Organizzazione del Codice:** Incoraggia a suddividere l'applicazione in feature più piccole e gestibili.

**1. Migliorare le performance caricando i moduli/componenti solo quando necessario**

Il router di Angular è il meccanismo chiave che abilita il lazy loading. Invece di associare direttamente un componente a una rotta nella configurazione principale, si indica al router di caricare un intero modulo o un set di rotte figlio quando un certo percorso URL viene visitato.

**2. Configurare il Lazy Loading per Feature Modules (Approccio Tradizionale)**

Questo era l'approccio standard prima della diffusione capillare dei componenti standalone. Pre
    # Per le versioni più recenti di Angular CLI, potrebbe essere necessario specificare --routing per CustomersRoutingModule.
    # ng g m customers --routing
    # ng g c customers --module=customers
    ```

2.  **Configurare il Routing nel Feature Module (`CustomersRoutingModule`):**
    Questo modulo definirà le rotte *interne* alla sezione "customers". Queste rotte saranno relative al percorso base `/customers`.
    ```typescript
    // src/app/customers/customers-routing.module.ts
    import { NgModule } from '@angular/core';
    import { RouterModule, Routes } from '@angular/router';
    import { CustomerListComponent } from './customer-list/customer-list.component'; // Crea questo componente
    import { CustomerDetailComponent } from './customer-detail/customer-detail.component'; // Crea questo componente
    import { CustomersComponent } from './customers.component'; // Componente principale del modulo (opzionale)

    const routes: Routes = [
      {
        path: '', // Relativo a '/customers'
        component: CustomersComponent, // Un componente contenitore per la sezione clienti (opzionale)
        children: [
          { path: '', component: CustomerListComponent }, // Default per /customers
          { path: 'detail/:id', component: CustomerDetailComponent } // /customers/detail/:id
        ]
      }
      // Oppure, se CustomersComponent non è un contenitore:
      // { path: '', component: CustomerListComponent },
      // { path: 'detail/:id', component: CustomerDetailComponent }
    ];

    @NgModule({
      imports: [RouterModule.forChild(routes)], // Usa forChild() per i feature module
      exports: [RouterModulevede la creazione di moduli separati per le diverse funzionalità dell'applicazione (Feature Modules).

*   **a. Creare un Feature Module:**
    Supponiamo di avere una sezione "customers" nella nostra applicazione.
    ```bash
    ng generate module customers --route customers --module app.module
    # Questo comando (se usato con --module app.module) tenterebbe di configurare il lazy loading
    # in app.module.ts. Tuttavia, con l'approccio moderno standalone per l'app principale,
    # modificheremo app.routes.ts direttamente.
    # Generiamo il modulo e il componente manualmente per chiarezza:

    ng g m customers # Crea il modulo customers
    ng g c customers/customers-list --module customers # Crea un componente dentro il modulo customers
    # Crea anche un customers-routing.module.ts
    ng g m customers/customers-routing --flat --module customers
    ```

*   **b. Definire le Route all'interno del Feature Module (`CustomersRoutingModule`):**
    ```typescript
    // src/app/customers/customers-routing.module.ts
    import { NgModule } from '@angular/core';
    import { RouterModule, Routes } from '@angular/router';
    import { CustomersListComponent } from './customers-list/customers-list.component';

    const routes: Routes = [
      {
        path: '', // Path relativo al path del modulo padre (es. 'customers')
        component: CustomersListComponent
        // Altre rotte specifiche per la feature 'customers' potrebbero essere qui
        // es. { path: ':id', component: CustomerDetailComponent }
      }
    ];

    @NgModule({
      imports: [RouterModule.forChild(routes)], // Usa forChild() per i feature modules
      exports: [RouterModule]
    })
    export class CustomersRoutingModule { }
    ```

*   **c. Configurare il Lazy Loading nel Router Principale (es. `app.routes.ts`):**
    ]
    })
    export class CustomersRoutingModule { }
    ```
    *   `RouterModule.forChild(routes)`: È fondamentale usare `forChild()` nei moduli di routing delle feature. `forRoot()` viene usato solo una volta nel modulo radice.

3.  **Configurare il Lazy Loading nel Router Principale (`AppRoutingModule` o `app.routes.ts`):**
    Nel router principale, invece di specificare un `component`, usi la proprietà `loadChildren` (per i moduli) con una funzione che usa l'import dinamico di ES2020 (`import()`).
    ```typescript
    // src/app/app-routing.module.ts (per app basate su NgModule)
    // ...
    const routes: Routes = [
      // ... (altre rotte eager)
      {
        path: 'customers', // Il percorso che attiverà il lazy loading
        loadChildren: () => import('./customers/customers.module').then(m => m.CustomersModule)
      },
      // ...
    ];
    // ...
    ```
    *   `loadChildren: () => import('./customers/customers.module').then(m => m.CustomersModule)`:
        *   La funzione `import('./customers/customers.module')` è un import dinamico. Dice al bundler (come Webpack) di creare un chunk separato per `CustomersModule`.
        *   Quando l'utente naviga a `/customers`, il browser scaricherà questo chunk, e poi il router caricherà `CustomersModule`.
        *   `.then(m => m.CustomersModule)` assicura che venga restituita la classe del modulo.

**b. Lazy Loading con Componenti Standalone e Route Figlie (da Angular 14+, più moderno):**

Se stai costruendo la tua applicazione con componenti standalone, puoi caricare pigramente un insieme di rotte associate a componenti standalone senza la necessità di un `NgModule` intermedio.

1.  **Definire le Route Figlie Standalone:**
    Crea un file separato per le rotte della tua feature (es. `products.routes.ts`).
    ```typescript
    // src/app/products/products.routes.ts
    import { Routes } from '@angular/router';
    import { ProductListComponent } from './product-list/product-list.component'; // Componente standalone
    import { ProductDetailComponent } from './product-detail/product-detail.component'; // Componente standalone

    // Assicurati che ProductListComponent e ProductDetailComponent siano standalone e importino ciò che serve.

    export const PRODUCT_ROUTES: Routes = [ // Esporta l'array di rotte
      { path: '', component: ProductListComponent },
      { pathNel file di routing principale, invece di specificare un `component`, usi la proprietà `loadChildren` con una funzione di import dinamico.

    ```typescript
    // src/app/app.routes.ts
    import { Routes } from '@angular/router';
    // ... (altre importazioni di componenti per eager loading)

    export const routes: Routes = [
      { path: '', redirectTo: '/home', pathMatch: 'full' },
      { path: 'home', component: HomeComponent }, // Eager loaded
      { path: 'about', component: AboutComponent }, // Eager loaded
      {
        path: 'customers', // Quando si naviga a /customers...
        loadChildren: () => import('./customers/customers.module').then(m => m.CustomersModule)
        // ...Angular caricherà CustomersModule e le sue rotte.
      },
      // ... (altre rotte, inclusa la wildcard)
      { path: '**', component: NotFoundComponent }
    ];
    ```
    *   `loadChildren: () => import('./customers/customers.module').then(m => m.CustomersModule)`:
        *   Usa la sintassi di `import()` dinamico di JavaScript/TypeScript.
        *   Quando l'utente naviga a un URL che inizia con `/customers`, Angular caricherà il file del modulo `customers.module.ts` (e le sue dipendenze) in un chunk separato.
        *   Una volta caricato, il router userà le rotte definite all'interno di `CustomersModule` (tramite `CustomersRoutingModule` e `RouterModule.forChild()`) per gestire la navigazione all'interno della sezione `/customers`.

*   **d. Aggiornare il Feature Module (`CustomersModule`):**
    Assicurati che `CustomersModule` importi `CustomersRoutingModule` e dichiari/importi i componenti necessari.
    ```typescript
    // src/app/customers/customers.module.ts
    import { NgModule } from '@angular/core';
    import { CommonModule } from '@angular/common'; // Importa CommonModule per direttive comuni
    import { CustomersRoutingModule } from './customers-routing.module';
    import { CustomersListComponent } from './customers-list/customers-list.component';

    @NgModule({
      declarations: [
        CustomersListComponent
        // Altri componenti specifici di questa feature
      ],
      imports: [
        CommonModule,
        CustomersRoutingModule // Importa le rotte della feature
      ]
    })
    export class CustomersModule { }
    ```

**3. Configurare il Lazy Loading con Componenti Standalone e Route Figlie (Approccio Moderno)**

Con i componenti standalone, l'approccio al lazy loading è ancora più flessibile. Puoi caricare pigramente:

*   Un singolo componente standalone.
*   Un array di rotte (che a loro volta possono puntare a componenti standalone).

Questo elimina la necessità di creare `NgModule` solo per il routing o per raggruppare componenti per il lazy loading, se non strettamente necessario per altri motivi.

*   **a. Lazy Loading di un Singolo Componente Standalone:**
    Se una rotta deve solo caricare un singolo componente standalone e quel componente non ha rotte figlie complesse, puoi farlo direttamente.

    ```typescript
    // src/app/app.routes.ts
    import { Routes } from '@angular/router';
    // ...

    export const routes: Routes = [
      // ...
      {
        path: 'profile', // Naviga a /profile
        loadComponent: () =>
          import('./profile/profile.component').then(m => m.ProfileComponent)
        // Assicurati che ProfileComponent sia standalone e gestisca le sue dipendenze (imports)
      },
      // ...
    ];
    ```
    *   `loadComponent`: Usato al posto di `component` per il lazy loading di un singolo componente.
    *   `ProfileComponent` (in `profile.component.ts`) dovrebbe essere un componente standalone:
        ```typescript
        // src/app/profile/profile.component.ts
        import { Component } from '@angular/core';
        import { CommonModule } from '@: 'detail/:id', component: ProductDetailComponent }
    ];
    ```

2.  **Configurare il Lazy Loading nel Router Principale (`app.routes.ts`):**
    Usa la proprietà `loadChildren` nel router principale, ma questa volta la funzione `import()` punterà al file che esporta l'array di `Routes`.
    ```typescript
    // src/app/app.routes.ts (per app standalone)
    import { Routes } from '@angular/router';
    import { HomeComponent } from './home/home.component';
    // ... (altre importazioni di componenti eager)

    export const routes: Routes = [
      { path: '', component: HomeComponent },
      // ... (altre rotte eager)
      {
        path: 'products', // Il percorso che attiverà il lazy loading
        loadChildren: () => import('./products/products.routes').then(mod => mod.PRODUCT_ROUTES)
        // Al posto di 'mod.PRODUCT_ROUTES', a volte si vede anche solo l'import se il file .routes.ts
        // esporta di default l'array di Routes.
        // Angular CLI spesso genera:
        // loadChildren: () => import('./products/products.routes')
        // se il file products.routes.ts ha `export default routesArray;`
      },
      // ... (rotta wildcard)
    ];
    ```
    *   `loadChildren: () => import('./products/products.routes').then(mod => mod.PRODUCT_ROUTES)`:
        *   L'import dinamico carica il file `products.routes.ts`.
        *   `.then(mod => mod.PRODUCT_ROUTES)` si aspetta che il modulo JavaScript risultante esporti una costante chiamata `PRODUCT_ROUTES` (o un default) che sia un array di `Routes`.

    **Alternativa più concisa se il file delle rotte esporta di default:**
    Se `products.routes.ts` fosse così:
    ```typescript
    // src/app/products/products.routes.ts
    // ... (importazioni componenti)
    // const routes: Routes = [ /* ... */ ];
    // export default routes; // Esportazione di default
    ```
    Allora nel router principale potresti scrivere:
    ```typescript
    // src/app/app.routes.ts
    // ...
    {
      path: 'products',
      loadChildren: () => import('./products/products.routes')
    },
    // ...
    ```

**2. Migliorare le performance caricando i moduli solo quando necessario**

*   **Verifica del Lazy Loading:**
    Quando esegui `ng serve` o `ng build`, apri gli strumenti per sviluppatori del browser (Developer Tools) e vai alla scheda "Network" (Rete).
    1.  Carica l'applicazione iniziale. Dovresti vedere i chunk JavaScript principali.
    2.  Naviga verso una rotta che è configurata per il lazy loading (es. `/customers` o `/products`).
    3.  Dovresti vedere una nuova richiesta di rete per un file JavaScript aggiuntivo (il chunk per la feature lazy-loaded). Questo conferma che il lazy loading sta funzionando.

*   **Considerazioni:**
    *   **Modulo Core e Modulo Shared:** Quando usi i moduli, è comune avere un `CoreModule` (per servizi singleton e componenti usati una sola volta come layout e navbar) e uno o più `SharedModule` (per componenti, direttive e pipe riutilizzabili usati in più feature module).
        *   Il `CoreModule` viene importato solo una volta nell'`AppModule`.
        *   Il `SharedModule` viene importato dai feature module che ne hanno bisogno. **Non fornire servizi in un `SharedModule`** che vuoi siano singleton, perché ogni feature module che lo importa creerebbe la sua istanza (a meno che non usi `providedIn: 'root'` nel servizio stesso).
    *   Con i componenti standalone, la necessità di `SharedModule` per dichiarare/esportare componenti diminuisce, poiché i componenti standalone importano direttamente le loro dipendenze. Tuttavia, potresti ancora avere moduli "shared" per raggruppare logicamente servizi o configurazioni comuni, oppure semplicemente importare i componenti/direttive/pipe standalone direttamente dove servono.

**3. Preloading Strategies (Strategie di Precaricamento)**

Il lazy loading migliora il tempo di caricamento iniziale, ma introduce una piccola latenza quando l'utente naviga per la prima volta a una sezione lazy-loaded (perché il chunk deve essere scaricato).

Per mitigare questo, Angular offre **strategie di precaricamento**:

*   **NoPreloading (Default):** Nessun precaricamento. I moduli lazy vengono caricati solo su richiesta.
*   **PreloadAllModules:** Dopo che l'applicazione iniziale è stata caricata, Angular inizia a precaricare *tutti* i moduli lazy-loaded in background. Questo può migliorare la velocità di navigazione verso le sezioni lazy, ma potrebbe consumare più banda e risorse se l'utente non visita mai quelle sezioni.
*   **Custom Preloading Strategy:** Puoi creare una tua logica di precaricamento personalizzata (es. precaricare solo alcuni moduli specifici, o precaricare moduli in base a determinate condizioni o dopo un certo ritardo).

**Come configurare una strategia di precaricamento:**

*   **Per Applicazioni Standalone (`app.config.ts`):**
    Passa un'opzione a `provideRouter`.
    ```typescript
    // src/app/app.config.ts
    import { ApplicationConfig } from '@angular/core';
    import { PreloadAllModules, provideRouter, withPreloading } from '@angular/router'; // Importa withPreloading e PreloadAllModules
    import { routes } from './app.routes';

    export const appConfig: ApplicationConfig = {
      providers: [
        provideRouter(routes,
          withPreloading(PreloadAllModules) // Configura la strategia di precaricamento
        )
      ]
    };
    ```

*   **Per Applicazioni Basate su NgModule (`AppRoutingModule`):**
    Passa un secondo argomento a `RouterModule.forRoot()`.
    ```typescript
    // src/app/app-routing.module.ts
    import { NgModule } from '@angular/core';
    import { RouterModule, Routes, PreloadAllModules } from '@angular/router'; // Importa PreloadAllModules
    // ... (definizione routes)

    @NgModule({
      imports: [RouterModule.forRoot(
        routes,
        { preloadingStrategy: PreloadAllModules } // Configura la strategia
      )],
      exports: [RouterModule]
    })
    export class AppRoutingModule { }
    ```

**Creare una Strategia di Precaricamento Personalizzata:**

1.  Crea un servizio che implementa l'interfaccia `PreloadingStrategy`.
2.  Il metodo `preload(route: Route, load: () => Observable<any>): Observable<any>` deve restituire langular/common'; // Esempio di dipendenza

        @Component({
          selector: 'app-profile',
          standalone: true,
          imports: [CommonModule],
          template: `<p>Profilo Utente Caricato Pigramente!</p>`
        })
        export class ProfileComponent {}
        ```

*   **b. Lazy Loading di un Gruppo di Route Figlie (con Componenti Standalone):**
    Questo è l'approccio più comune quando una sezione della tua applicazione ha diverse sotto-rotte, tutte da caricare pigramente insieme.

    1.  **Creare un file per le rotte figlie:**
        ```typescript
        // src/app/dashboard/dashboard.routes.ts
        import { Routes } from '@angular/router';
        import { DashboardHomeComponent } from './dashboard-home/dashboard-home.component'; // Standalone
        import { SettingsComponent } from './settings/settings.component';           // Standalone
        // import { authGuard } from '../guards/auth.guard'; // Esempio di guard

        export const DASHBOARD_ROUTES: Routes = [
          {
            path: '', // Path di default per /dashboard
            component: DashboardHomeComponent,
            // canActivate: [authGuard] // Puoi applicare guard anche qui
          },
          {
            path: 'settings', // /dashboard/settings
            component: SettingsComponent
          }
          // Altre rotte figlie del dashboard
        ];
        ```
        *   Assicurati che `DashboardHomeComponent` e `SettingsComponent` siano componenti standalone.

    2.  **Configurare `loadChildren` nel Router Principale per caricare queste rotte:**
        ```typescript
        // src/app/app.routes.ts
        import { Routes } from '@angular/router';
        // ...

        export const routes: Routes = [
          // ...
          {
            path: 'dashboard',
            loadChildren: () =>
              import('./dashboard/dashboard.routes').then(mod => mod.DASHBOARD_ROUTES)
            // Qui non importi un NgModule, ma direttamente l'array di Routes
          },
          // ...
        ];
        ```
        Quando si naviga a `/dashboard`, Angular caricherà il file `dashboard.routes.ts` e registrerà le `DASHBOARD_ROUTES` sotto il prefisso `/dashboard`.

*   **c. Default Export per le Route Figlie (Opzionale ma Conveniente):**
    Puoi usare un `default export` nel tuo file di rotte figlie per semplificare l'importazione in `app.routes.ts`.

    ```typescript
    // src/app/orders/orders.routes.ts
    import { Routes } from '@angular/router';
    import { OrderListComponent } from './order-list/order-list.component';
    import { OrderDetailComponent } from './order-detail/order-detail.component';

    // Usa 'export default'
    export default [
      { path: '', component: OrderListComponent },
      { path: ':id', component: OrderDetailComponent }
    ] as Routes; // Type assertion per chiarezza
    ```

    ```typescript
    // src/app/app.routes.ts
    import { Routes } from '@angular/router';
    // ...

    export const routes: Routes = [
      // ...
      {
        path: 'orders',
        loadChildren: () => import('./orders/orders.routes')
        // Non c'è bisogno di .then(mod => mod.ROUTES_ARRAY_NAME) se usi default export
      },
      // ...
    ];
    ```

**Verificare il Lazy Loading:**

*   **Network Tab dei DevTools del Browser:** Quando navighi per la prima volta verso una rotta caricata pigramente, dovresti vedere un nuovo file JavaScript (chunk) scaricato nella scheda Network.
*   **Output di `ng build`:** Quando compili per la produzione, vedrai che Angular CLI crea chunk separati per le sezioni caricate pigramente.

**Considerazioni Aggiuntive:**

*   **Preloading Strategy:**
    *   Per impostazione predefinita, i moduli/componenti pigri vengono caricati solo quando l'utente naviga attivamente alla loro rotta.
    *   Angular permette di configurare una **strategia di precaricamento**. Ad esempio, con `PreloadAllModules`, Angular carica il bundle iniziale e poi, in background, precarica tutti gli altri moduli pigri dopo che l'app è stata avviata. Questo può migliorare la velocità di navigazione verso le sezioni pigre, poiché potrebbero essere già state caricate quando l'utente ci clicca.
    *   Puoi configurarlo quando fornisci il router:
        ```typescript
        // app.config.ts
        // import { PreloadAllModules, provideRouter, withPreloading } from '@angular/router';
        // providers: [
        //   provideRouter(routes, withPreloading(PreloadAllModules))
        // ]
        ```
    *   Puoi anche creare strategie di precaricamento personalizzate.

*   **Guard e Resolver con Lazy Loading:**
    I guard (`CanActivate`, `CanMatch`, ecc.) e i resolver possono essere applicati anche a rotte che usano `loadChildren` o `loadComponent`.
    *   `CanMatch` è particolarmente utile qui, perché può impedire il caricamento del chunk JavaScript se le condizioni non sono soddisfatte, risparmiando banda. `CanActivate` viene eseguito *dopo* che il chunk è stato caricato.

---

Il Lazy Loading è una tecnica di ottimizzazione cruciale. L'evoluzione di Angular verso i componenti standalone ha reso il lazy loading ancora più granulare e facile da implementare, permettendo di caricare pigramente non solo moduli ma anche singoli componenti o insiemi di rotte figlie definite in semplici array.

Questo conclude il Modulo 5 sul Routing e la Navigazione! Abbiamo coperto molti aspetti, dalla configurazione base ai parametri, ai guard, e ora al lazy loading.
