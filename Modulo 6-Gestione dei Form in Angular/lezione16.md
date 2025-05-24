Certamente! Iniziamo il **Modulo 6: Gestione dei Form in Angular**. I form sono una parte essenziale di quasi tutte le applicazioni web per raccogliere input dall'utente. Angular offre due approcci principali per la gestione dei form: Template-Driven Forms e Reactive Forms.

Cominciamo con la **Lezione 16: Template-Driven Forms**.

---

### Modulo 6: Gestione dei Form in Angular

**Lezione 16: Template-Driven Forms**

I **Template-Driven Forms** sono un approccio alla gestione dei form in Angular in cui la logica, la struttura e la validazione del form sono definite principalmente nel **template HTML** del componente, utilizzando direttive specifiche di Angular.

Questo approccio è spesso più semplice da implementare per form di base e quando la logica del form non è eccessivamente complessa. Si basa molto sulla direttiva `ngModel` per il two-way data binding e su altre direttive per tracciare lo stato e la validità dei controlli del form.

**Caratteristiche Principali dei Template-Driven Forms:**

*   **Logica nel Template:** La maggior parte della definizione del form (controlli, validazione) risiede nel template HTML.
*   **Two-Way Data Binding:** Fa un uso intensivo di `[(ngModel)]` per sincronizzare i dati tra il modello del componente e gli input del form.
*   **Meno Codice TypeScript nel Componente:** La classe del componente contiene meno codice specifico per la gestione del form rispetto ai Reactive Forms.
*   **Asincrono (in una certa misura):** Poiché la costruzione del modello del form avviene tramite direttive nel template, il modello del form nel componente potrebbe non essere immediatamente disponibile dopo l'inizializzazione della vista.
*   **Più Facile per Form Semplici:** Generalmente considerato più veloce da implementare per form con requisiti di validazione semplici.

**1. Utilizzo di `FormsModule` e `ngForm`**

Per utilizzare i Template-Driven Forms, devi prima importare `FormsModule` (da `@angular/forms`) nel tuo componente standalone o nel `NgModule` appropriato.

*   **Importare `FormsModule`:**
    *   **Per componenti standalone:**
        ```typescript
        // tuo-componente.component.ts
        import { Component } from '@angular/core';
        import { FormsModule } from '@angular/forms'; // Importa FormsModule
        import { CommonModule } from '@angular/common'; // Spesso utile per *ngIf, ecc.

        @Component({
          selector: 'app-tuo-componente',
          standalone: true,
          imports: [FormsModule, CommonModule], // Aggiungi FormsModule agli imports
          templateUrl: './tuo-componente.component.html',
        })
        export class TuoComponente { /* ... */ }
        ```
    *   **Per componenti basati su NgModule:**
        ```typescript
        // tuo.module.ts
        import { NgModule } from '@angular/core';
        import { CommonModule } from '@angular/common';
        import { FormsModule } from '@angular/forms'; // Importa FormsModule
        import { TuoComponente } from './tuo-componente.component';

        @NgModule({
          declarations: [TuoComponente],
          imports: [
            CommonModule,
            FormsModule // Aggiungi FormsModule agli imports
          ]
        })
        export class TuoModule { }
        ```

*   **La Direttiva `ngForm`:**
    *   Quando importi `FormsModule`, Angular applica automaticamente la direttiva `ngForm` a qualsiasi tag `<form>` nel tuo template che non abbia già un binding a `formGroup` (usato nei Reactive Forms).
    *   `ngForm` crea un oggetto `FormGroup` dietro le quinte e lo associa al form. Questo `FormGroup` tiene traccia del valore complessivo del form e del suo stato di validità.
    *   Puoi ottenere un riferimento a questa istanza di `NgForm` nel template usando una variabile di riferimento locale:
        ```html
        <form #mioForm="ngForm" (ngSubmit)="onSubmit(mioForm)">
          <!-- Controlli del form qui -->
        </form>
        ```
        Qui, `mioForm` è una variabile che fa riferimento all'istanza della direttiva `NgForm`. Puoi passarla al metodo `onSubmit` del tuo componente.

**2. Binding con `ngModel`**

La direttiva `ngModel` è il cuore del data binding nei Template-Driven Forms. Usata con la sintassi del two-way data binding `[(ngModel)]`, sincronizza il valore di un controllo di input con una proprietà nel tuo componente.

*   **Requisiti per `ngModel`:**
    1.  Deve essere usato su un elemento di form (come `<input>`, `<select>`, `<textarea>`).
    2.  L'elemento deve avere un attributo `name`. Angular usa l'attributo `name` per registrare il controllo con la direttiva `ngForm` (il `FormGroup` genitore).

    ```html
    <!-- registration-form.component.html -->
    <form #registrationForm="ngForm" (ngSubmit)="onSubmit(registrationForm)">
      <div>
        <label for="username">Nome Utente:</label>
        <input
          type="text"
          id="username"
          name="username"                           ngModel
          [(ngModel)]="user.username"
          required                                 <!-- Validazione HTML5/Angular -->
          minlength="3"                            <!-- Validazione Angular -->
          #usernameCtrl="ngModel">                 <!-- Riferimento locale al controllo NgModel -->

        <!-- Messaggi di errore per usernameCtrl -->
        <div *ngIf="usernameCtrl.invalid && (usernameCtrl.dirty || usernameCtrl.touched)" class="error-messages">
          <div *ngIf="usernameCtrl.errors?.['required']">
            Il nome utente è obbligatorio.
          </div>
          <div *ngIf="usernameCtrl.errors?.['minlength']">
            Il nome utente deve avere almeno {{ usernameCtrl.errors?.['minlength'].requiredLength }} caratteri.
          </div>
        </div>
      </div>

      <div>
        <label for="email">Email:</label>
        <input
          type="email"
          id="email"
          name="email"
          [(ngModel)]="user.email"
          required
          email                                    <!-- Validazione Angular per email -->
          #emailCtrl="ngModel">

        <div *ngIf="emailCtrl.invalid && (emailCtrl.dirty || emailCtrl.touched)" class="error-messages">
          <div *ngIf="emailCtrl.errors?.['required']">
            L'email è obbligatoria.
          </div>
          <div *ngIf="emailCtrl.errors?.['email']">
            Inserisci un indirizzo email valido.
          </div>
        </div>
      </div>

      <div>
        <label for="password">Password:</label>
        <input
          type="password"
          id="password"
          name="password"
          [(ngModel)]="user.password"
          required
          minlength="6"
          #passwordCtrl="ngModel">

        <div *ngIf="passwordCtrl.invalid && (passwordCtrl.dirty || passwordCtrl.touched)" class="error-messages">
          <!-- ... messaggi di errore per la password ... -->
        </div>
      </div>

      <button type="submit" [disabled]="registrationForm.invalid">Registrati</button>
    </form>

    <div *ngIf="formSubmittedData">
      <h3>Dati Inviati:</h3>
      <pre>{{ formSubmittedData | json }}</pre>
    </div>
    ```

    ```typescript
    // registration-form.component.ts
    import { Component } from '@angular/core';
    import { NgForm } from '@angular/forms'; // Importa NgForm
    import { CommonModule } from '@angular/common';
    import { FormsModule } from '@angular/forms';

    interface User {
      username?: string;
      email?: string;
      password?: string;
    }

    @Component({
      selector: 'app-registration-form',
      standalone: true,
      imports: [CommonModule, FormsModule],
      templateUrl: './registration-form.component.html',
      styleUrls: ['./registration-form.component.css'] // Aggiungi un po' di stile per gli errori
    })
    export class RegistrationFormComponent {
      user: User = {}; // Oggetto modello per i dati del form
      formSubmittedData: any = null;

      constructor() {}

      onSubmit(form: NgForm): void {
        console.log('Form inviato:', form);
        console.log('Valori del form:', form.value); // Oggetto con i valori del form
        console.log('Validità del form:', form.valid);
        console.log('Stato del form:', form.status); // 'VALID', 'INVALID', 'PENDING', 'DISABLED'

        if (form.valid) {
          this.formSubmittedData = { ...form.value }; // Crea una copia dei valori
          // Qui invieresti i dati al backend
          console.log('Dati utente da inviare:', this.user);
          // form.reset(); // Resetta il form dopo l'invio (opzionale)
        } else {
          console.error('Il form contiene errori!');
          // Potresti marcare tutti i controlli come 'touched' per mostrare tutti gli errori
          Object.keys(form.controls).forEach(field => {
            const control = form.controls[field];
            control.markAsTouched({ onlySelf: true });
          });
        }
      }
    }
    ```
    ```css
    /* registration-form.component.css (esempio) */
    .error-messages {
      color: red;
      font-size: 0.9em;
      margin-top: 5px;
    }

    input.ng-invalid.ng-touched, /* Stile per input invalidi che sono stati toccati */
    input.ng-invalid.ng-dirty {   /* o modificati (dirty) */
      border: 1px solid red;
    }
    ```

**3. Validazione di Base (required, minlength, maxlength, pattern, email)**

Angular estende gli attributi di validazione HTML5 standard e ne fornisce di propri. Quando usi questi attributi su un elemento con `ngModel` e `name`, Angular li riconosce e aggiorna lo stato di validità del controllo e del form.

*   **Attributi di Validazione Comuni:**
    *   `required`: Il campo non può essere vuoto.
    *   `minlength="x"`: La stringa deve avere almeno `x` caratteri.
    *   `maxlength="y"`: La stringa non può avere più di `y` caratteri (questo è più un aiuto all'input, la validazione della lunghezza massima effettiva si fa con `Validators.maxLength` nei Reactive Forms o con un validatore personalizzato).
    *   `pattern="regex"`: Il valore deve corrispondere all'espressione regolare specificata.
    *   `email`: Il valore deve essere un indirizzo email formattato correttamente (Angular aggiunge una sua direttiva di validazione `email`).
    *   `min="n"`, `max="m"`: Per input di tipo `number`.

*   **Direttive di Validazione:**
    Questi attributi vengono interpretati da direttive di validazione fornite da `FormsModule` (es. `RequiredValidator`, `MinLengthValidator`, `EmailValidator`).

**4. Visualizzazione dei Messaggi di Errore e Stati del Form/Controllo**

Angular traccia lo stato di ogni controllo del form e del form nel suo complesso. Puoi usare questi stati per mostrare messaggi di errore condizionali o per stilare gli input.

*   **Stati di un Controllo (es. `usernameCtrl` nel nostro esempio):**
    Ottenuti da una variabile di riferimento locale associata a `ngModel` (es. `#usernameCtrl="ngModel"`).
    *   `usernameCtrl.valid`: `true` se il controllo è valido.
    *   `usernameCtrl.invalid`: `true` se il controllo è invalido.
    *   `usernameCtrl.pending`: `true` se ci sono validatori asincroni in esecuzione.
    *   `usernameCtrl.disabled`: `true` se il controllo è disabilitato.
    *   `usernameCtrl.enabled`: `true` se il controllo è abilitato.
    *   `usernameCtrl.pristine`: `true` se l'utente non ha ancora modificato il valore del controllo.
    *   `usernameCtrl.dirty`: `true` se l'utente ha modificato il valore del controllo.
    *   `usernameCtrl.untouched`: `true` se l'utente non ha ancora interagito (focus e blur) con il controllo.
    *   `usernameCtrl.touched`: `true` se l'utente ha interagito con il controllo (ha ricevuto il focus e poi lo ha perso).
    *   `usernameCtrl.errors`: Un oggetto che contiene le violazioni di validazione. Le chiavi sono i nomi degli errori (es. `'required'`, `'minlength'`, `'email'`) e i valori possono contenere dettagli sull'errore (es. per `minlength`, contiene `{ requiredLength: 3, actualLength: 1 }`). Se non ci sono errori, è `null`.

*   **Stati del Form Complessivo (es. `registrationForm`):**
    Ottenuti dalla variabile di riferimento locale associata a `ngForm` (es. `#registrationForm="ngForm"`).
    *   Hanno proprietà simili a quelle dei singoli controlli (`valid`, `invalid`, `pristine`, `dirty`, `touched`, `untouched`, `errors`).
    *   `registrationForm.value`: Un oggetto che contiene i valori di tutti i controlli nel form, con le chiavi corrispondenti agli attributi `name` degli input.
    *   `registrationForm.controls`: Un oggetto che contiene le istanze `FormControl` per ogni input nel form.

*   **Mostrare Messaggi di Errore:**
    Tipicamente, mostri un messaggio di errore quando un controllo è `invalid` E l'utente ha interagito con esso (`touched` o `dirty`). Questo evita di mostrare errori prima che l'utente abbia avuto la possibilità di inserire dati.
    ```html
    <div *ngIf="usernameCtrl.invalid && (usernameCtrl.dirty || usernameCtrl.touched)" class="error-messages">
      <div *ngIf="usernameCtrl.errors?.['required']">
        Il nome utente è obbligatorio.
      </div>
      <div *ngIf="usernameCtrl.errors?.['minlength']">
        Deve avere almeno {{ usernameCtrl.errors?.['minlength'].requiredLength }} caratteri.
      </div>
    </div>
    ```

*   **Classi CSS di Stato:**
    Angular aggiunge automaticamente classi CSS agli elementi di input in base al loro stato, che puoi usare per lo styling:
    *   `ng-valid` / `ng-invalid`
    *   `ng-pending`
    *   `ng-pristine` / `ng-dirty`
    *   `ng-untouched` / `ng-touched`
    Esempio di styling nel CSS:
    ```css
    input.ng-invalid.ng-touched {
      border-color: red;
    }
    input.ng-valid.ng-touched {
      border-color: green;
    }
    ```

**Gestione dell'Invio (Submit) del Form:**

*   Usa l'evento `(ngSubmit)` sul tag `<form>`. Questo evento viene scatenato quando il form viene inviato (es. cliccando un bottone `type="submit"` o premendo Invio in un campo di testo).
*   Evita di usare l'evento `(click)` su un bottone di submit per inviare il form, perché `(ngSubmit)` gestisce anche l'invio tramite tasto Invio e previene il comportamento di submit predefinito del browser (che causerebbe un ricaricamento della pagina).
*   Nel metodo handler di `(ngSubmit)` (es. `onSubmit(form: NgForm)`), puoi accedere ai valori e allo stato del form.

**Reset del Form:**
La direttiva `NgForm` ha un metodo `reset()` che puoi chiamare per resettare i valori e lo stato del form.
```typescript
onSubmit(form: NgForm): void {
  if (form.valid) {
    // ... invia dati ...
    form.reset(); // Resetta tutti i campi ai loro valori iniziali (o a null) e lo stato a pristine/untouched
  }
}
```
Puoi anche passare un oggetto a `form.reset({ username: 'valorePredefinito' })` per resettare i campi a valori specifici.

**Quando Usare i Template-Driven Forms:**

*   Per form semplici con logica di validazione minima.
*   Quando vuoi una configurazione rapida e la maggior parte della logica può risiedere nel template.
*   Se preferisci lavorare principalmente con direttive e data binding nel template.
*   In scenari di migrazione da AngularJS dove l'approccio è simile.

Per form più complessi, con validazioni dinamiche, validatori personalizzati complessi, o quando hai bisogno di un maggiore controllo programmatico sul modello del form e sulla sua reattività, i **Reactive Forms** (che vedremo nella prossima lezione) sono generalmente una scelta migliore.

---

I Template-Driven Forms offrono un modo dichiarativo e relativamente semplice per gestire i form in Angular. Comprendere `FormsModule`, `ngForm`, `ngModel` e gli stati dei controlli è la chiave per utilizzarli efficacemente.

Nella prossima lezione, esploreremo i **Reactive Forms**, un approccio più programmatico e potente alla gestione dei form.
