Perfetto! Iniziamo il **Modulo 5: Routing e Navigazione**. Questo modulo è essenziale per costruire Single Page Applications (SPA) con più viste o "pagine" virtuali. Partiamo con la **Lezione 12: Configurazione del Routing di Base**.

---

### Modulo 5: Routing e Navigazione

Nelle Single Page Applications (SPA), l'intera applicazione viene caricata una sola volta (una singola pagina HTML). La navigazione tra diverse sezioni o "pagine" dell'applicazione avviene dinamicamente, aggiornando porzioni della vista senza ricaricare l'intera pagina dal server. Il **Router di Angular** (`RouterModule`) è il modulo che gestisce questa navigazione lato client.

**Lezione 12: Configurazione del Routing di Base**

In questa lezione, vedremo come impostare le fondamenta del sistema di routing nella tua applicazione Angular.

**1. Importare `RouterModule` e configurare le route (`Routes` array)**

Per abilitare il routing, devi configurare il `RouterModule` di Angular. Questo viene fatto definendo un array di oggetti `Route`, dove ogni oggetto mappa un percorso URL (path) a un componente che dovrebbe essere visualizzato quando quel percorso è attivo.

*   **Configurazione per Applicazioni Standalone (da Angular 14+, default da v17):**
    Nelle applicazioni basate su componenti standalone, la configurazione del routing avviene tipicamente nel file `app.config.ts` (per il routing a livello di applicazione) o direttamente nell'array `imports` di un componente standalone se si tratta di routing specifico per quel componente (meno comune per il routing principale).

    1.  **Definire le Route:**
        Crea un file (es. `app.routes.ts`) per definire il tuo array di `Routes`.
        ```typescript
        // src/app/app.routes.ts
        import { Routes } from '@angular/router';
        import { HomeComponent } from './home/home.component'; // Crea questi componenti
        import { AboutComponent } from './about/about.component';
        import { ContactComponent } from './contact/contact.component';

        export const routes: Routes = [
          { path: '', component: HomeComponent }, // Rotta di default (es. http://localhost:4200/)
          { path: 'home', component: HomeComponent },
          { path: 'about', component: AboutComponent },
          { path: 'contact', component: ContactComponent }
          // Altre rotte verranno aggiunte qui
        ];
        ```
        *   Ogni oggetto nel `routes` array è un oggetto `Route`.
        *   `path`: Una stringa che definisce il segmento URL per questa rotta.
            *   `''` (stringa vuota) è spesso usata per la rotta di default o la "home page".
        *   `component`: La classe del componente da visualizzare quando questa rotta è attiva.
        *   *Assicurati che i componenti `HomeComponent`, `AboutComponent`, `ContactComponent` esistano e siano componenti standalone (o dichiarati in un modulo importato).*

    2.  **Fornire la Configurazione del Router:**
        Nel file `app.config.ts`, usa la funzione `provideRouter` (da `@angular/router`) per registrare le tue route.
        ```typescript
        // src/app/app.config.ts
        import { ApplicationConfig } from '@angular/core';
        import { provideRouter } from '@angular/router';
        import { routes } from './app.routes'; // Importa le tue route

        export const appConfig: ApplicationConfig = {
          providers: [
            provideRouter(routes) // Configura il router con le tue route
            // Altri provider (es. provideHttpClient())
          ]
        };
        ```
        La funzione `provideRouter()` è il modo moderno e tree-shakable per configurare il router in applicazioni standalone.

*   **Configurazione per Applicazioni Basate su NgModule (approccio più vecchio):**
    Nelle applicazioni basate su moduli, il `RouterModule` viene importato e configurato all'interno di un `NgModule` (tipicamente `AppModule` per le rotte principali, o un modulo di routing separato).

    1.  **Creare un Modulo di Routing (Opzionale ma Raccomandato):**
        È buona pratica creare un modulo separato per la configurazione del routing, spesso chiamato `AppRoutingModule`.
        ```bash
        ng generate module AppRouting --flat --module=app
        # --flat mette il file nella cartella src/app invece di una sua cartella
        # --module=app lo registra in AppModule
        ```

    2.  **Definire le Route e Configurare `RouterModule`:**
        ```typescript
        // src/app/app-routing.module.ts
        import { NgModule } from '@angular/core';
        import { RouterModule, Routes } from '@angular/router';
        import { HomeComponent } from './home/home.component';
        import { AboutComponent } from './about/about.component';
        import { ContactComponent } from './contact/contact.component';

        const routes: Routes = [
          { path: '', component: HomeComponent },
          { path: 'home', component: HomeComponent },
          { path: 'about', component: AboutComponent },
          { path: 'contact', component: ContactComponent }
        ];

        @NgModule({
          imports: [RouterModule.forRoot(routes)], // Usa forRoot() per il modulo di routing radice
          exports: [RouterModule] // Esporta RouterModule così è disponibile per AppModule
        })
        export class AppRoutingModule { }
        ```        *   `RouterModule.forRoot(routes)`: Questo metodo statico viene usato per configurare il router a livello radice dell'applicazione. Deve essere chiamato solo una volta, tipicamente nel modulo di routing radice o nell'`AppModule`. Per i feature module, userai `RouterModule.forChild(routes)`.

    3.  **Importare `AppRoutingModule` in `AppModule`:**
        ```typescript
        // src/app/app.module.ts
        import { NgModule } from '@angular/core';
        import { BrowserModule } from '@angular/platform-browser';
        import { AppRoutingModule } from './app-routing.module'; // Importa il modulo di routing
        import { AppComponent } from './app.component';
        import { HomeComponent } from './home/home.component';
        import { AboutComponent } from './about/about.component';
        import { ContactComponent } from './contact/contact.component';

        @NgModule({
          declarations: [
            AppComponent,
            HomeComponent, // Dichiara i componenti usati nelle rotte (se non standalone)
            AboutComponent,
            ContactComponent
          ],
          imports: [
            BrowserModule,
            AppRoutingModule // Aggiungi AppRoutingModule agli imports
          ],
          providers: [],
          bootstrap: [AppComponent]
        })
        export class AppModule { }
        ```

**2. `<router-outlet>`**

Una volta configurate le route, devi dire ad Angular *dove* nel tuo template visualizzare il componente attivato dalla rotta corrente. Questo viene fatto usando la direttiva `<router-outlet>`.

*   **Utilizzo:**
    Solitamente, posizioni `<router-outlet>` nel template del tuo componente radice (`AppComponent`) o in un componente di layout principale. Quando navighi verso un URL, il componente associato a quella rotta verrà renderizzato all'interno del tag `<router-outlet>`.

    ```html
    <!-- src/app/app.component.html (o un componente di layout) -->
    <nav>
      <ul>
        <li><a routerLink="/home">Home</a></li>
        <li><a routerLink="/about">About Us</a></li>
        <li><a routerLink="/contact">Contact</a></li>
      </ul>
    </nav>

    <hr>

    <main>
      <!-- I componenti attivati dal router verranno visualizzati qui -->
      <router-outlet></router-outlet>
    </main>

    <footer>
      <p>&copy; 2025 La Mia Applicazione Angular</p>
    </footer>
    ```
    *   Il componente `AppComponent` deve essere standalone e importare `RouterOutlet` e `RouterLink` (o `RouterModule` se si usa l'approccio basato su NgModule).

    ```typescript
    // src/app/app.component.ts (per standalone)
    import { Component } from '@angular/core';
    import { RouterOutlet, RouterLink } from '@angular/router'; // Importa RouterOutlet e RouterLink

    @Component({
      selector: 'app-root',
      standalone: true,
      imports: [RouterOutlet, RouterLink], // Aggiungi RouterOutlet e RouterLink agli imports
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent {
      title = 'mia-app-con-routing';
    }
    ```

**3. `routerLink`**

Per creare link di navigazione nei tuoi template che interagiscono con il router di Angular (invece dei tradizionali tag `<a>` con `href` che causerebbero un ricaricamento completo della pagina), usi la direttiva `routerLink`.

*   **Sintassi di base:** `routerLink="/percorso/desiderato"`
*   **Con parametri di rotta (li vedremo meglio dopo):** `[routerLink]="['/percorso', parametro1, parametro2]"`
*   **Stilizzare i link attivi:** Puoi usare la direttiva `routerLinkActive` per applicare automaticamente una classe CSS a un link quando la sua rotta associata è attiva.

    ```html
    <!-- src/app/app.component.html (esempio di navigazione) -->
    <nav>
      <ul>
        <li>
          <a routerLink="/home" routerLinkActive="active-link" [routerLinkActiveOptions]="{exact: true}">
            Home
          </a>
        </li>
        <li>
          <a routerLink="/about" routerLinkActive="active-link">
            About Us
          </a>
        </li>
        <li>
          <a routerLink="/contact" routerLinkActive="active-link">
            Contact
          </a>
        </li>
        <!-- Esempio di link con parametri (ipotetico) -->
        <!-- <li><a [routerLink]="['/users', 123]" routerLinkActive="active-link">Profilo Utente 123</a></li> -->
      </ul>
    </nav>

    <router-outlet></router-outlet>
    ```
    *   `routerLinkActive="active-link"`: Applica la classe CSS `active-link` all'elemento `<a>` quando la rotta `/home`, `/about`, o `/contact` è attiva.
    *   `[routerLinkActiveOptions]="{exact: true}"`: Per la rotta radice o rotte che sono prefissi di altre, `exact: true` assicura che la classe attiva venga applicata solo quando l'URL corrisponde esattamente. Ad esempio, senza `exact: true` su `/home`, il link "Home" potrebbe rimanere attivo anche quando si naviga a `/home/details` (se esistesse una rotta del genere).

    Definisci la classe `active-link` nel tuo CSS:
    ```css
    /* styles.css o app.component.css */
    .active-link {
      font-weight: bold;
      color: blue;
      text-decoration: underline;
    }
    ```

**4. Navigazione Programmatica con il servizio `Router`**

Oltre a usare `routerLink` nei template, puoi anche navigare programmaticamente dal codice TypeScript del tuo componente (o servizio) usando il servizio `Router` di Angular.

1.  **Iniettare il servizio `Router`:**
    Importa `Router` da `@angular/router` e iniettalo nel costruttore del tuo componente/servizio.

2.  **Usare il metodo `navigate()` o `navigateByUrl()`:**
    *   `router.navigate(['/percorso', param1, param2], extras?)`: Naviga a una rotta costruita da un array di segmenti di percorso. È più flessibile e generalmente preferito.
        *   `extras` (opzionale): Un oggetto `NavigationExtras` che può contenere opzioni come `queryParams`, `fragment`, `relativeTo`, ecc.
    *   `router.navigateByUrl('/percorso-completo-come-stringa?query=val#fragment')`: Naviga a un URL fornito come stringa singola.

    ```typescript
    // some-action.component.ts
    import { Component } from '@angular/core';
    import { Router } from '@angular/router'; // Importa Router
    // Assicurati che il componente sia standalone e importi ciò che serve (es. CommonModule se usi direttive)

    @Component({
      selector: 'app-some-action',
      standalone: true,
      imports: [],
      template: `
        <button (click)="goToAboutPage()">Vai alla Pagina About</button>
        <button (click)="goToContactWithQuery()">Vai a Contatti con Query Params</button>
      `
    })
    export class SomeActionComponent {
      constructor(private router: Router) {} // Inietta il Router

      goToAboutPage(): void {
        this.logger.log('Navigazione verso /about...'); // Se hai un logger
        this.router.navigate(['/about']);
      }

      goToContactWithQuery(): void {
        this.router.navigate(['/contact'], { queryParams: { source: 'button', reason: 'demo' } });
        // Questo navigherà a /contact?source=button&reason=demo
      }
    }
    ```

**Strategia di Matching dei Path (`pathMatch`)**

Per le rotte, specialmente quelle con path vuoto (`path: ''`) o rotte di reindirizzamento, la proprietà `pathMatch` è importante:

*   `pathMatch: 'full'` (default per `path: ''` se non specificato diversamente in alcune configurazioni di `redirectTo`): La rotta matcha solo se l'URL *completo* corrisponde al path.
*   `pathMatch: 'prefix'` (default per la maggior parte delle altre rotte): La rotta matcha se l'URL *inizia* con il path specificato.

Per una rotta di reindirizzamento da un path vuoto alla home, si usa spesso:```typescript
// app.routes.ts
// ...
{ path: '', redirectTo: '/home', pathMatch: 'full' }, // Se l'URL è vuoto, reindirizza a /home
{ path: 'home', component: HomeComponent },
// ...
```

---

Questa lezione ha coperto le basi per mettere in piedi il sistema di routing: definire le route, usare `<router-outlet>` per visualizzare i componenti, e navigare con `routerLink` e il servizio `Router`.

Nella prossima lezione, "Parametri di Rotta e Route Wildcard", vedremo come passare dati alle tue rotte e come gestire URL non validi.

Domande sulla configurazione di base del routing?