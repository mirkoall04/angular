Perfetto! Continuiamo con la **Lezione 14: Route Guards**. I Route Guards sono un meccanismo potente per controllare l'accesso alle rotte, ad esempio per implementare l'autenticazione e l'autorizzazione.

---

### Modulo 5: Routing e Navigazione

**Lezione 14: Route Guards**

I **Route Guards** (o Guardie di Rotta) sono servizi che implementano specifiche interfacce del router per decidere se una navigazione verso o da una rotta debba essere permessa o meno. Sono essenzialmente dei controlli che vengono eseguiti prima che una rotta venga attivata o disattivata.

**Casi d'uso comuni per i Route Guards:**

*   **Autenticazione:** Impedire l'accesso a determinate rotte a utenti non autenticati (es. una dashboard di amministrazione).
*   **Autorizzazione:** Verificare se un utente autenticato ha i permessi necessari per accedere a una specifica rotta (es. solo gli utenti con ruolo "admin" possono accedere a certe funzionalità).
*   **Prevenire la Navigazione Incompleta:** Impedire a un utente di lasciare una rotta se ha modifiche non salvate in un form (`CanDeactivate`).
*   **Caricamento Dati Preliminare:** Recuperare dati necessari per un componente prima che la rotta venga attivata, assicurando che i dati siano disponibili quando il componente viene renderizzato (`Resolve`).

**Tipi di Route Guards:**

Angular fornisce diverse interfacce per i guard, ognuna con uno scopo specifico:

1.  **`CanActivate`**: Controlla se una rotta può essere attivata. Se tutti i guard `CanActivate` restituiscono `true` (o un `Observable<boolean>`/`Promise<boolean>` che risolve a `true`), la navigazione procede. Altrimenti, viene annullata.
    *   *Utilizzo tipico:* Proteggere intere sezioni dell'applicazione che richiedono login.

2.  **`CanActivateChild`**: Controlla se i figli di una rotta possono essere attivati. Utile per proteggere rotte figlie all'interno di una sezione già accessibile.
    *   *Utilizzo tipico:* All'interno di una sezione utente, alcune sotto-rotte potrebbero richiedere permessi specifici.

3.  **`CanDeactivate<T>`**: Controlla se si può uscire da una rotta. Chiede conferma all'utente prima di abbandonare una rotta, ad esempio se ci sono modifiche non salvate.
    *   `T` è il tipo del componente da cui si sta cercando di disattivare la rotta. Il guard avrà accesso all'istanza di quel componente.

4.  **`Resolve<T>`**: Esegue un recupero dati prima che la rotta venga attivata. Il valore restituito dal resolver (di tipo `T`) viene reso disponibile al componente attivato tramite i dati della rotta (`ActivatedRoute.data`).
    *   *Utilizzo tipico:* Assicurarsi che i dati necessari per un componente siano già caricati prima che il componente stesso venga visualizzato, evitando schermate di caricamento vuote all'interno del componente.

5.  **`CanMatch`** (introdotto in versioni più recenti di Angular, es. v14+): Controlla se una rotta può essere abbinata all'URL corrente. Questo è particolarmente utile per il lazy loading condizionale dei moduli, permettendo di decidere se un intero modulo debba essere caricato in base a determinate condizioni (es. ruolo utente).

**Come Funziona un Guard:**

Un guard è un servizio `@Injectable` che implementa una (o più) delle interfacce di guardia. Il metodo richiesto dall'interfaccia deve restituire:

*   `boolean`
*   `UrlTree` (per reindirizzare l'utente a un'altra rotta se la navigazione viene negata)
*   `Observable<boolean | UrlTree>`
*   `Promise<boolean | UrlTree>`

Se il guard restituisce `false` (o un Observable/Promise che emette/risolve a `false`), la navigazione viene annullata. Se restituisce un `UrlTree`, la navigazione corrente viene annullata e viene iniziata una nuova navigazione verso l'URL specificato nell'`UrlTree`.

**1. Implementare l'Autenticazione e l'Autorizzazione di Base con `CanActivate`**

Creiamo un semplice servizio di autenticazione e un guard `CanActivate` per proteggere una rotta.

*   **a. Creare un Servizio di Autenticazione (`AuthService`):**
    ```bash
    ng g s services/auth
    ```
    ```typescript
    // src/app/services/auth.service.ts
    import { Injectable } from '@angular/core';
    import { Observable, of, delay } from 'rxjs';
    import { tap } from 'rxjs/operators';

    @Injectable({
      providedIn: 'root'
    })
    export class AuthService {
      private _isLoggedIn: boolean = false;
      private _userRole: string | null = null; // 'user' o 'admin'

      // Simula un controllo di login
      checkLoginStatus(): Observable<boolean> {
        // In un'app reale, controlleresti un token, una sessione, ecc.
        // Per ora, simuliamo che l'utente sia loggato se _isLoggedIn è true
        return of(this._isLoggedIn).pipe(delay(100)); // Simula chiamata asincrona
      }

      login(role: 'user' | 'admin' = 'user'): Observable<boolean> {
        return of(true).pipe(
          delay(500), // Simula chiamata API
          tap(() => {
            this._isLoggedIn = true;
            this._userRole = role;
            console.log(`Utente loggato come ${this._userRole}`);
          })
        );
      }

      logout(): void {
        this._isLoggedIn = false;
        this._userRole = null;
        console.log('Utente sloggato');
      }

      isLoggedIn(): boolean {
        return this._isLoggedIn;
      }

      getUserRole(): string | null {
        return this._userRole;
      }
    }
    ```

*   **b. Creare un Auth Guard (`AuthGuard`):**
    ```bash
    ng g guard guards/auth --implements CanActivate --functional=false
    # `--functional=false` crea un guard basato su classe (più vecchio stile, ma utile per vedere la struttura)
    # Per un functional guard (più moderno): ng g guard guards/auth --functional
    # Scegliamo basato su classe per mostrare l'iniezione di dipendenze chiaramente.
    ```
    Il CLI ti chiederà quali interfacce implementare; seleziona `CanActivate`.

    ```typescript
    // src/app/guards/auth.guard.ts
    import { Injectable } from '@angular/core';
    import { ActivatedRouteSnapshot, CanActivate, Router, RouterStateSnapshot, UrlTree } from '@angular/router';
    import { Observable } from 'rxjs';
    import { map, tap } from 'rxjs/operators';
    import { AuthService } from '../services/auth.service'; // Importa il tuo AuthService

    @Injectable({
      providedIn: 'root'
    })
    export class AuthGuard implements CanActivate {

      constructor(private authService: AuthService, private router: Router) {}

      canActivate(
        route: ActivatedRouteSnapshot,
        state: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {

        // In un guard basato su classe, puoi iniettare servizi.
        // Per i functional guards (più moderni), usi la funzione inject().

        if (this.authService.isLoggedIn()) {
          // L'utente è già loggato (controllo sincrono)
          console.log('AuthGuard: Utente è loggato (sincrono). Accesso consentito.');
          return true;
        } else {
          // Prova a controllare lo stato del login (es. se c'è un token valido)
          return this.authService.checkLoginStatus().pipe(
            map(isLoggedIn => {
              if (isLoggedIn) {
                console.log('AuthGuard: Utente è loggato (asincrono). Accesso consentito.');
                return true;
              } else {
                console.log('AuthGuard: Utente non loggato. Reindirizzamento a /login.');
                // Utente non loggato, reindirizza alla pagina di login
                // e passa l'URL a cui si tentava di accedere come query param (opzionale)
                return this.router.createUrlTree(['/login'], { queryParams: { returnUrl: state.url } });
              }
            })
          );
        }
      }
    }
    ```
    **Alternativa con Functional Guard (più moderno, da Angular 14+):**
    I functional guards sono semplici funzioni esportate, più concise e spesso preferite.
    ```bash
    ng g guard guards/auth --functional
    ```
    ```typescript
    // src/app/guards/auth.guard.ts (versione funzionale)
    import { inject } from '@angular/core';
    import { CanActivateFn, Router, UrlTree } from '@angular/router';
    import { Observable } from 'rxjs';
    import { map } from 'rxjs/operators';
    import { AuthService } from '../services/auth.service';

    export const authGuard: CanActivateFn = (route, state):
      Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree => {

      const authService = inject(AuthService); // Inietta AuthService
      const router = inject(Router);       // Inietta Router

      if (authService.isLoggedIn()) {
        console.log('authGuard: Utente è loggato (sincrono). Accesso consentito.');
        return true;
      } else {
        return authService.checkLoginStatus().pipe(
          map(isLoggedIn => {
            if (isLoggedIn) {
              console.log('authGuard: Utente è loggato (asincrono). Accesso consentito.');
              return true;
            } else {
              console.log('authGuard: Utente non loggato. Reindirizzamento a /login.');
              return router.createUrlTree(['/login'], { queryParams: { returnUrl: state.url } });
            }
          })
        );
      }
    };
    ```

*   **c. Applicare il Guard a una Rotta:**
    Modifica il tuo array `routes` per aggiungere il guard alla rotta che vuoi proteggere. Supponiamo di avere un `AdminComponent` che vogliamo proteggere.

    ```typescript
    // src/app/app.routes.ts
    // ... (altre importazioni)
    import { AdminComponent } from './admin/admin.component'; // Crea questo componente
    import { LoginComponent } from './login/login.component'; // Crea questo componente
    // Se usi il guard basato su classe:
    // import { AuthGuard } from './guards/auth.guard';
    // Se usi il functional guard:
    import { authGuard } from './guards/auth.guard'; // Importa il functional guard

    export const routes: Routes = [
      // ... (altre rotte)
      { path: 'login', component: LoginComponent },
      {
        path: 'admin',
        component: AdminComponent,
        canActivate: [authGuard] // Applica il guard qui (usa authGuard per il funzionale)
                                   // o [AuthGuard] per quello basato su classe
      },
      // ... (rotta wildcard)
    ];
    ```
    Crea anche un `LoginComponent` base e un `AdminComponent`. `LoginComponent` potrebbe usare `AuthService` per effettuare il login.

    ```typescript
    // src/app/login/login.component.ts (esempio base)
    import { Component } from '@angular/core';
    import { Router, ActivatedRoute } from '@angular/router';
    import { AuthService } from '../services/auth.service';
    import { CommonModule } from '@angular/common'; // Per *ngIf

    @Component({
      selector: 'app-login',
      standalone: true,
      imports: [CommonModule],
      template: `
        <h2>Login</h2>
        <div *ngIf="!isLoggedIn">
          <p>Devi effettuare il login per accedere alle aree protette.</p>
          <button (click)="loginAsUser()">Login come Utente</button>
          <button (click)="loginAsAdmin()">Login come Admin</button>
        </div>
        <div *ngIf="isLoggedIn">
          <p>Sei già loggato.</p>
          <button (click)="logout()">Logout</button>
        </div>
        <p *ngIf="errorMessage">{{ errorMessage }}</p>
      `
    })
    export class LoginComponent {
      isLoggedIn = false;
      errorMessage: string = '';
      private returnUrl: string = '/';

      constructor(
        private authService: AuthService,
        private router: Router,
        private route: ActivatedRoute
      ) {
        this.isLoggedIn = this.authService.isLoggedIn();
        this.route.queryParamMap.subscribe(params => {
          this.returnUrl = params.get('returnUrl') || '/';
        });
      }

      loginAsUser(): void {
        this.authService.login('user').subscribe({
          next: (success) => {
            if (success) {
              this.isLoggedIn = true;
              this.router.navigateByUrl(this.returnUrl);
            } else {
              this.errorMessage = 'Login fallito.';
            }
          },
          error: () => this.errorMessage = 'Errore durante il login.'
        });
      }

      loginAsAdmin(): void {
        this.authService.login('admin').subscribe({
          next: (success) => {
            if (success) {
              this.isLoggedIn = true;
              this.router.navigateByUrl(this.returnUrl);
            } else {
              this.errorMessage = 'Login fallito.';
            }
          },
          error: () => this.errorMessage = 'Errore durante il login.'
        });
      }

      logout(): void {
        this.authService.logout();
        this.isLoggedIn = false;
        this.router.navigate(['/login']);
      }
    }
    ```

*   **d. Testare il Guard:**
    Avvia l'applicazione (`ng serve`). Prova a navigare direttamente a `/admin`. Dovresti essere reindirizzato a `/login`. Dopo aver effettuato il login, dovresti essere in grado di accedere a `/admin` (se il `returnUrl` è gestito correttamente dal `LoginComponent`).

**2. Implementare `CanDeactivate` per Prevenire Perdita di Dati**

Supponiamo di avere un form di modifica e vogliamo avvisare l'utente se cerca di navigare via con modifiche non salvate.

*   **a. Creare un `CanDeactivate` Guard:**
    ```bash
    ng g guard guards/unsaved-changes --implements CanDeactivate --functional=false
    ```
    Seleziona `CanDeactivate`.

    ```typescript
    // src/app/guards/unsaved-changes.guard.ts
    import { Injectable } from '@angular/core';
    import { ActivatedRouteSnapshot, CanDeactivate, RouterStateSnapshot, UrlTree } from '@angular/router';
    import { Observable } from 'rxjs';

    // Interfaccia che il componente deve implementare
    export interface CanComponentDeactivate {
      canDeactivate: () => Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree;
    }

    @Injectable({
      providedIn: 'root'
    })
    export class UnsavedChangesGuard implements CanDeactivate<CanComponentDeactivate> {
      canDeactivate(
        component: CanComponentDeactivate, // Il componente che sta per essere disattivato
        currentRoute: ActivatedRouteSnapshot,
        currentState: RouterStateSnapshot,
        nextState?: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {

        // Chiama il metodo canDeactivate() del componente
        // Se il componente non ha questo metodo, permette la navigazione (o gestiscilo diversamente)
        return component.canDeactivate ? component.canDeactivate() : true;
      }
    }
    ```
    **Versione Funzionale di `CanDeactivate`:**
    ```typescript
    // src/app/guards/unsaved-changes.guard.ts (funzionale)
    import { CanDeactivateFn, UrlTree } from '@angular/router';
    import { Observable } from 'rxjs';

    export interface CanComponentDeactivate {
      canDeactivate: () => Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree;
    }

    export const unsavedChangesGuard: CanDeactivateFn<CanComponentDeactivate> = (component, currentRoute, currentState, nextState) => {
      return component.canDeactivate ? component.canDeactivate() : true;
    };
    ```

*   **b. Implementare l'interfaccia nel Componente:**
    Il componente che ha il form deve implementare `CanComponentDeactivate` e fornire la logica per il metodo `canDeactivate()`.

    ```typescript
    // src/app/edit-profile/edit-profile.component.ts (esempio)
    import { Component } from '@angular/core';
    import { Observable, of } from 'rxjs';
    import { CanComponentDeactivate } from '../guards/unsaved-changes.guard'; // Importa l'interfaccia

    @Component({
      selector: 'app-edit-profile',
      standalone: true,
      imports: [], // Aggiungi FormsModule se usi ngModel
      template: `
        <h2>Modifica Profilo</h2>
        <label>Nome: <input [(ngModel)]="profileName" (input)="onInput()"></label>
        <p><small>Hai modifiche non salvate: {{ hasUnsavedChanges ? 'Sì' : 'No' }}</small></p>
        <button (click)="saveChanges()">Salva Modifiche</button>
      `
      // Per [(ngModel)], importa FormsModule
    })
    export class EditProfileComponent implements CanComponentDeactivate {
      profileName: string = 'Nome Profilo Originale';
      hasUnsavedChanges: boolean = false;

      constructor() {}

      onInput(): void {
        this.hasUnsavedChanges = true;
      }

      saveChanges(): void {
        console.log('Modifiche salvate!');
        this.hasUnsavedChanges = false;
        // Logica per salvare i dati...
      }

      canDeactivate(): Observable<boolean> | Promise<boolean> | boolean {
        if (this.hasUnsavedChanges) {
          // Usa window.confirm o un dialog modale più carino
          return window.confirm('Hai modifiche non salvate. Sei sicuro di voler lasciare questa pagina?');
        }
        return true; // Permette la navigazione se non ci sono modifiche non salvate
      }
    }
    // Per FormsModule in EditProfileComponent:
    // import { FormsModule } from '@angular/forms';
    // @Component({ ... imports: [FormsModule], ... })
    ```

*   **c. Applicare il Guard alla Rotta:**
    ```typescript
    // src/app/app.routes.ts
    // ...
    import { EditProfileComponent } from './edit-profile/edit-profile.component';
    // Se usi il guard basato su classe:
    // import { UnsavedChangesGuard } from './guards/unsaved-changes.guard';
    // Se usi il functional guard:
    import { unsavedChangesGuard } from './guards/unsaved-changes.guard';

    export const routes: Routes = [
      // ...
      {
        path: 'edit-profile',
        component: EditProfileComponent,
        canDeactivate: [unsavedChangesGuard] // Applica il guard
                                           // o [UnsavedChangesGuard] per quello basato su classe
      },
      // ...
    ];
    ```

**3. Utilizzare `Resolve` per Pre-caricare Dati**

Un resolver può recuperare dati prima che una rotta venga attivata.

*   **a. Creare un Resolver:**
    ```bash
    ng g resolver resolvers/user-data --functional=false
    # o ng g resolver resolvers/user-data --functional (per funzionale)
    ```
    ```typescript
    // src/app/resolvers/user-data.resolver.ts
    import { Injectable } from '@angular/core';
    import { Resolve, ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';
    import { Observable, of } from 'rxjs';
    import { delay } from 'rxjs/operators';

    export interface UserData {
      id: number;
      name: string;
      email: string;
    }

    @Injectable({
      providedIn: 'root'
    })
    export class UserDataResolver implements Resolve<UserData> {
      resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<UserData> {
        const userId = route.paramMap.get('id'); // Supponendo che l'ID sia nella rotta
        console.log(`UserDataResolver: Caricamento dati per utente ID: ${userId}`);
        // In un'app reale, chiameresti un servizio per recuperare i dati
        // Simula una chiamata API:
        return of({
          id: Number(userId) || 0,
          name: `Utente ${userId}`,
          email: `utente${userId}@example.com`
        }).pipe(delay(1500)); // Simula ritardo di rete
      }
    }
    ```
    **Versione Funzionale di `Resolve`:**
    ```typescript
    // src/app/resolvers/user-data.resolver.ts (funzionale)
    import { inject } from '@angular/core';
    import { ResolveFn, ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';
    import { Observable, of } from 'rxjs';
    import { delay } from 'rxjs/operators';
    // import { UserService } from '../services/user.service'; // Se i dati vengono da un servizio

    export interface UserData { /* ... come sopra ... */ }

    export const userDataResolver: ResolveFn<UserData> = (route, state) => {
      // const userService = inject(UserService); // Esempio di iniezione di un servizio
      const userId = route.paramMap.get('id');
      console.log(`userDataResolver: Caricamento dati per utente ID: ${userId}`);
      return of({ /* ... dati ... */ }).pipe(delay(1500));
    };
    ```

*   **b. Applicare il Resolver a una Rotta:**
    Nel file `app.routes.ts`, aggiungi la proprietà `resolve` alla configurazione della rotta.

    ```typescript
    // src/app/app.routes.ts
    // ...
    // Se usi il resolver basato su classe:
    // import { UserDataResolver } from './resolvers/user-data.resolver';
    // Se usi il resolver funzionale:
    import { userDataResolver } from './resolvers/user-data.resolver';
    import { UserProfileWithResolverComponent } from './user-profile-with-resolver/user-profile-with-resolver.component'; // Crea questo componente

    export const routes: Routes = [
      // ...
      {
        path: 'profile/:id', // La rotta che usa il resolver
        component: UserProfileWithResolverComponent,
        resolve: {
          // La chiave 'userData' sarà usata per accedere ai dati risolti
          userData: userDataResolver // Usa userDataResolver per funzionale
                                      // o UserDataResolver per basato su classe
        }
      },
      // ...
    ];
    ```

*   **c. Accedere ai Dati Risolti nel Componente:**
    I dati risolti sono disponibili tramite `ActivatedRoute.data` (che è un Observable).

    ```typescript
    // src/app/user-profile-with-resolver/user-profile-with-resolver.component.ts
    import { Component, OnInit } from '@angular/core';
    import { ActivatedRoute } from '@angular/router';
    import { UserData } from '../resolvers/user-data.resolver'; // Importa l'interfaccia dati
    import { CommonModule } from '@angular/common'; // Per *ngIf

    @Component({
      selector: 'app-user-profile-with-resolver',
      standalone: true,
      imports: [CommonModule],
      template: `
        <div *ngIf="resolvedUserData">
          <h2>Profilo Utente (con Resolver)</h2>
          <p>ID: {{ resolvedUserData.id }}</p>
          <p>Nome: {{ resolvedUserData.name }}</p>
          <p>Email: {{ resolvedUserData.email }}</p>
        </div>
        <div *ngIf="!resolvedUserData">
          <p>Caricamento dati utente...</p>
        </div>
      `
    })
    export class UserProfileWithResolverComponent implements OnInit {
      resolvedUserData: UserData | null = null;

      constructor(private route: ActivatedRoute) {}

      ngOnInit(): void {
        // Accedi ai dati risolti. 'data' è un Observable.
        this.route.data.subscribe(dataContainer => {
          // 'userData' è la chiave che abbiamo definito nella proprietà 'resolve' della rotta
          this.resolvedUserData = dataContainer['userData'];
          console.log('Dati risolti nel componente:', this.resolvedUserData);
        });
      }
    }
    ```
    Ora, quando navighi a `/profile/123`, `UserDataResolver` verrà eseguito. La navigazione verso `UserProfileWithResolverComponent` attenderà che l'Observable del resolver completi. Solo allora il componente verrà istanziato e i dati saranno disponibili in `this.route.data`.

---

I Route Guards e i Resolver sono strumenti essenziali per gestire flussi di navigazione complessi, sicurezza e caricamento dati nelle tue applicazioni Angular. L'uso di functional guards e resolvers è l'approccio moderno e più conciso.

Nella prossima lezione, "Lazy Loading dei Moduli", vedremo come migliorare le performance iniziali della tua applicazione caricando parti di essa solo quando sono effettivamente necessarie.
