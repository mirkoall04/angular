Certamente! Iniziamo il **Modulo 7: Comunicazione con il Backend (HTTP)**. La maggior parte delle applicazioni web moderne ha bisogno di interagire con un server backend per recuperare, inviare, aggiornare o eliminare dati. Angular fornisce il modulo `HttpClientModule` e il servizio `HttpClient` per facilitare queste operazioni.

---

### Modulo 7: Comunicazione con il Backend (HTTP)

**Lezione 18: Introduzione a `HttpClientModule` e `HttpClient`**

Per comunicare con server esterni tramite il protocollo HTTP (HyperText Transfer Protocol), Angular offre un client HTTP robusto e facile da usare. Questo client è basato sugli **Observables** di RxJS per gestire le richieste e le risposte asincrone.

**1. Importare `HttpClientModule` (Approccio più Vecchio con NgModule) o `provideHttpClient` (Approccio Moderno Standalone)**

Per rendere disponibile il servizio `HttpClient` nella tua applicazione, devi configurarlo a livello radice.

*   **Approccio Moderno con Applicazioni Standalone (da Angular 14+, default da v17):**
    Usa la funzione `provideHttpClient()` nel file di configurazione dell'applicazione (tipicamente `app.config.ts`). Questa è la maniera raccomandata e tree-shakable.
    ```typescript
    // src/app/app.config.ts
    import { ApplicationConfig } from '@angular/core';
    import { provideRouter } from '@angular/router';
    import { provideHttpClient, withInterceptorsFromDi } from '@angular/common/http'; // Importa provideHttpClient
    // withInterceptorsFromDi è spesso necessario se usi intercettori basati su classe.
    // Per i nuovi intercettori funzionali, potrebbe non servire o si usa withInterceptors([...]).

    import { routes } from './app.routes';

    export const appConfig: ApplicationConfig = {
      providers: [
        provideRouter(routes),
        provideHttpClient(withInterceptorsFromDi()) // Fornisce HttpClient e configura per intercettori DI
        // Per intercettori funzionali (più recenti):
        // provideHttpClient(withInterceptors([myFunctionalInterceptor]))
      ]
    };
    ```
    La funzione `withInterceptorsFromDi()` abilita l'uso di intercettori HTTP basati su classi che sono forniti tramite Dependency Injection. Se usi i nuovi intercettori funzionali (da Angular 15+), li passi direttamente a `withInterceptors([...])`.

*   **Approccio più Vecchio con Applicazioni Basate su NgModule:**
    Importa `HttpClientModule` (da `@angular/common/http`) una sola volta nel tuo modulo radice (tipicamente `AppModule`).
    ```typescript
    // src/app/app.module.ts
    import { NgModule } from '@angular/core';
    import { BrowserModule } from '@angular/platform-browser';
    import { HttpClientModule } from '@angular/common/http'; // Importa HttpClientModule

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    // ... altri import

    @NgModule({
      declarations: [
        AppComponent,
        // ... altri componenti
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        HttpClientModule // Aggiungi HttpClientModule agli imports
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
    ```
    **Importante:** `HttpClientModule` dovrebbe essere importato solo nel modulo radice (`AppModule`) e non nei moduli lazy-loaded. I servizi forniti da `HttpClientModule` (come `HttpClient`) sono pensati per essere singleton a livello di applicazione.

**2. Utilizzare il servizio `HttpClient`**

Una volta che `HttpClientModule` (o `provideHttpClient`) è configurato, puoi iniettare il servizio `HttpClient` in qualsiasi tuo servizio o componente per effettuare richieste HTTP.

*   **Iniettare `HttpClient`:**
    ```typescript
    // data.service.ts
    import { Injectable } from '@angular/core';
    import { HttpClient, HttpHeaders, HttpParams, HttpErrorResponse } from '@angular/common/http'; // Importa HttpClient e altre classi utili
    import { Observable, throwError } from 'rxjs';
    import { catchError, map, tap } from 'rxjs/operators';

    export interface Post { // Interfaccia per i dati che ci aspettiamo (esempio)
      userId: number;
      id: number;
      title: string;
      body: string;
    }

    @Injectable({
      providedIn: 'root'
    })
    export class DataService {
      private apiUrl = 'https://jsonplaceholder.typicode.com'; // URL di un'API di esempio

      constructor(private http: HttpClient) {} // Inietta HttpClient
    ```

**3. Effettuare Richieste GET, POST, PUT, DELETE**

Il servizio `HttpClient` offre metodi per tutti i verbi HTTP comuni. Questi metodi restituiscono un `Observable` a cui ti puoi sottoscrivere per ricevere la risposta (o gestire gli errori).

*   **Richiesta GET (Recuperare Dati):**
    Usato per richiedere dati da una risorsa specificata.
    ```typescript
    // data.service.ts (continua)

    // GET di una lista di post
    getPosts(): Observable<Post[]> {
      return this.http.get<Post[]>(`${this.apiUrl}/posts`).pipe(
        tap(posts => console.log('Post recuperati:', posts)), // Operatore per side-effect (es. logging)
        catchError(this.handleError) // Gestione centralizzata degli errori
      );
    }

    // GET di un singolo post per ID
    getPostById(id: number): Observable<Post> {
      // Costruire URL con parametri
      // let params = new HttpParams().set('userId', '1'); // Esempio di HttpParams
      // return this.http.get<Post>(`${this.apiUrl}/posts/${id}`, { params: params });

      return this.http.get<Post>(`${this.apiUrl}/posts/${id}`).pipe(
        tap(post => console.log(`Post recuperato con ID ${id}:`, post)),
        catchError(this.handleError)
      );
    }
    ```
    *   `this.http.get<Post[]>(url)`:
        *   Il primo argomento è l'URL della risorsa.
        *   Il tipo generico `<Post[]>` (o `<Post>`) dice a `HttpClient` che tipo di dati ci aspettiamo nella risposta. `HttpClient` tenterà di parsare il corpo della risposta JSON in questo tipo. Questo fornisce type checking e autocompletamento.
    *   `.pipe()`: Usato per concatenare operatori RxJS per manipolare l'Observable (es. `tap` per logging, `catchError` per la gestione degli errori, `map` per trasformare i dati).

*   **Richiesta POST (Inviare Dati per Creare una Nuova Risorsa):**
    Usato per inviare dati al server, tipicamente per creare una nuova entità.
    ```typescript
    // data.service.ts (continua)

    createPost(newPost: Omit<Post, 'id'>): Observable<Post> { // Omit<Post, 'id'> perché l'ID viene solitamente assegnato dal server
      const httpOptions = {
        headers: new HttpHeaders({
          'Content-Type':  'application/json; charset=UTF-8'
          // 'Authorization': 'my-auth-token' // Esempio di header di autorizzazione
        })
      };

      return this.http.post<Post>(`${this.apiUrl}/posts`, newPost, httpOptions).pipe(
        tap(createdPost => console.log('Post creato:', createdPost)),
        catchError(this.handleError)
      );
    }
    ```
    *   `this.http.post<Post>(url, body, options)`:
        *   `url`: L'URL a cui inviare i dati.
        *   `body`: I dati da inviare nel corpo della richiesta (es. `newPost`). `HttpClient` serializzerà automaticamente gli oggetti JavaScript in JSON se l'header `Content-Type` è `application/json`.
        *   `options` (opzionale): Un oggetto che può contenere `headers`, `params`, `observe`, `responseType`, ecc.
            *   `HttpHeaders`: Per impostare header HTTP personalizzati.

*   **Richiesta PUT (Inviare Dati per Aggiornare una Risorsa Esistente):**
    Usato per aggiornare completamente una risorsa esistente. Se la risorsa non esiste, alcune API potrebbero crearla.
    ```typescript
    // data.service.ts (continua)

    updatePost(postId: number, updatedPostData: Partial<Post>): Observable<Post> {
      const httpOptions = {
        headers: new HttpHeaders({ 'Content-Type': 'application/json; charset=UTF-8' })
      };
      return this.http.put<Post>(`${this.apiUrl}/posts/${postId}`, updatedPostData, httpOptions).pipe(
        tap(updatedPost => console.log(`Post ${postId} aggiornato:`, updatedPost)),
        catchError(this.handleError)
      );
    }
    ```
    *   `this.http.put<Post>(url, body, options)`: Simile a `POST`.

*   **Richiesta DELETE (Eliminare una Risorsa):**
    Usato per eliminare una risorsa specificata.
    ```typescript
    // data.service.ts (continua)

    deletePost(postId: number): Observable<{}> { // Spesso DELETE non restituisce un corpo significativo, quindi si usa {} o unknown
      return this.http.delete<{}>(`${this.apiUrl}/posts/${postId}`).pipe(
        tap(() => console.log(`Post ${postId} eliminato`)),
        catchError(this.handleError)
      );
    }
    ```
    *   `this.http.delete<ResponseType>(url, options)`: L'URL tipicamente include l'ID della risorsa da eliminare.

*   **Gestione degli Errori (Esempio Semplice):**
    È fondamentale gestire gli errori che possono verificarsi durante le chiamate HTTP. L'operatore `catchError` di RxJS è usato per questo.
    ```typescript
    // data.service.ts (continua)

    private handleError(error: HttpErrorResponse) {
      let errorMessage = 'Si è verificato un errore sconosciuto!';
      if (error.error instanceof ErrorEvent) {
        // Errore lato client o di rete
        errorMessage = `Errore client: ${error.error.message}`;
      } else {
        // Il backend ha restituito un codice di errore
        // Il corpo della risposta potrebbe contenere indizi sull'errore
        errorMessage = `Codice errore server: ${error.status}\nMessaggio: ${error.message}`;
        if (error.error && typeof error.error === 'object') {
            errorMessage += `\nDettagli: ${JSON.stringify(error.error)}`;
        }
      }
      console.error(errorMessage);
      // Restituisci un observable con un messaggio di errore user-friendly
      // o rilancia l'errore per essere gestito dal componente che ha chiamato il servizio.
      return throwError(() => new Error(errorMessage));
    }
    ```
    Il metodo `handleError` viene passato a `catchError`. `throwError` (da RxJS) crea un nuovo Observable che emette immediatamente un errore.

**Opzioni delle Richieste HTTP:**

L'oggetto `options` che puoi passare ai metodi di `HttpClient` (GET, POST, ecc.) permette di configurare vari aspetti della richiesta:

*   **`headers: HttpHeaders`**: Per impostare header HTTP (es. `Content-Type`, `Authorization`).
*   **`params: HttpParams`**: Per aggiungere parametri di query all'URL (es. `/search?query=term`).
    ```typescript
    // let params = new HttpParams().set('page', '1').set('pageSize', '10');
    // this.http.get('/api/items', { params: params });
    ```
*   **`observe`**: Specifica quanta parte della risposta vuoi.
    *   `'body'` (default): Restituisce solo il corpo della risposta parsato.
    *   `'events'`: Restituisce tutti gli `HttpEvent`, inclusi eventi di progresso dell'upload/download (utile per barre di progresso).
    *   `'response'`: Restituisce l'intera `HttpResponse`, inclusi header, stato, e corpo.
    ```typescript
    // this.http.get<Post>(url, { observe: 'response' }).subscribe(response => {
    //   console.log(response.status);
    //   console.log(response.headers.get('X-Custom-Header'));
    //   console.log(response.body); // Il corpo parsato
    // });
    ```
*   **`responseType`**: Specifica il formato in cui vuoi il corpo della risposta.
    *   `'json'` (default): Parsa il corpo come JSON.
    *   `'text'`: Restituisce il corpo come stringa.
    *   `'blob'`: Per dati binari (es. download di file).
    *   `'arraybuffer'`: Per dati binari generici.
*   **`reportProgress: boolean`**: Se `true` (e `observe: 'events'`), emette eventi di progresso.
*   **`withCredentials: boolean`**: Indica se le richieste cross-site dovrebbero essere fatte usando credenziali come cookie o header di autorizzazione.

**Utilizzare i Dati Recuperati in un Componente:**

Un componente inietterà `DataService` e si sottoscriverà agli Observables restituiti dai suoi metodi.

```typescript
// post-list.component.ts
import { Component, OnInit, OnDestroy } from '@angular/core';
import { CommonModule } from '@angular/common';
import { Subscription } from 'rxjs';
import { DataService, Post } from '../services/data.service'; // Importa il servizio e l'interfaccia

@Component({
  selector: 'app-post-list',
  standalone: true,
  imports: [CommonModule], // Per *ngFor, *ngIf
  template: `
    <h2>Lista dei Post</h2>
    <div *ngIf="isLoading" class="loading">Caricamento post...</div>
    <ul *ngIf="!isLoading && posts.length > 0">
      <li *ngFor="let post of posts">
        <h3>{{ post.title }} (ID: {{ post.id }})</h3>
        <p>{{ post.body }}</p>
        <button (click)="deletePost(post.id)">Elimina</button>
      </li>
    </ul>
    <p *ngIf="!isLoading && posts.length === 0 && !errorMessage">Nessun post da visualizzare.</p>
    <div *ngIf="errorMessage" class="error">{{ errorMessage }}</div>

    <div>
      <h3>Crea Nuovo Post</h3>
      <input #titleInput type="text" placeholder="Titolo">
      <textarea #bodyInput placeholder="Corpo"></textarea>
      <button (click)="createPost(titleInput.value, bodyInput.value)">Crea Post</button>
    </div>
  `
})
export class PostListComponent implements OnInit, OnDestroy {
  posts: Post[] = [];
  isLoading: boolean = false;
  errorMessage: string | null = null;
  private postsSubscription: Subscription;
  private createSubscription: Subscription;
  private deleteSubscription: Subscription;

  constructor(private dataService: DataService) {}

  ngOnInit(): void {
    this.loadPosts();
  }

  loadPosts(): void {
    this.isLoading = true;
    this.errorMessage = null;
    this.postsSubscription = this.dataService.getPosts().subscribe({
      next: (data) => {
        this.posts = data;
        this.isLoading = false;
      },
      error: (err) => {
        this.errorMessage = `Errore nel caricamento dei post: ${err.message || 'Errore sconosciuto'}`;
        this.isLoading = false;
        console.error(err);
      },
      complete: () => console.log('Caricamento post completato')
    });
  }

  createPost(title: string, body: string): void {
    if (!title || !body) {
      alert('Titolo e corpo sono obbligatori!');
      return;
    }
    const newPostData = { userId: 1, title, body }; // userId fittizio
    this.createSubscription = this.dataService.createPost(newPostData).subscribe({
      next: (createdPost) => {
        this.posts.unshift(createdPost); // Aggiunge il nuovo post all'inizio della lista
        alert('Post creato con successo!');
      },
      error: (err) => alert(`Errore nella creazione del post: ${err.message}`)
    });
  }

  deletePost(postId: number): void {
    if (confirm(`Sei sicuro di voler eliminare il post con ID ${postId}?`)) {
      this.deleteSubscription = this.dataService.deletePost(postId).subscribe({
        next: () => {
          this.posts = this.posts.filter(post => post.id !== postId);
          alert('Post eliminato con successo!');
        },
        error: (err) => alert(`Errore nell'eliminazione del post: ${err.message}`)
      });
    }
  }

  ngOnDestroy(): void {
    // Fondamentale fare l'unsubscribe per evitare memory leak
    if (this.postsSubscription) {
      this.postsSubscription.unsubscribe();
    }
    if (this.createSubscription) {
      this.createSubscription.unsubscribe();
    }
    if (this.deleteSubscription) {
      this.deleteSubscription.unsubscribe();
    }
  }
}
```
Ricorda di creare questo `PostListComponent` (es. `ng g c post-list`) e di aggiungerlo alle rotte o al template di un altro componente per visualizzarlo.

---

`HttpClientModule` e `HttpClient` sono gli strumenti principali in Angular per tutte le comunicazioni HTTP. L'uso di tipi generici per le risposte e la gestione degli errori tramite operatori RxJS come `catchError` sono pratiche chiave.

Nella prossima lezione, "Gestione delle Risposte e degli Errori HTTP", approfondiremo ulteriormente come lavorare con gli `Observable` restituiti da `HttpClient` e come gestire in modo più robusto le risposte e gli errori.
