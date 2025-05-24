Assolutamente! Concludiamo il Modulo 7 con la **Lezione 20: Interceptors HTTP**. Gli intercettori sono una funzionalità molto potente di `HttpClient` che permette di intercettare e modificare le richieste HTTP in uscita e le risposte (o errori) in entrata a livello globale.

---

### Modulo 7: Comunicazione con il Backend (HTTP)

**Lezione 20: Interceptors HTTP**

Gli **Interceptors HTTP** forniscono un meccanismo per intercettare e trasformare le richieste HTTP prima che vengano inviate al server e le risposte HTTP prima che vengano passate al codice che ha effettuato la richiesta. Questo è estremamente utile per gestire funzionalità trasversali come l'autenticazione, il logging, la modifica degli header, la trasformazione dei dati delle risposte, la gestione degli errori a livello globale, e il caching.

**Come Funziona un Interceptor:**

Un interceptor è un servizio speciale che implementa l'interfaccia `HttpInterceptor`. Questa interfaccia ha un solo metodo: `intercept()`.

*   `intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>>`
    *   `req: HttpRequest<any>`: L'oggetto della richiesta HTTP in uscita. È **immutabile**. Se vuoi modificarlo, devi clonarlo (`req.clone({ /* modifiche */ })`).
    *   `next: HttpHandler`: Rappresenta il prossimo interceptor nella catena (o il backend HTTP se questo è l'ultimo interceptor). Chiamare `next.handle(modifiedReq)` passa la richiesta (modificata o originale) al prossimo gestore. **Devi chiamare `next.handle()`** affinché la richiesta proceda; altrimenti, la richiesta non verrà mai inviata.
    *   Il metodo deve restituire un `Observable<HttpEvent<any>>`. Solitamente, questo è l'Observable restituito da `next.handle()`, a cui puoi applicare operatori RxJS per gestire la risposta o gli errori.

**Casi d'Uso Comuni per gli Interceptors:**

*   **Aggiunta di Header di Autenticazione:** Inserire automaticamente un token JWT (JSON Web Token) o altri header di autenticazione a tutte le richieste (o a quelle dirette a specifiche API).
*   **Logging:** Loggare tutte le richieste HTTP in uscita e le risposte in entrata.
*   **Modifica URL:** Aggiungere un prefisso base a tutti gli URL delle API.
*   **Gestione degli Errori Globale:** Intercettare errori HTTP specifici (es. 401 Unauthorized, 500 Internal Server Error) e gestirli a livello globale (es. reindirizzare al login, mostrare una notifica di errore).
*   **Caching:** Implementare una strategia di caching per le richieste GET.
*   **Trasformazione dei Dati:** Modificare il formato dei dati inviati o ricevuti (es. da snake_case a camelCase).
*   **Aggiunta di Header di Default:** Assicurare che certi header (es. `Content-Type: application/json`) siano sempre presenti.
*   **Fake Backend:** Simulare un backend per lo sviluppo o i test.

**1. Creare un Interceptor**

Puoi creare un interceptor usando Angular CLI o manualmente.

*   **Generare un Interceptor con Angular CLI:**
    ```bash
    ng generate interceptor nome-interceptor
    # Esempio: ng g interceptor auth
    ```
    Questo crea un file (es. `auth.interceptor.ts`) con la struttura base di un interceptor.

*   **Esempio: Un Interceptor per Aggiungere un Token di Autenticazione (`AuthInterceptor`)**

    ```typescript
    // src/app/interceptors/auth.interceptor.ts
    import { Injectable } from '@angular/core';
    import {
      HttpRequest,
      HttpHandler,
      HttpEvent,
      HttpInterceptor
    } from '@angular/common/http';
    import { Observable } from 'rxjs';
    // import { AuthService } from '../services/auth.service'; // Se il token viene da un servizio

    @Injectable()
    export class AuthInterceptor implements HttpInterceptor {

      constructor(/* private authService: AuthService */) {} // Inietta AuthService se necessario

      intercept(request: HttpRequest<unknown>, next: HttpHandler): Observable<HttpEvent<unknown>> {
        // Recupera il token di autenticazione (es. da localStorage o da un AuthService)
        const authToken = localStorage.getItem('authToken');
        // const authToken = this.authService.getToken(); // Se usi un servizio

        // Clona la richiesta per aggiungere il nuovo header.
        // Le richieste sono immutabili, quindi devi clonarle per modificarle.
        if (authToken) {
          // Solo se il token esiste e magari solo per certe URL (puoi aggiungere logica qui)
          // if (request.url.startsWith('https://api.miodominio.com/')) {
          const authReq = request.clone({
            headers: request.headers.set('Authorization', `Bearer ${authToken}`)
            // Oppure, se vuoi sovrascrivere tutti gli header:
            // setHeaders: {
            //   Authorization: `Bearer ${authToken}`
            // }
          });
          console.log('AuthInterceptor: Aggiunto header Authorization', authReq.headers.get('Authorization'));
          // Passa la richiesta clonata al prossimo handler nella catena.
          return next.handle(authReq);
          // }
        }

        // Se non c'è token o la richiesta non va modificata, passa la richiesta originale.
        console.log('AuthInterceptor: Nessun token, richiesta originale inoltrata.');
        return next.handle(request);
      }
    }
    ```

*   **Esempio: Un Interceptor per il Logging (`LoggingInterceptor`)**

    ```typescript
    // src/app/interceptors/logging.interceptor.ts
    import { Injectable } from '@angular/core';
    import {
      HttpRequest,
      HttpHandler,
      HttpEvent,
      HttpInterceptor,
      HttpResponse
    } from '@angular/common/http';
    import { Observable } from 'rxjs';
    import { tap } from 'rxjs/operators';

    @Injectable()
    export class LoggingInterceptor implements HttpInterceptor {

      constructor() {}

      intercept(request: HttpRequest<unknown>, next: HttpHandler): Observable<HttpEvent<unknown>> {
        const startTime = Date.now();
        let status: string;

        console.log(`---> LoggingInterceptor: Richiesta ${request.method} per ${request.urlWithParams}`);
        console.log('     Headers Richiesta:', request.headers);
        if (request.body) {
          console.log('     Corpo Richiesta:', request.body);
        }

        return next.handle(request).pipe(
          tap({
            next: (event) => {
              if (event instanceof HttpResponse) {
                status = 'successo';
                console.log(`<--- LoggingInterceptor: Risposta ${status} per ${request.urlWithParams} (status: ${event.status}) ricevuta in ${Date.now() - startTime}ms`);
                console.log('      Headers Risposta:', event.headers);
                console.log('      Corpo Risposta:', event.body);
              }
            },
            error: (error) => {
              status = 'fallito';
              console.error(`<--- LoggingInterceptor: Risposta ${status} per ${request.urlWithParams} (status: ${error.status}) ricevuta in ${Date.now() - startTime}ms`);
              console.error('      Errore:', error);
            }
          })
        );
      }
    }
    ```

**2. Fornire l'Interceptor**

Affinché il tuo interceptor venga utilizzato da `HttpClient`, devi registrarlo come provider speciale usando il token `HTTP_INTERCEPTORS` e specificando `multi: true` (perché possono esserci più interceptor).

*   **Approccio Moderno con Applicazioni Standalone (`app.config.ts`):**
    Usa `provideHttpClient(withInterceptors([...]))` per i nuovi intercettori funzionali, o `provideHttpClient(withInterceptorsFromDi())` e poi fornisci l'interceptor basato su classe separatamente.

    *   **Per Interceptor Funzionali (da Angular 15+):**
        Un interceptor funzionale è semplicemente una funzione che rispetta la firma `HttpInterceptorFn`.
        ```typescript
        // src/app/interceptors/functional-auth.interceptor.ts
        // import { HttpInterceptorFn, HttpRequest, HttpHandlerFn, HttpEvent } from '@angular/common/http';
        // import { Observable } from 'rxjs';
        // export const functionalAuthInterceptor: HttpInterceptorFn = (
        //   req: HttpRequest<unknown>,
        //   next: HttpHandlerFn
        // ): Observable<HttpEvent<unknown>> => {
        //   const authToken = localStorage.getItem('authToken');
        //   if (authToken) {
        //     const authReq = req.clone({
        //       headers: req.headers.set('Authorization', `Bearer ${authToken}`),
        //     });
        //     return next(authReq);
        //   }
        //   return next(req);
        // };

        // src/app/app.config.ts
        // import { provideHttpClient, withInterceptors } from '@angular/common/http';
        // import { functionalAuthInterceptor } from './interceptors/functional-auth.interceptor';
        // import { functionalLoggingInterceptor } from './interceptors/functional-logging.interceptor'; // Se ne hai uno
        //
        // export const appConfig: ApplicationConfig = {
        //   providers: [
        //     provideRouter(routes),
        //     provideHttpClient(
        //       withInterceptors([functionalAuthInterceptor, functionalLoggingInterceptor]) // Ordine importante!
        //     )
        //   ]
        // };
        ```
        Questo è l'approccio più recente e spesso più semplice.

    *   **Per Interceptor Basati su Classe (come quelli creati prima):**
        ```typescript
        // src/app/app.config.ts
        import { ApplicationConfig } from '@angular/core';
        import { provideRouter } from '@angular/router';
        import { provideHttpClient, withInterceptorsFromDi, HTTP_INTERCEPTORS } from '@angular/common/http';

        import { routes } from './app.routes';
        import { AuthInterceptor } from './interceptors/auth.interceptor';
        import { LoggingInterceptor } from './interceptors/logging.interceptor';

        export const appConfig: ApplicationConfig = {
          providers: [
            provideRouter(routes),
            provideHttpClient(withInterceptorsFromDi()), // Abilita DI per intercettori basati su classe
            {
              provide: HTTP_INTERCEPTORS,
              useClass: AuthInterceptor, // Il tuo interceptor basato su classe
              multi: true // Importante: permette più intercettori
            },
            {
              provide: HTTP_INTERCEPTORS,
              useClass: LoggingInterceptor,
              multi: true
            }
            // Altri provider...
          ]
        };
        ```

*   **Approccio più Vecchio con Applicazioni Basate su NgModule (`AppModule`):**
    Aggiungi un oggetto provider all'array `providers` del tuo `AppModule`.
    ```typescript
    // src/app/app.module.ts
    import { NgModule } from '@angular/core';
    import { BrowserModule } from '@angular/platform-browser';
    import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http'; // Importa HTTP_INTERCEPTORS

    import { AppComponent } from './app.component';
    import { AuthInterceptor } from './interceptors/auth.interceptor'; // Importa il tuo interceptor
    import { LoggingInterceptor } from './interceptors/logging.interceptor';
    // ...

    @NgModule({
      declarations: [AppComponent /*...*/],
      imports: [BrowserModule, HttpClientModule /*...*/],
      providers: [
        {
          provide: HTTP_INTERCEPTORS,
          useClass: AuthInterceptor,
          multi: true // Dice ad Angular che HTTP_INTERCEPTORS è un token per un array di provider
        },
        {
          provide: HTTP_INTERCEPTORS,
          useClass: LoggingInterceptor,
          multi: true
        }
        // L'ordine dei provider qui può essere importante per l'esecuzione degli intercettori
      ],
      bootstrap: [AppComponent]
    })
    export class AppModule {}
    ```

**3. Ordine degli Interceptors**

Se fornisci più interceptor, questi vengono eseguiti nell'**ordine in cui li hai forniti** nell'array dei provider (o nell'array passato a `withInterceptors`).

*   **Richiesta:** Interceptor1 -> Interceptor2 -> ... -> Backend
*   **Risposta:** Backend -> ... -> Interceptor2 -> Interceptor1

L'ordine è importante. Ad esempio, un interceptor di logging potrebbe voler loggare la richiesta sia prima che dopo che un interceptor di autenticazione ha aggiunto l'header.

**4. Modificare Richieste e Risposte**

*   **Modificare Richieste:**
    Poiché `HttpRequest` è immutabile, devi clonarla per apportare modifiche:
    ```typescript
    const modifiedReq = request.clone({
      headers: request.headers.set('X-Custom-Header', 'valore'),
      url: newUrl, // Puoi anche cambiare l'URL
      // body: nuovoCorpo, // Puoi cambiare il corpo (es. per trasformarlo)
      // params: nuoviParametri
    });
    return next.handle(modifiedReq);
    ```

*   **Modificare Risposte:**
    Puoi usare operatori RxJS (come `map`, `tap`) sull'Observable restituito da `next.handle(request)` per ispezionare o modificare la risposta. Devi assicurarti di intercettare solo `HttpResponse` se vuoi modificare il corpo.
    ```typescript
    // return next.handle(request).pipe(
    //   map(event => {
    //     if (event instanceof HttpResponse) {
    //       // Clona la risposta per modificarla (anche HttpResponse è spesso trattata come immutabile)
    //       const modifiedBody = event.body.data; // Esempio: se il corpo è { data: ... }
    //       return event.clone({ body: modifiedBody });
    //     }
    //     return event; // Restituisci altri eventi (es. HttpSentEvent) invariati
    //   })
    // );
    ```

**5. Gestione degli Errori con gli Interceptors**

Gli intercettori sono un ottimo posto per gestire gli errori HTTP globalmente.

```typescript
// error-handler.interceptor.ts (esempio)
// import { HttpInterceptor, HttpRequest, HttpHandler, HttpEvent, HttpErrorResponse } from '@angular/common/http';
// import { Observable, throwError } from 'rxjs';
// import { catchError, retry } from 'rxjs/operators';
// import { Router } from '@angular/router';

// @Injectable()
// export class ErrorHandlerInterceptor implements HttpInterceptor {
//   constructor(private router: Router) {}

//   intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
//     return next.handle(request).pipe(
//       retry(1), // Riprova le richieste fallite una volta
//       catchError((error: HttpErrorResponse) => {
//         let errorMessage = '';
//         if (error.status === 401) { // Unauthorized
//           errorMessage = 'Non autorizzato. Effettua nuovamente il login.';
//           this.router.navigate(['/login']); // Reindirizza al login
//         } else if (error.status === 404) {
//           errorMessage = 'Risorsa non trovata.';
//         } else {
//           errorMessage = `Errore ${error.status}: ${error.message}`;
//         }
//         console.error('ErrorHandlerInterceptor:', errorMessage, error);
//         // Puoi mostrare una notifica globale qui
//         // alert(errorMessage);
//         return throwError(() => new Error(errorMessage)); // Rilancia l'errore (o un nuovo errore)
//       })
//     );
//   }
// }

// E poi fornisci questo interceptor.
```

**Quando NON usare gli Interceptors:**

*   Per logica specifica di un singolo componente o servizio. In tal caso, gestisci la logica direttamente nel servizio o componente.
*   Se la trasformazione dei dati è molto specifica per un endpoint e non è una regola generale.

---

Gli Interceptors HTTP sono uno strumento potente e flessibile per gestire preoccupazioni trasversali nelle comunicazioni HTTP della tua applicazione Angular. Usali saggiamente per mantenere il codice dei tuoi servizi e componenti più pulito e focalizzato sulla loro logica di business specifica.

Questo conclude il Modulo 7 sulla Comunicazione con il Backend.
