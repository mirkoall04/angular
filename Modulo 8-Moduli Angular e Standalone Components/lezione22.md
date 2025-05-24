Eccellente! Passiamo ora alla **Lezione 22: Standalone Components, Directives, e Pipes**. Questo è l'approccio moderno e preferito per lo sviluppo di applicazioni Angular, introdotto per semplificare l'architettura e ridurre il boilerplate associato agli `NgModule`.

---

### Modulo 8: Moduli Angular e Standalone Components

**Lezione 22: Standalone Components, Directives, e Pipes**

A partire da Angular 14 e diventati lo standard con Angular 17, i **componenti, le direttive e i pipe standalone** offrono un modo per costruire parti della tua UI senza la necessità di dichiararli in un `NgModule`. Possono gestire le proprie dipendenze direttamente, rendendo l'architettura dell'applicazione più semplice e modulare a un livello più granulare.

**1. Introduzione ai Componenti, Direttive e Pipe Standalone**

*   **Cosa Significa "Standalone"?**
    Un componente, direttiva o pipe standalone è **auto-contenuto** per quanto riguarda le sue dipendenze di template. Non ha bisogno di essere dichiarato in un `NgModule` per essere utilizzato.
    Invece, dichiara esplicitamente le sue dipendenze (altri componenti, direttive, pipe, o anche `NgModule`) direttamente nei metadati del suo decoratore (`@Component`, `@Directive`, o `@Pipe`).

*   **La Proprietà `standalone: true`:**
    Per rendere un componente, direttiva o pipe standalone, si imposta la proprietà `standalone: true` nel suo decoratore. Angular CLI genera nuovi componenti, direttive e pipe come standalone per default da Angular 17.

    ```typescript
    // Componente Standalone
    import { Component } from '@angular/core';
    import { CommonModule } from '@angular/common'; // Esempio di dipendenza
    import { MyStandalonePipe } from './my-standalone.pipe';
    import { MyStandaloneDirective } from './my-standalone.directive';

    @Component({
      selector: 'app-mio-componente-standalone',
      standalone: true, // <-- Lo rende standalone!
      imports: [
        CommonModule, // Per *ngIf, *ngFor, ecc.
        MyStandalonePipe,
        MyStandaloneDirective,
        // Altri componenti/direttive/pipe standalone o NgModule
      ],
      template: `
        <div *ngIf="isVisible" appMyStandaloneDirective>
          <p>{{ messaggio | myStandalonePipe }}</p>
        </div>
        <button (click)="toggle()">Toggle</button>
      `
    })
    export class MioComponenteStandalone {
      isVisible = true;
      messaggio = 'Hello Standalone World!';
      toggle() { this.isVisible = !this.isVisible; }
    }

    // Direttiva Standalone
    import { Directive, ElementRef } from '@angular/core';

    @Directive({
      selector: '[appMyStandaloneDirective]',
      standalone: true, // <-- Lo rende standalone!
    })
    export class MyStandaloneDirective {
      constructor(private el: ElementRef) {
        el.nativeElement.style.border = '2px solid blue';
      }
    }

    // Pipe Standalone
    import { Pipe, PipeTransform } from '@angular/core';

    @Pipe({
      name: 'myStandalonePipe',
      standalone: true, // <-- Lo rende standalone!
    })
    export class MyStandalonePipe implements PipeTransform {
      transform(value: string): string {
        return `*** ${value} ***`;
      }
    }
    ```

**2. Come Creare e Utilizzare Componenti Standalone**

*   **Creazione con Angular CLI:**
    Il CLI genera componenti standalone per default:
    ```bash
    ng generate component nome-componente
    # o ng g c nome-componente
    ```
    Questo creerà un componente con `standalone: true` e un array `imports` (spesso con `CommonModule` già incluso).

*   **Gestire le Dipendenze con la Proprietà `imports`:**
    La proprietà chiave per i componenti standalone è `imports` nel decoratore `@Component`. Questo array specifica tutto ciò di cui il template del componente ha bisogno per funzionare correttamente:
    *   **`CommonModule`**: Se usi direttive strutturali comuni (`*ngIf`, `*ngFor`), pipe comuni (`DatePipe`, `AsyncPipe`, ecc.).
    *   **Altri Componenti Standalone**: Se il tuo componente usa altri componenti standalone nel suo template, devi importarli.
    *   **Direttive Standalone**: Se usi direttive standalone.
    *   **Pipe Standalone**: Se usi pipe standalone.
    *   **`NgModule` Esistenti**: Puoi ancora importare `NgModule` esistenti se forniscono funzionalità che ti servono (es. `FormsModule`, `ReactiveFormsModule`, moduli da librerie di terze parti come Angular Material).

    Esempio:
    ```typescript
    // parent.component.ts
    import { Component } from '@angular/core';
    import { CommonModule } from '@angular/common';
    import { ChildStandaloneComponent } from './child-standalone/child-standalone.component'; // Un altro componente standalone
    import { FormsModule } from '@angular/forms'; // Un NgModule

    @Component({
      selector: 'app-parent',
      standalone: true,
      imports: [
        CommonModule,
        FormsModule, // Importa un NgModule
        ChildStandaloneComponent // Importa un altro componente standalone
      ],
      template: `
        <div *ngIf="showChild">
          <app-child-standalone [data]="parentData"></app-child-standalone>
        </div>
        <input [(ngModel)]="parentData">
      `
    })
    export class ParentComponent {
      showChild = true;
      parentData = 'Dati dal genitore';
    }
    ```

**3. Vantaggi e Impatto sull'Architettura dell'Applicazione**

L'introduzione dei componenti standalone ha diversi vantaggi significativi:

*   **Semplificazione:** Riducono la necessità di creare e gestire `NgModule` per ogni piccola feature o per raggruppare componenti. Il codice diventa meno verboso e la struttura dell'applicazione più facile da capire, specialmente per i nuovi arrivati.
*   **Riduzione del Boilerplate:** Meno file `*.module.ts` da creare e mantenere.
*   **Migliore Organizzazione Logica:** Le dipendenze di un componente sono chiaramente elencate nei suoi `imports`, rendendo più facile vedere di cosa ha bisogno.
*   **Lazy Loading più Granulare:** Puoi caricare pigramente singoli componenti standalone o insiemi di rotte che puntano a componenti standalone, senza dover creare un `NgModule` apposito solo per il lazy loading. Questo porta a un controllo più fine sui chunk JavaScript generati.
*   **Miglioramento del Tree-Shaking:** Dichiarando le dipendenze direttamente, è potenzialmente più facile per il compilatore Angular determinare quale codice è effettivamente utilizzato e rimuovere quello inutilizzato.
*   **Esperienza di Sviluppo (DX) Migliorata:** Molti sviluppatori trovano l'approccio standalone più intuitivo e diretto.

**Impatto sull'Architettura:**

*   **Meno Enfasi sugli `NgModule` per l'Organizzazione:** Mentre gli `NgModule` possono ancora essere usati (vedi Lezione 21), non sono più il meccanismo *primario* per organizzare i componenti. L'organizzazione può basarsi maggiormente sulla struttura delle cartelle e sulla coesione logica dei componenti stessi.
*   **Bootstrap dell'Applicazione:** Le applicazioni interamente standalone vengono avviate usando la funzione `bootstrapApplication` invece di `platformBrowserDynamic().bootstrapModule(AppModule)`.
    ```typescript
    // main.ts (per un'applicazione standalone)
    import { bootstrapApplication } from '@angular/platform-browser';
    import { AppComponent } from './app/app.component'; // Il tuo componente radice standalone
    import { appConfig } from './app/app.config'; // File di configurazione dell'app

    bootstrapApplication(AppComponent, appConfig) // Passa il componente radice e la configurazione
      .catch(err => console.error(err));
    ```
    Il file `app.config.ts` (o simile) è dove configuri i provider a livello di applicazione (come il router, `HttpClient`, ecc.) usando le nuove funzioni `provide*` (es. `provideRouter`, `provideHttpClient`).
    ```typescript
    // app.config.ts
    import { ApplicationConfig } from '@angular/core';
    import { provideRouter } from '@angular/router';
    import { routes } from './app.routes';
    import { provideHttpClient } from '@angular/common/http';

    export const appConfig: ApplicationConfig = {
      providers: [
        provideRouter(routes), // Fornisce il router
        provideHttpClient(),   // Fornisce HttpClient
        // Altri provider a livello di applicazione
      ]
    };
    ```

**4. Come Importare Dipendenze Direttamente nei Componenti Standalone**

Come visto negli esempi, la proprietà `imports: []` nel decoratore `@Component` (e similmente in `@Directive` e `@Pipe`) è il luogo dove elenchi:

*   Altri componenti, direttive o pipe **standalone**.
*   `NgModule` esistenti che esportano le funzionalità di cui hai bisogno (es. `CommonModule`, `FormsModule`, `ReactiveFormsModule`, moduli di librerie UI come `MatButtonModule` da Angular Material).

**Esempio con Angular Material:**
Se usi Angular Material (che è organizzato in moduli), importeresti i moduli dei componenti Material specifici che ti servono.
```typescript
// user-profile.component.ts
import { Component } from '@angular/core';
import { MatCardModule } from '@angular/material/card'; // NgModule da Angular Material
import { MatButtonModule } from '@angular/material/button'; // NgModule da Angular Material
import { UserAvatarComponent } from '../user-avatar/user-avatar.component'; // Un altro componente standalone

@Component({
  selector: 'app-user-profile',
  standalone: true,
  imports: [
    MatCardModule,         // Importa il NgModule per MatCard
    MatButtonModule,       // Importa il NgModule per MatButton
    UserAvatarComponent    // Importa un componente standalone
  ],
  template: `
    <mat-card>
      <mat-card-header>
        <app-user-avatar></app-user-avatar>
        <mat-card-title>Nome Utente</mat-card-title>
        <mat-card-subtitle>Ruolo Utente</mat-card-subtitle>
      </mat-card-header>
      <mat-card-content>
        <p>Bio dell'utente...</p>
      </mat-card-content>
      <mat-card-actions>
        <button mat-button>MI PIACE</button>
        <button mat-button>CONDIVIDI</button>
      </mat-card-actions>
    </mat-card>
  `
})
export class UserProfileComponent {}
```
In futuro, è probabile che anche le librerie come Angular Material inizieranno ad esportare i loro componenti come standalone, semplificando ulteriormente gli import.

**5. Routing con Componenti Standalone**

Abbiamo già toccato questo argomento nella Lezione 15 (Lazy Loading). Il routing con componenti standalone è molto diretto:

*   **Rotte Eager-Loaded:**
    Nella tua configurazione delle rotte (es. `app.routes.ts`), puoi puntare direttamente a un componente standalone:
    ```typescript
    // app.routes.ts
    // import { MyStandalonePageComponent } from './my-standalone-page/my-standalone-page.component';
    // const routes: Routes = [
    //   { path: 'my-page', component: MyStandalonePageComponent }
    // ];
    ```

*   **Rotte Lazy-Loaded:**
    *   **Caricare un singolo componente standalone pigramente:**
        ```typescript
        // app.routes.ts
        // const routes: Routes = [
        //   {
        //     path: 'lazy-standalone',
        //     loadComponent: () => import('./lazy-feature/lazy-standalone.component')
        //                            .then(m => m.LazyStandaloneComponent)
        //   }
        // ];
        ```    *   **Caricare un insieme di rotte figlie che usano componenti standalone pigramente:**
        ```typescript
        // app.routes.ts
        // const routes: Routes = [
        //   {
        //     path: 'admin-area',
        //     loadChildren: () => import('./admin/admin.routes').then(m => m.ADMIN_ROUTES_ARRAY)
        //     // Oppure, con default export nel file admin.routes.ts:
        //     // loadChildren: () => import('./admin/admin.routes')
        //   }
        // ];

        // admin/admin.routes.ts
        // import { Routes } from '@angular/router';
        // import { AdminDashboardComponent } from './admin-dashboard/admin-dashboard.component';
        // import { ManageUsersComponent } from './manage-users/manage-users.component';
        //
        // export const ADMIN_ROUTES_ARRAY: Routes = [
        //   { path: '', component: AdminDashboardComponent },
        //   { path: 'users', component: ManageUsersComponent }
        // ];
        ```

**Transizione da `NgModule` a Standalone:**

Angular fornisce schematics (comandi CLI) per aiutare a migrare applicazioni basate su `NgModule` verso l'approccio standalone. Questo può essere fatto gradualmente.

*   `ng generate @angular/core:standalone`: Questo schematic può aiutare a convertire componenti, direttive e pipe esistenti, e anche a migrare il bootstrap dell'applicazione.

**Conclusione su Standalone:**

L'approccio standalone rappresenta una significativa evoluzione in Angular, mirando a:

*   **Ridurre la complessità e il boilerplate.**
*   **Semplificare l'apprendimento di Angular.**
*   **Migliorare l'esperienza di sviluppo.**
*   **Offrire maggiore flessibilità nel lazy loading.**

Per i nuovi progetti Angular, partire con un'architettura basata su componenti standalone è fortemente raccomandato.

---

Questa lezione ha evidenziato il passaggio verso i componenti standalone come il nuovo standard in Angular, spiegando come crearli, usarli e i benefici che portano.

Il prossimo modulo, "State Management Avanzato", esplorerà come gestire lo stato in applicazioni Angular più grandi, dove la semplice condivisione di dati tramite servizi potrebbe non essere sufficiente.
