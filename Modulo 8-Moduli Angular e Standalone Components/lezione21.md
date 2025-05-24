Ottimo! Procediamo con il **Modulo 8: Moduli Angular e Standalone Components**. In questo modulo, chiariremo il ruolo tradizionale degli `NgModule` e ci concentreremo sull'approccio moderno e preferito dei **Standalone Components**, che semplifica significativamente l'architettura delle applicazioni Angular.

---

### Modulo 8: Moduli Angular e Standalone Components

Storicamente, gli `NgModule` erano un pilastro fondamentale dell'architettura Angular, usati per organizzare l'applicazione in blocchi coesi di funzionalità. Tuttavia, a partire da Angular 14 e con un'adozione completa come default da Angular 17, i **Standalone Components, Directives e Pipes** hanno rivoluzionato questo approccio, offrendo un modo più semplice e meno verboso per costruire applicazioni.

**Lezione 21: `NgModule` in Profondità (se ancora rilevante o per progetti legacy)**

Anche se i nuovi progetti Angular tendono a usare i componenti standalone, è ancora importante capire cosa sono gli `NgModule` perché:

1.  Potresti lavorare su **progetti Angular esistenti** che li utilizzano ampiamente.
2.  Alcune **librerie di terze parti** potrebbero ancora essere distribuite come moduli che devi importare.
3.  Comprendere i moduli aiuta a capire alcuni **concetti di base** dell'organizzazione e della Dependency Injection che, sebbene semplificati, hanno radici nel sistema modulare.
4.  Per scenari molto grandi o complessi, una struttura modulare potrebbe ancora offrire benefici organizzativi.

**Cos'è un `NgModule`?**

Un `NgModule` è una classe TypeScript decorata con `@NgModule`. Il decoratore `@NgModule` accetta un oggetto di metadati che descrive come compilare il template di un componente e come creare un injector a runtime.

Un `NgModule` raggruppa logicamente componenti, direttive, pipe e servizi correlati e può importare funzionalità da altri moduli ed esportare le proprie funzionalità per essere usate da altri moduli.

**Proprietà Chiave dei Metadati di `@NgModule`:**

*   **`declarations: (any[] | undefined)`**:
    *   Un array di **componenti, direttive e pipe** che appartengono a *questo* `NgModule`.
    *   Questi elementi sono "privati" per il modulo, a meno che non vengano esportati.
    *   Ogni componente, direttiva o pipe può essere dichiarato in **un solo** `NgModule`.

*   **`imports: (any[] | undefined)`**:
    *   Un array di **altri `NgModule`** le cui funzionalità esportate sono necessarie ai componenti, direttive o pipe dichiarati in *questo* `NgModule`.
    *   Esempio: `CommonModule` (per direttive come `*ngIf`, `*ngFor`), `FormsModule` (per Template-Driven Forms), `ReactiveFormsModule`, `HttpClientModule`, moduli di routing, moduli di feature personalizzati.

*   **`exports: (any[] | undefined)`**:
    *   Un array di **componenti, direttive, pipe, o anche `NgModule`** dichiarati o importati in *questo* `NgModule` che devono essere resi disponibili ai moduli che importeranno *questo* `NgModule`.
    *   Se non esporti un componente, ad esempio, non potrai usarlo nel template di un componente che appartiene a un altro modulo (anche se quel modulo importa il modulo corrente).

*   **`providers: (Provider[] | undefined)`**:
    *   Un array di **provider di servizi**.
    *   I servizi forniti qui sono generalmente disponibili per tutti i componenti, direttive, pipe e servizi che appartengono a *questo* `NgModule` (e, per i moduli eager-loaded importati nel modulo radice, spesso diventano singleton a livello di applicazione, a meno che non si usi `providedIn: 'root'` nel servizio stesso, il che è preferibile per i servizi globali).
    *   Per i moduli **lazy-loaded**, i provider registrati qui creano un injector figlio, rendendo il servizio un singleton all'interno del contesto di quel modulo lazy-loaded.

*   **`bootstrap: (any[] | undefined)`**:
    *   Usato **solo nel modulo radice** (tipicamente `AppModule`).
    *   Specifica il componente (o i componenti) radice che Angular deve caricare all'avvio dell'applicazione per avviare il processo di bootstrap. Solitamente è `AppComponent`.
    *   *Questa proprietà non è usata con i componenti standalone, dove il bootstrap avviene tramite la funzione `bootstrapApplication` che prende direttamente il componente radice standalone.*

**Tipi Comuni di `NgModule`:**

Anche se con i componenti standalone questa categorizzazione è meno rigida, storicamente si parlava di:

1.  **Root Module (Modulo Radice - es. `AppModule`):**
    *   Ogni applicazione Angular basata su moduli ha almeno un modulo radice, convenzionalmente chiamato `AppModule`.
    *   È il modulo che avvia l'applicazione (tramite la sua proprietà `bootstrap`).
    *   Importa moduli essenziali come `BrowserModule` (che fornisce servizi essenziali per l'esecuzione nel browser e riesporta `CommonModule`) e il modulo di routing principale.

2.  **Feature Modules (Moduli di Funzionalità):**
    *   Organizzano il codice relativo a una specifica funzionalità o dominio di business (es. `OrdersModule`, `CustomersModule`, `ProductsModule`).
    *   Importano `CommonModule` (invece di `BrowserModule`), `FormsModule`/`ReactiveFormsModule` se necessario, e moduli condivisi.
    *   Dichiarano i loro componenti, direttive e pipe.
    *   Possono essere caricati "eagerly" (all'avvio) o "lazily" (su richiesta).

3.  **Shared Modules (Moduli Condivisi):**
    *   Raggruppano componenti, direttive e pipe riutilizzabili che vengono usati in più feature module.
    *   Un `SharedModule` tipicamente importa `CommonModule` e altri moduli UI (es. Angular Material), e poi *esporta* i componenti, direttive e pipe che vuole condividere.
    *   I feature module poi importano questo `SharedModule`.
    *   **Importante:** Un `SharedModule` non dovrebbe avere `providers` per servizi che si vogliono singleton a livello di applicazione (a meno che non sia un modulo fornito a un modulo lazy-loaded per creare un servizio scoped). I servizi singleton globali dovrebbero usare `providedIn: 'root'` o essere forniti nel `CoreModule` o `AppModule`.

4.  **Core Module (Modulo Core - Convenzione):**
    *   Una convenzione per un modulo che viene importato **solo una volta** dall'`AppModule`.
    *   Usato per fornire servizi singleton a livello di applicazione (quelli che non usano `providedIn: 'root'`) e per componenti di layout usati una sola volta (es. navbar, footer, layout principale dell'app).
    *   L'idea è di evitare che i feature module importino il `CoreModule`.
    *   `CoreModule` spesso riesporta `CommonModule` e altri moduli fondamentali se necessario.

5.  **Routing Modules (Moduli di Routing):**
    *   Moduli dedicati alla configurazione del router (es. `AppRoutingModule`, `CustomersRoutingModule`).
    *   Importano `RouterModule.forRoot(routes)` (nel modulo radice) o `RouterModule.forChild(routes)` (nei feature module) e poi esportano `RouterModule`.

**Esempio di Struttura con `AppModule` (Tradizionale):**

```typescript
// src/app/app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser'; // Importa BrowserModule (che include CommonModule)
import { HttpClientModule } from '@angular/common/http'; // Per HttpClient

import { AppRoutingModule } from './app-routing.module'; // Modulo di routing principale
import { AppComponent } from './app.component';
import { HomeComponent } from './home/home.component';     // Componente eager-loaded
import { AboutComponent } from './about/about.component';    // Componente eager-loaded
import { NotFoundComponent } from './not-found/not-found.component';
// CoreModule (opzionale, per convenzione)
// import { CoreModule } from './core/core.module';
// SharedModule (opzionale, per convenzione)
// import { SharedModule } from './shared/shared.module';

@NgModule({
  declarations: [ // Componenti, direttive, pipe che appartengono a questo modulo
    AppComponent,
    HomeComponent,
    AboutComponent,
    NotFoundComponent
    // Altri componenti che non sono standalone e non appartengono a feature module
  ],
  imports: [ // Altri moduli le cui funzionalità esportate sono usate qui
    BrowserModule,         // Essenziale per app browser, importato solo in AppModule
    HttpClientModule,      // Per fare richieste HTTP
    AppRoutingModule,      // Gestisce il routing principale (che può caricare feature module pigramente)
    // CoreModule,            // Convenzione: importato solo qui
    // SharedModule,          // Convenzione: importato qui e nei feature modules se necessario
    // Altri moduli di feature caricati "eagerly" (non pigramente)
  ],
  providers: [ // Servizi forniti a livello di root (se non usano providedIn: 'root')
    // Esempio: MyGlobalService (se non ha providedIn: 'root')
  ],
  bootstrap: [AppComponent] // Il componente radice per avviare l'applicazione
})
export class AppModule { }
```

**Impatto dei Componenti Standalone sugli `NgModule`:**

Con l'introduzione dei componenti standalone, la necessità di creare `NgModule` per ogni piccola feature o per raggruppare componenti è drasticamente diminuita.

*   I componenti standalone dichiarano le proprie dipendenze (altri componenti standalone, direttive, pipe, o anche `NgModule` esistenti) direttamente nel loro array `imports` nel decoratore `@Component`.
*   Il routing può essere configurato per caricare pigramente componenti standalone o array di rotte che puntano a componenti standalone (`loadComponent`, `loadChildren` con array di `Route`).
*   Il bootstrap dell'applicazione avviene con `bootstrapApplication(RootStandaloneComponent, { providers: [...] })` invece che tramite `AppModule` e la sua proprietà `bootstrap`.

**Quando potresti ancora usare `NgModule` oggi:**

*   **Organizzazione di Librerie:** Se stai creando una libreria Angular da condividere, potresti ancora volerla pacchettizzare come un `NgModule` per facilitarne l'importazione da parte di chi la usa (anche se le librerie possono anche esportare componenti standalone).
*   **Progetti Molto Grandi con Team Distribuiti:** Per progetti estremamente grandi, la struttura offerta dai moduli potrebbe ancora aiutare a definire confini chiari tra diverse parti dell'applicazione sviluppate da team diversi.
*   **Integrazione con Moduli di Terze Parti:** Molte librerie UI o di utilità sono ancora distribuite come `NgModule` che dovrai importare (o nel tuo `AppModule` legacy, o negli `imports` di un componente standalone, o nella configurazione `providers` di `bootstrapApplication` se forniscono servizi globali).
*   **Gestione Scoped dei Provider:** Se hai bisogno di un'istanza di servizio specifica per una particolare sezione (lazy-loaded) dell'applicazione e non vuoi che sia un singleton globale, fornire quel servizio nei `providers` di un `NgModule` caricato pigramente è ancora un modo per ottenere questo "scoping".

---

Questa lezione ha fornito una panoramica degli `NgModule`. Anche se la tendenza è verso i componenti standalone, capire i moduli è ancora utile per il contesto storico e per interagire con codice o librerie esistenti.

Nella prossima lezione, **Lezione 22: Standalone Components, Directives, e Pipes**, ci tufferemo completamente nell'approccio moderno e vedremo come semplificano lo sviluppo Angular.
