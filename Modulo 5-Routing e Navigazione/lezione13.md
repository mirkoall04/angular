Certamente! Proseguiamo con la **Lezione 13: Parametri di Rotta e Route Wildcard**. Questa lezione espande le nostre capacità di routing, permettendoci di creare rotte più dinamiche e di gestire URL non previsti.

---

### Modulo 5: Routing e Navigazione

**Lezione 13: Parametri di Rotta e Route Wildcard**

Spesso, nelle applicazioni web, abbiamo bisogno di visualizzare dettagli specifici per un elemento, come il profilo di un utente, i dettagli di un prodotto, o un articolo di un blog. L'identificatore di questo elemento è tipicamente parte dell'URL. I **parametri di rotta** ci permettono di catturare questi valori dinamici dall'URL.

Inoltre, è fondamentale gestire i casi in cui un utente naviga verso un URL che non corrisponde a nessuna rotta definita. Le **route wildcard** ci aiutano a intercettare questi URL e a mostrare, ad esempio, una pagina "404 Not Found".

**1. Passare Dati alle Route (Parametri Obbligatori e Opzionali)**

Ci sono due tipi principali di parametri che puoi passare con le rotte:

*   **Parametri di Rotta Obbligatori (Route Parameters):**
    *   Fanno parte del percorso URL stesso.
    *   Sono definiti nella configurazione della rotta usando la sintassi con i due punti (`:`), ad esempio `:id`.
    *   Esempio: `/users/:id`, `/products/:category/:productId`

*   **Parametri di Query Opzionali (Query Parameters):**
    *   Sono aggiunti alla fine dell'URL dopo un punto interrogativo (`?`).
    *   Sono coppie chiave-valore, separate da `&`.
    *   Esempio: `/search?query=angular&sort=relevance`, `/products?category=electronics&page=2`

**a. Definire Rotte con Parametri Obbligatori:**

Modifichiamo la nostra configurazione delle rotte per includere una rotta che accetta un parametro `id` per visualizzare i dettagli di un utente (ipotetico).

```typescript
// src/app/app.routes.ts
import { Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { AboutComponent } from './about/about.component';
import { ContactComponent } from './contact/contact.component';
import { UserDetailComponent } from './user-detail/user-detail.component'; // Crea questo componente
import { NotFoundComponent } from './not-found/not-found.component'; // Crea questo componente

export const routes: Routes = [
  { path: '', redirectTo: '/home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'contact', component: ContactComponent },
  { path: 'users/:id', component: UserDetailComponent }, // Rotta con parametro 'id'
  // ... (altre rotte)
  // La route wildcard va messa per ultima!
  // { path: '**', component: NotFoundComponent } // Lo vedremo tra poco
];
```
*   La rotta `{ path: 'users/:id', component: UserDetailComponent }` indica che quando l'URL matcha un pattern come `/users/1` o `/users/john-doe`, il `UserDetailComponent` verrà attivato, e il valore dopo `/users/` sarà disponibile come parametro `id`.

**b. Navigare verso Rotte con Parametri Obbligatori:**

*   **Con `routerLink`:**
    ```html
    <!-- In un template che lista utenti, ad esempio -->
    <ul>
      <li><a [routerLink]="['/users', 1]">Dettagli Utente 1</a></li>
      <li><a [routerLink]="['/users', 'admin']">Dettagli Utente 'admin'</a></li>
    </ul>
    ```
    L'array passato a `routerLink` viene usato per costruire il percorso: `['/base-path', param1, param2, ...]`.

*   **Con il servizio `Router` (navigazione programmatica):**
    ```typescript
    // In un componente
    constructor(private router: Router) {}

    navigateToUser(userId: number | string): void {
      this.router.navigate(['/users', userId]);
    }
    ```

**c. Passare Parametri di Query Opzionali:**

*   **Con `routerLink`:**
    Usa la proprietà `queryParams` (che accetta un oggetto).
    ```html
    <a routerLink="/search" [queryParams]="{ query: 'angular', category: 'framework' }">
      Cerca Angular Framework
    </a>
    <!-- Genera: /search?query=angular&category=framework -->

    <a [routerLink]="['/products']" [queryParams]="{ page: 2, sort: 'price' }">
      Prodotti Pagina 2 (ordinati per prezzo)
    </a>
    ```

*   **Con il servizio `Router`:**
    Usa l'oggetto `NavigationExtras` nel metodo `navigate`.
    ```typescript
    // In un componente
    constructor(private router: Router) {}

    performSearch(searchTerm: string): void {
      this.router.navigate(['/search'], { queryParams: { q: searchTerm, filter: 'active' } });
    }
    ```

**2. Recuperare i Parametri di Rotta e i Query Parameters (`ActivatedRoute`)**

Una volta che hai navigato a una rotta con parametri, il componente attivato ha bisogno di leggere questi parametri. Il servizio `ActivatedRoute` (da `@angular/router`) fornisce accesso alle informazioni sulla rotta corrente, inclusi i parametri.

*   **Iniettare `ActivatedRoute`:**
    ```typescript
    // user-detail.component.ts
    import { Component, OnInit, OnDestroy } from '@angular/core';
    import { ActivatedRoute, Params } from '@angular/router'; // Importa ActivatedRoute e Params
    import { Subscription } from 'rxjs';

    @Component({
      selector: 'app-user-detail',
      standalone: true,
      imports: [], // Aggiungi CommonModule se usi direttive come *ngIf
      template: `
        <div *ngIf="userId">
          <h2>Dettagli Utente ID: {{ userId }}</h2>
          <p>Qui verranno mostrate le informazioni per l'utente con ID: {{ userId }}.</p>
          <!-- Ulteriori dettagli caricati in base a userId -->
        </div>
        <div *ngIf="userNotFound">
          <p>Utente non trovato.</p>
        </div>

        <div *ngIf="queryParams && queryParams['source']">
          <p><small>Navigato da: {{ queryParams['source'] }}</small></p>
        </div>
      `
    })
    export class UserDetailComponent implements OnInit, OnDestroy {
      userId: string | null = null;
      queryParams: Params | null = null;
      userNotFound: boolean = false;

      private routeParamsSubscription: Subscription;
      private queryParamsSubscription: Subscription;

      constructor(private route: ActivatedRoute) {} // Inietta ActivatedRoute

      ngOnInit(): void {
        // 1. Recuperare Parametri di Rotta Obbligatori (es. /users/:id)

        // Approccio con Snapshot (solo per il valore iniziale, se il componente non viene mai riutilizzato per la stessa rotta con parametri diversi)
        // this.userId = this.route.snapshot.paramMap.get('id');
        // console.log('User ID (snapshot):', this.userId);
        // if (this.userId) { /* Carica dati utente */ }

        // Approccio con Observable (RACCOMANDATO, gestisce i cambiamenti se il componente viene riutilizzato)
        this.routeParamsSubscription = this.route.paramMap.subscribe(params => {
          this.userId = params.get('id');
          console.log('User ID (observable):', this.userId);
          if (this.userId) {
            // Qui caricheresti i dati dell'utente basati su this.userId
            // Esempio: this.userService.getUserById(this.userId).subscribe(...)
            // Simula il caricamento:
            if (this.userId === 'non-esistente') {
                this.userNotFound = true;
            } else {
                this.userNotFound = false;
                // Logica per caricare i dati dell'utente
            }
          }
        });

        // 2. Recuperare Parametri di Query Opzionali (es. ?source=button)

        // Approccio con Snapshot
        // const source = this.route.snapshot.queryParamMap.get('source');
        // console.log('Query Param "source" (snapshot):', source);

        // Approccio con Observable (RACCOMANDATO)
        this.queryParamsSubscription = this.route.queryParamMap.subscribe(queryParams => {
          this.queryParams = { ...queryParams['params'] }; // Converte QueryParams in un oggetto semplice se necessario
          console.log('Query Params (observable):', this.queryParams);
          if (this.queryParams['source']) {
            // Fai qualcosa con il query parameter 'source'
          }
        });
      }

      ngOnDestroy(): void {
        // È FONDAMENTALE fare l'unsubscribe dagli observables per evitare memory leak!
        if (this.routeParamsSubscription) {
          this.routeParamsSubscription.unsubscribe();
        }
        if (this.queryParamsSubscription) {
          this.queryParamsSubscription.unsubscribe();
        }
      }
    }
    ```

*   **`snapshot` vs. Observable (`paramMap`, `queryParamMap`):**
    *   `this.route.snapshot.paramMap` (o `queryParamMap`): Fornisce un'istantanea (snapshot) dei parametri al momento della creazione del componente. **Non si aggiorna** se navighi dalla stessa rotta a sé stessa con parametri diversi (es. da `/users/1` a `/users/2` senza che il componente venga distrutto e ricreato). È adatto solo se sei sicuro che il componente verrà sempre distrutto e ricreato quando i parametri cambiano.
    *   `this.route.paramMap` (o `queryParamMap`): Sono **Observables**. Si sottoscrivono a questi Observables per ricevere i valori dei parametri ogni volta che cambiano, anche se il componente viene riutilizzato per la stessa rotta. **Questo è l'approccio raccomandato e più robusto.**
        *   `paramMap` e `queryParamMap` forniscono metodi come `get('nomeParametro')`, `getAll('nomeParametro')` (se un parametro può avere più valori), e `has('nomeParametro')`.
    *   **Importante:** Ricordati sempre di fare l'**unsubscribe** dagli Observables in `ngOnDestroy` per prevenire memory leak.

**3. Configurare una Rotta Wildcard (`**`) per le Pagine 404**

Se un utente inserisce un URL che non corrisponde a nessuna delle tue route definite, per impostazione predefinita Angular non farà nulla o potrebbe mostrare un errore. È buona pratica definire una **rotta wildcard** per intercettare tutti questi URL non corrispondenti e mostrare una pagina "Not Found" (404).

*   **Definizione della Rotta Wildcard:**
    *   La rotta wildcard usa `**` come `path`.
    *   **Deve essere l'ULTIMA rotta nell'array di configurazione `routes`**, perché le rotte vengono valutate in ordine. Se la mettessi prima, intercetterebbe tutte le rotte che la seguono.

    ```typescript
    // src/app/app.routes.ts (aggiornato)
    import { Routes } from '@angular/router';
    import { HomeComponent } from './home/home.component';
    import { AboutComponent } from './about/about.component';
    import { ContactComponent } from './contact/contact.component';
    import { UserDetailComponent } from './user-detail/user-detail.component';
    import { NotFoundComponent } from './not-found/not-found.component'; // Assicurati che esista

    export const routes: Routes = [
      { path: '', redirectTo: '/home', pathMatch: 'full' },
      { path: 'home', component: HomeComponent },
      { path: 'about', component: AboutComponent },
      { path: 'contact', component: ContactComponent },
      { path: 'users/:id', component: UserDetailComponent },
      // ... altre rotte specifiche

      { path: '**', component: NotFoundComponent } // Rotta Wildcard - DEVE ESSERE L'ULTIMA
    ];
    ```

*   **Creare il `NotFoundComponent`:**
    Genera un semplice componente che informa l'utente che la pagina non è stata trovata.
    ```bash
    ng g c not-found
    ```
    ```typescript
    // src/app/not-found/not-found.component.ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-not-found',
      standalone: true,
      imports: [],
      template: `
        <div style="text-align: center; margin-top: 50px;">
          <h1>404 - Pagina Non Trovata</h1>
          <p>Spiacenti, la pagina che stai cercando non esiste.</p>
          <a routerLink="/home">Torna alla Home</a>
          <!-- Assicurati che RouterLink sia importato in AppComponent o dove serve -->
        </div>
      `
    })
    export class NotFoundComponent {}
    ```    Per usare `routerLink` nel template di `NotFoundComponent`, dovresti importare `RouterLink` nell'array `imports` del `@Component` di `NotFoundComponent`.

Ora, se un utente naviga a un URL come `/qualcosa-che-non-esiste`, vedrà il contenuto di `NotFoundComponent`.

---

Abbiamo imparato come rendere le nostre rotte dinamiche usando parametri obbligatori e opzionali, come leggerli nei nostri componenti usando `ActivatedRoute`, e come gestire elegantemente gli URL non validi con una rotta wildcard. Questi sono strumenti essenziali per costruire SPA complesse e user-friendly.

Nella prossima lezione, "Route Guards", vedremo come proteggere le rotte, ad esempio richiedendo l'autenticazione prima di accedere a determinate sezioni dell'applicazione.

Domande sui parametri di rotta o sulle route wildcard?