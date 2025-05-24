Perfetto! Passiamo ora alla **Lezione 6: Template Syntax - Direttive Strutturali**. Queste direttive sono fondamentali per rendere i tuoi template dinamici, aggiungendo o rimuovendo elementi dal DOM in base a determinate condizioni o iterando su collezioni di dati.

---

### Modulo 2: Componenti Angular: Il Cuore dell'Applicazione

**Lezione 6: Template Syntax - Direttive Strutturali**

Le **Direttive Strutturali** sono responsabili della modifica della struttura del DOM. Lo fanno aggiungendo, rimuovendo o manipolando elementi HTML a cui sono associate. Sono facili da riconoscere nei template Angular perché la loro sintassi è preceduta da un asterisco (`*`), ad esempio `*ngIf`, `*ngFor`.

L'asterisco è una "scorciatoia sintattica" (syntactic sugar). Dietro le quinte, Angular trasforma questa sintassi in un tag `<ng-template>` che avvolge l'elemento host. Questo `<ng-template>` contiene la struttura che può essere renderizzata o meno dalla direttiva.

Le direttive strutturali più comuni e importanti sono:

*   `*ngIf`: Aggiunge o rimuove condizionalmente un elemento dal DOM.
*   `*ngFor`: Ripete un elemento del template per ogni item in una collezione.
*   `ngSwitch` (con `*ngSwitchCase` e `*ngSwitchDefault`): Simile a uno `switch` statement JavaScript, mostra condizionalmente uno tra diversi elementi.

**1. `*ngIf`: Aggiungere o Rimuovere Elementi Condizionalmente**

La direttiva `*ngIf` aggiunge un elemento (e i suoi figli) al DOM se l'espressione a cui è associata valuta a `true`, altrimenti lo rimuove dal DOM. Se l'espressione diventa `false` dopo che l'elemento è stato aggiunto, l'elemento viene rimosso.

*   **Sintassi di base:** `*ngIf="condizione"`

```typescript
// my-conditional.component.ts
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common'; // Necessario per *ngIf, *ngFor, etc., in componenti standalone

@Component({
  selector: 'app-my-conditional',
  standalone: true,
  imports: [CommonModule], // Importa CommonModule
  template: `
    <h2>Direttiva *ngIf</h2>

    <button (click)="toggleShowElement()">
      {{ showElement ? 'Nascondi' : 'Mostra' }} Elemento
    </button>

    <div *ngIf="showElement" class="box">
      <p>Questo elemento è visibile perché 'showElement' è true.</p>
    </div>

    <hr>

    <h3>*ngIf con 'else'</h3>
    <button (click)="login()">Login</button>
    <button (click)="logout()">Logout</button>

    <div *ngIf="isLoggedIn; else loggedOutContent" class="box">
      <p>Benvenuto utente!</p>
    </div>

    <!-- Blocco 'else' definito con un <ng-template> e una variabile di riferimento locale (#loggedOutContent) -->
    <ng-template #loggedOutContent>
      <div class="box-else">
        <p>Per favore, effettua il login per vedere il contenuto.</p>
      </div>
    </ng-template>
  `
})
export class MyConditionalComponent {
  showElement: boolean = true;
  isLoggedIn: boolean = false;

  toggleShowElement(): void {
    this.showElement = !this.showElement;
  }

  login(): void {
    this.isLoggedIn = true;
  }

  logout(): void {
    this.isLoggedIn = false;
  }
}
```

*   **Spiegazione:**
    *   Nel primo esempio, il `div` con classe `box` viene aggiunto o rimosso dal DOM quando `showElement` cambia. È importante notare che l'elemento non viene solo nascosto con CSS (`display: none`), ma **rimosso completamente dal DOM** e ricreato se la condizione torna `true`. Questo ha implicazioni sulle performance e sullo stato degli elementi figli.
    *   Nel secondo esempio, usiamo la clausola `else`. Se `isLoggedIn` è `true`, viene mostrato il primo `div`. Altrimenti (`else`), viene renderizzato il contenuto del `<ng-template>` referenziato da `#loggedOutContent`. L'uso di `<ng-template>` è il modo standard per definire blocchi di contenuto che `*ngIf` può usare.

*   **`*ngIf` e `then`:**
    Similmente a `else`, puoi usare `then` per specificare un template da usare quando la condizione è `true`. Questo può essere utile per logiche più complesse, anche se la sintassi con `else` è più comune.
    ```html
    <div *ngIf="condition; then thenBlock else elseBlock"></div>
    <ng-template #thenBlock>Contenuto se true.</ng-template>
    <ng-template #elseBlock>Contenuto se false.</ng-template>
    ```

**2. `*ngFor`: Iterare su Collezioni**

La direttiva `*ngFor` è usata per renderizzare una lista di elementi. Itera su una collezione (tipicamente un array o un `Iterable`) e istanzia il template a cui è applicata per ogni elemento della collezione.

*   **Sintassi di base:** `*ngFor="let item of items"`

```typescript
// my-list.component.ts
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common'; // Necessario

interface Product {
  id: number;
  name: string;
  price: number;
}

@Component({
  selector: 'app-my-list',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h2>Direttiva *ngFor</h2>

    <h3>Lista Semplice di Nomi:</h3>
    <ul>
      <li *ngFor="let name of userNames">
        {{ name }}
      </li>
    </ul>

    <h3>Lista di Oggetti (Prodotti):</h3>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Nome Prodotto</th>
          <th>Prezzo</th>
          <th>Indice</th>
          <th>Primo?</th>
          <th>Ultimo?</th>
          <th>Pari?</th>
          <th>Dispari?</th>
        </tr>
      </thead>
      <tbody>
        <tr *ngFor="let product of products; let i = index; let first = first; let last = last; let even = even; let odd = odd; trackBy: trackByProductId">
          <td>{{ product.id }}</td>
          <td>{{ product.name }}</td>
          <td>{{ product.price }} €</td>
          <td>{{ i }}</td>
          <td>{{ first ? 'Sì' : 'No' }}</td>
          <td>{{ last ? 'Sì' : 'No' }}</td>
          <td>{{ even ? 'Sì' : 'No' }}</td>
          <td>{{ odd ? 'Sì' : 'No' }}</td>
        </tr>
      </tbody>
    </table>

    <p *ngIf="products.length === 0">Nessun prodotto da visualizzare.</p>
  `
})
export class MyListComponent {
  userNames: string[] = ['Alice', 'Bob', 'Charlie', 'Diana'];
  products: Product[] = [
    { id: 1, name: 'Laptop Pro', price: 1200 },
    { id: 2, name: 'Mouse Wireless', price: 25 },
    { id: 3, name: 'Tastiera Meccanica', price: 80 },
    { id: 4, name: 'Monitor 4K', price: 350 }
  ];

  // Funzione trackBy per migliorare le performance
  trackByProductId(index: number, product: Product): number {
    return product.id; // Usa l'ID univoco del prodotto
  }
}
```

*   **Spiegazione:**
    *   `let item of items`: Per ogni `item` nella collezione `items`, il template dell'elemento `<li>` (o `<tr>`) viene ripetuto. Puoi accedere all'oggetto corrente dell'iterazione usando la variabile `item` (nel nostro caso `name` o `product`).
    *   **Variabili Locali Esportate:** `*ngFor` fornisce diverse utili variabili locali che puoi "esportare" usando `let varName = exportedValue`:
        *   `index: number`: L'indice dell'elemento corrente nell'iterazione (parte da 0).
        *   `count: number`: La lunghezza totale della collezione.
        *   `first: boolean`: `true` se l'elemento corrente è il primo della collezione.
        *   `last: boolean`: `true` se l'elemento corrente è l'ultimo della collezione.
        *   `even: boolean`: `true` se l'indice dell'elemento corrente è pari.
        *   `odd: boolean`: `true` se l'indice dell'elemento corrente è dispari.
    *   **`trackBy`:**
        *   Quando la collezione `items` cambia (elementi aggiunti, rimossi o riordinati), Angular per default ri-renderizza l'intera lista nel DOM. Questo può essere inefficiente per liste lunghe.
        *   La funzione `trackBy` permette ad Angular di identificare univocamente gli elementi della lista. Se fornisci una funzione `trackBy`, Angular può tracciare quali elementi sono stati aggiunti, rimossi o spostati, e fare solo le modifiche minime necessarie al DOM, migliorando significativamente le performance.
        *   La funzione `trackBy` riceve l'indice e l'elemento corrente e deve restituire un valore univoco per quell'elemento (spesso l'ID dell'oggetto).

**3. `ngSwitch`: Mostrare Elementi Condizionalmente da un Insieme**

La direttiva `ngSwitch` è un po' diversa sintatticamente. `ngSwitch` è una direttiva di attributo applicata a un elemento contenitore, mentre `*ngSwitchCase` e `*ngSwitchDefault` sono direttive strutturali.

*   **Sintassi:**
    *   `[ngSwitch]="espressioneSwitch"` sull'elemento contenitore.
    *   `*ngSwitchCase="valoreCaso"` su elementi figli.
    *   `*ngSwitchDefault` su un elemento figlio per il caso di default.

```typescript
// my-switch.component.ts
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common'; // Necessario

type UserRole = 'admin' | 'editor' | 'guest' | 'unknown';

@Component({
  selector: 'app-my-switch',
  standalone: true,
  imports: [CommonModule],
  template: `
    <h2>Direttiva ngSwitch</h2>

    <div>
      <label for="roleSelect">Seleziona Ruolo Utente: </label>
      <select [(ngModel)]="selectedRole" (change)="roleChanged()" id="roleSelect">
        <option value="admin">Admin</option>
        <option value="editor">Editor</option>
        <option value="guest">Guest</option>
        <option value="unknown">Sconosciuto</option>
      </select>
    </div>

    <div [ngSwitch]="currentRole" class="switch-container">
      <div *ngSwitchCase="'admin'" class="box admin-box">
        <p>Accesso completo: Pannello di Amministrazione disponibile.</p>
      </div>
      <div *ngSwitchCase="'editor'" class="box editor-box">
        <p>Accesso limitato: Puoi modificare contenuti.</p>
      </div>
      <div *ngSwitchCase="'guest'" class="box guest-box">
        <p>Accesso ospite: Puoi visualizzare contenuti pubblici.</p>
      </div>
      <div *ngSwitchDefault class="box default-box">
        <p>Ruolo non riconosciuto o non selezionato. Accesso negato.</p>
      </div>
    </div>
  `
  // FormsModule è necessario per [(ngModel)], quindi dovremmo aggiungerlo agli imports se non
  // è già in un modulo importato (come CommonModule non lo include)
  // Per questo esempio, per semplicità, gestiamo il cambio ruolo con un (change)
  // Se volessimo usare ngModel, avremmo bisogno di importare FormsModule.
  // Modifichiamo per usare un evento change per evitare di aggiungere FormsModule qui:
  // template: ` ... <select (change)="roleChanged($event)" ... `
  // e nel component:
  // currentRole: UserRole = 'guest';
  // selectedRole: UserRole = 'guest';
  // roleChanged(event: Event) { this.currentRole = (event.target as HTMLSelectElement).value as UserRole; }
  // Per mantenere l'esempio più pulito con ngModel, assumiamo che FormsModule sia gestito altrove
  // o lo aggiungiamo:
  // imports: [CommonModule, FormsModule], // SE USI [(ngModel)]
})
export class MySwitchComponent {
  // Per far funzionare [(ngModel)] in un componente standalone, devi importare FormsModule.
  // Dato che CommonModule è già lì, aggiungiamo FormsModule per l'esempio.
  // Nel file ts:
  // import { FormsModule } from '@angular/forms';
  // @Component({ ... imports: [CommonModule, FormsModule], ... })

  currentRole: UserRole = 'guest'; // Il valore che ngSwitch valuta
  selectedRole: UserRole = 'guest'; // Per [(ngModel)] del select

  constructor() {
    // Inizializziamo currentRole con selectedRole
    this.roleChanged();
  }

  roleChanged(): void {
    this.currentRole = this.selectedRole;
    console.log('Ruolo cambiato a:', this.currentRole);
  }
}
```
*Per l'esempio `MySwitchComponent` sopra, per far funzionare `[(ngModel)]` correttamente, la classe del componente dovrebbe importare `FormsModule` e aggiungerlo all'array `imports` del decoratore `@Component`, dato che stiamo lavorando con componenti standalone.*

```typescript
// my-switch.component.ts CORRETTO CON FormsModule
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms'; // << IMPORTA FormsModule

type UserRole = 'admin' | 'editor' | 'guest' | 'unknown';

@Component({
  selector: 'app-my-switch',
  standalone: true,
  imports: [CommonModule, FormsModule], // << AGGIUNGI FormsModule AGLI IMPORTS
  template: `
    <h2>Direttiva ngSwitch</h2>

    <div>
      <label for="roleSelect">Seleziona Ruolo Utente: </label>
      <select [(ngModel)]="selectedRole" (change)="roleChanged()" id="roleSelect">
        <option value="admin">Admin</option>
        <option value="editor">Editor</option>
        <option value="guest">Guest</option>
        <option value="unknown">Sconosciuto</option>
      </select>
    </div>

    <div [ngSwitch]="currentRole" class="switch-container">
      <div *ngSwitchCase="'admin'" class="box admin-box">
        <p>Accesso completo: Pannello di Amministrazione disponibile.</p>
      </div>
      <div *ngSwitchCase="'editor'" class="box editor-box">
        <p>Accesso limitato: Puoi modificare contenuti.</p>
      </div>
      <div *ngSwitchCase="'guest'" class="box guest-box">
        <p>Accesso ospite: Puoi visualizzare contenuti pubblici.</p>
      </div>
      <div *ngSwitchDefault class="box default-box">
        <p>Ruolo non riconosciuto o non selezionato. Accesso negato.</p>
      </div>
    </div>
  `,
  styles: [`
    .box { padding: 10px; margin-top: 10px; border: 1px solid #ccc; }
    .admin-box { background-color: #d1ecf1; }
    .editor-box { background-color: #fff3cd; }
    .guest-box { background-color: #e2e3e5; }
    .default-box { background-color: #f8d7da; }
  `]
})
export class MySwitchComponent {
  currentRole: UserRole = 'guest';
  selectedRole: UserRole = 'guest';

  constructor() {
    this.roleChanged(); // Sincronizza all'inizio
  }

  roleChanged(): void {
    this.currentRole = this.selectedRole;
  }
}
```

*   **Spiegazione:**
    *   L'elemento `div` contenitore ha `[ngSwitch]="currentRole"`. L'espressione `currentRole` viene valutata.
    *   Angular cerca un elemento figlio con `*ngSwitchCase` il cui valore corrisponda al risultato di `currentRole`.
    *   Solo il primo `*ngSwitchCase` che matcha viene renderizzato nel DOM. Tutti gli altri `*ngSwitchCase` e `*ngSwitchDefault` vengono rimossi dal DOM.
    *   Se nessun `*ngSwitchCase` matcha, e se è presente un `*ngSwitchDefault`, quest'ultimo viene renderizzato.
    *   Come `*ngIf`, anche `*ngSwitchCase` e `*ngSwitchDefault` rimuovono/aggiungono elementi dal/al DOM.

**Importanza di `CommonModule`**

Le direttive strutturali come `*ngIf`, `*ngFor`, `ngSwitchCase`, `ngSwitchDefault`, e anche molti pipe comuni (come `DatePipe`, `UpperCasePipe`), sono forniti da `CommonModule` (che fa parte di `@angular/common`).

*   **Per i componenti standalone (default da Angular 17):** Devi importare `CommonModule` (o le direttive/pipe specifici di cui hai bisogno, se sono esportati come standalone) direttamente nell'array `imports` del decoratore `@Component` del tuo componente. Angular CLI spesso lo fa automaticamente quando generi un nuovo componente.
*   **Per i componenti basati su NgModule (approccio più vecchio):** `CommonModule` deve essere importato nell'array `imports` del `NgModule` che dichiara il componente. Se stai usando `BrowserModule` nel tuo `AppModule` (modulo radice), `BrowserModule` riesporta `CommonModule`, quindi queste direttive sono disponibili nei componenti dichiarati in `AppModule`. Per i feature module, importeresti `CommonModule` direttamente.

---

Le direttive strutturali sono strumenti incredibilmente potenti per creare interfacce utente dinamiche e adattive in Angular. Comprendere come e quando usarle è fondamentale per costruire applicazioni complesse.

Nella prossima lezione, "Ciclo di Vita dei Componenti (Lifecycle Hooks)", esploreremo i vari "momenti" nella vita di un componente, da quando viene creato a quando viene distrutto, e come possiamo agganciarci a questi momenti per eseguire logica specifica.

Hai domande sulle direttive strutturali?