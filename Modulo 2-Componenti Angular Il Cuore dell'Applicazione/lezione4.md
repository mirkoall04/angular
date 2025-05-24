Eccellente! Procediamo con il **Modulo 2** e la sua prima lezione, addentrandoci nel cuore pulsante delle applicazioni Angular: i Componenti.

---

### Modulo 2: Componenti Angular: Il Cuore dell'Applicazione

**Lezione 4: Anatomia di un Componente**

I componenti sono i blocchi di costruzione fondamentali e più importanti di qualsiasi applicazione Angular. Ogni componente è responsabile di una porzione dell'interfaccia utente (UI), gestendone la logica e la visualizzazione. Un'applicazione Angular è essenzialmente un albero di componenti.

**1. Cos'è un Componente Angular?**

Un componente Angular è una classe TypeScript, decorata con il metadato `@Component`, che possiede:

*   Un **template HTML** che definisce la sua vista (la parte visibile all'utente).
*   Una **classe TypeScript** che contiene la logica di business, i dati (proprietà) e i metodi che interagiscono con la vista.
*   Opzionalmente, degli **stili CSS** specifici per quel componente, che ne definiscono l'aspetto.

I componenti sono progettati per essere **riusabili** e **autonomi**. Ad esempio, potresti avere un `ProductListComponent` che mostra una lista di prodotti, e ogni prodotto nella lista potrebbe essere un'istanza di un `ProductDetailComponent`.

**2. Il Decoratore `@Component`**

Il decoratore `@Component` è una funzione TypeScript che viene applicata a una classe e le fornisce i metadati che dicono ad Angular come processare, istanziare e usare quella classe come un componente. Importiamo `@Component` da `@angular/core`.

```typescript
import { Component } from '@angular/core';

@Component({
  // Metadati del componente
})
export class MyExampleComponent {
  // Logica del componente
}
```

Vediamo le proprietà di configurazione (metadati) più comuni del decoratore `@Component`:

*   **`selector`**:
    *   Una stringa (solitamente in formato kebab-case, es. `app-user-profile`) che definisce il tag HTML personalizzato che userai per istanziare questo componente nei template di altri componenti.
    *   Quando Angular incontra questo tag nel DOM, renderizza il template del componente al suo posto.
    *   Esempio: `selector: 'app-my-example'` significa che potrai usare `<app-my-example></app-my-example>` in un altro template HTML.

*   **`templateUrl`**:
    *   Una stringa che specifica il percorso relativo a un file HTML esterno che contiene il template del componente.
    *   Esempio: `templateUrl: './my-example.component.html'`
    *   Questo è l'approccio più comune per template di media o grande dimensione, poiché mantiene separata la struttura dalla logica.

*   **`styleUrls`** (o **`styles`**):
    *   **`styleUrls`**: Un array di stringhe che specificano i percorsi relativi a file CSS esterni che contengono gli stili per il componente.
        *   Esempio: `styleUrls: ['./my-example.component.css', './another-style.css']`
    *   **`styles`**: Un array di stringhe che contengono direttamente il codice CSS inline.
        *   Esempio: `styles: ['h1 { font-weight: bold; }', 'p { color: blue; }']`
        *   Utile per stili molto piccoli o specifici.
    *   Gli stili definiti per un componente sono **incapsulati** (scoped) per default. Ciò significa che si applicano solo al template del componente e non "fuoriescono" influenzando il resto dell'applicazione, né gli stili globali influenzano direttamente gli elementi interni del componente (a meno che non si usino meccanismi specifici come `::ng-deep`, che è generalmente sconsigliato). Questo si chiama **View Encapsulation**.

*   **`template`**:
    *   Una stringa che contiene direttamente il codice HTML del template del componente.
    *   Alternativa a `templateUrl`, utile per template molto piccoli (poche righe).
    *   Esempio:
        ```typescript
        template: `
          <h1>Hello from My Example Component!</h1>
          <p>This is an inline template.</p>
        `
        ```

*   **`standalone`**:
    *   Una proprietà booleana (introdotta in Angular 14 e **default a `true`** per i nuovi componenti generati con Angular CLI da v17 in poi).
    *   Se `true`, il componente è **standalone**. Un componente standalone non ha bisogno di essere dichiarato in un `NgModule`. Gestisce le proprie dipendenze direttamente importandole nella proprietà `imports` del decoratore `@Component`.
    *   Esempio:
        ```typescript
        @Component({
          standalone: true,
          selector: 'app-standalone-example',
          templateUrl: './standalone-example.component.html',
          imports: [CommonModule, AnotherStandaloneComponent, MyCustomPipe] // Importa moduli, altri componenti standalone, direttive, pipe
        })
        export class StandaloneExampleComponent { /* ... */ }
        ```
    *   Se `false` (o omesso nelle versioni precedenti ad Angular 17, dove il default era `false`), il componente deve essere dichiarato in un `NgModule`. Questo approccio sta diventando meno comune per i nuovi sviluppi.

**3. Il File TypeScript del Componente (Logica)**

Il file `.ts` (es. `my-example.component.ts`) contiene la classe del componente. Questa classe è il cuore della logica del componente.

*   **`export class MyExampleComponent { ... }`**:
    *   `export` rende la classe disponibile per l'uso in altre parti dell'applicazione (es. per essere importata da un componente padre o da un file di routing).
    *   La classe contiene:
        *   **Proprietà (Properties):** Variabili che memorizzano i dati che il componente utilizzerà o visualizzerà nel template.
        *   **Metodi (Methods):** Funzioni che definiscono il comportamento del componente, come la risposta a eventi dell'utente (click di un bottone, input di testo) o l'esecuzione di calcoli.
        *   **Costruttore (Constructor):** Un metodo speciale per inizializzare il componente e per la dependency injection (che vedremo più avanti).
        *   **Lifecycle Hooks:** Metodi speciali (come `ngOnInit`, `ngOnDestroy`) che Angular chiama durante il ciclo di vita del componente, permettendoti di eseguire codice in momenti specifici (es. dopo che le proprietà di input sono state inizializzate).

Esempio di un file TypeScript di un componente:

```typescript
// my-example.component.ts
import { Component, OnInit } from '@angular/core'; // Importa OnInit se vuoi usare quel lifecycle hook

@Component({
  selector: 'app-my-example',
  standalone: true, // Assumiamo un componente standalone
  imports: [],      // Eventuali dipendenze come CommonModule se si usano direttive come *ngIf, *ngFor
  templateUrl: './my-example.component.html',
  styleUrls: ['./my-example.component.css']
})
export class MyExampleComponent implements OnInit { // Implementa OnInit
  // Proprietà
  title: string = 'Benvenuti nel Mio Componente!';
  counter: number = 0;
  isVisible: boolean = true;

  // Costruttore (spesso usato per la Dependency Injection)
  constructor() { }

  // Lifecycle Hook (eseguito dopo che Angular ha inizializzato le proprietà di input)
  ngOnInit(): void {
    console.log('MyExampleComponent inizializzato!');
    // Potresti fare un fetch di dati iniziali qui
  }

  // Metodo
  incrementCounter(): void {
    this.counter++;
  }

  toggleVisibility(): void {
    this.isVisible = !this.isVisible;
  }
}
```

**4. Il File HTML del Componente (Template)**

Il file `.html` (es. `my-example.component.html`) definisce la struttura HTML che verrà renderizzata per questo componente. Può contenere HTML standard e la sintassi speciale dei template di Angular per:

*   **Interpolation (`{{ }}`):** Per visualizzare i valori delle proprietà del componente.
*   **Property Binding (`[property]`):** Per passare dati dal componente a proprietà di elementi HTML o direttive.
*   **Event Binding (`(event)`):** Per rispondere a eventi DOM (es. click, input) chiamando metodi del componente.
*   **Two-way Data Binding (`[(ngModel)]`):** Per sincronizzare dati tra il componente e un input di un form.
*   **Direttive Strutturali (`*ngIf`, `*ngFor`, `*ngSwitch`):** Per manipolare la struttura del DOM.

Esempio di un file HTML di un componente (corrispondente al file TS sopra):

```html
<!-- my-example.component.html -->
<h1>{{ title }}</h1>

<div *ngIf="isVisible"> <!-- Direttiva Strutturale *ngIf -->
  <p>Il contatore è attualmente a: {{ counter }}</p> <!-- Interpolation -->
  <button (click)="incrementCounter()">Incrementa</button> <!-- Event Binding -->
</div>

<button (click)="toggleVisibility()">
  {{ isVisible ? 'Nascondi' : 'Mostra' }} Sezione Contatore
</button>

<!-- Esempio di come potresti usare un altro componente (se 'app-child' fosse un selettore valido) -->
<!-- <app-child [data]="counter"></app-child> -->
```Per usare direttive come `*ngIf`, dovrai importare `CommonModule` nell'array `imports` del decoratore `@Component` se il tuo componente è standalone.

**5. Il File CSS del Componente (Stili)**

Il file `.css` (o `.scss`, `.less`, etc., es. `my-example.component.css`) contiene gli stili specifici per il componente.

Come menzionato, Angular fornisce l'**incapsulamento degli stili** (View Encapsulation). Per default (Emulated View Encapsulation), Angular modifica i selettori CSS del componente per far sì che si applichino solo agli elementi del template del componente. Lo fa aggiungendo attributi univoci agli elementi HTML del componente e modificando i selettori CSS di conseguenza.

Esempio di un file CSS di un componente:

```css
/* my-example.component.css */
h1 {
  color: navy;
  font-family: Arial, sans-serif;
}

p {
  font-size: 16px;
  margin-bottom: 10px;
}

button {
  background-color: lightcoral;
  color: white;
  padding: 8px 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-right: 10px;
}

button:hover {
  background-color: coral;
}
```Questi stili `h1`, `p`, `button` si applicheranno solo agli elementi `<h1>`, `<p>` e `<button>` definiti nel `my-example.component.html` e non a quelli in altri componenti.

**6. Creare e Registrare un Nuovo Componente usando Angular CLI**

Angular CLI rende la creazione di nuovi componenti estremamente semplice.

*   **Comando per generare un componente:**
    Apri il terminale nella root del tuo progetto Angular e digita:
    ```bash
    ng generate component nome-del-tuo-componente
    ```
    Oppure la forma abbreviata:
    ```bash
    ng g c nome-del-tuo-componente
    ```
    Ad esempio, per creare un componente chiamato `user-profile`:
    ```bash
    ng g c user-profile
    ```

*   **Cosa genera il CLI:**
    Quando esegui questo comando, Angular CLI fa diverse cose:
    1.  Crea una nuova cartella chiamata `user-profile` (o il nome che hai dato) all'interno della cartella `src/app/` (o nella cartella in cui hai eseguito il comando, se sei già in una sottocartella di `app`).
    2.  Dentro questa cartella, genera quattro file:
        *   `user-profile.component.ts`: Il file della classe TypeScript.
        *   `user-profile.component.html`: Il file del template HTML.
        *   `user-profile.component.css` (o il formato di stile scelto per il progetto): Il file degli stili.
        *   `user-profile.component.spec.ts`: Il file per gli unit test del componente.
    3.  Per impostazione predefinita (da Angular 17), il componente generato sarà **standalone** (`standalone: true`). Questo significa che è pronto per essere importato e utilizzato direttamente in altri componenti standalone o moduli.
        ```typescript
        // user-profile.component.ts (generato)
        import { Component } from '@angular/core';
        import { CommonModule } from '@angular/common'; // Spesso aggiunto di default

        @Component({
          selector: 'app-user-profile',
          standalone: true,
          imports: [CommonModule], // CommonModule è utile per direttive come *ngIf, *ngFor
          templateUrl: './user-profile.component.html',
          styleUrl: './user-profile.component.css' // o styleUrls
        })
        export class UserProfileComponent {

        }
        ```

*   **Come usare il componente generato:**
    Dato che `UserProfileComponent` è standalone, puoi usarlo in un altro componente (ad esempio, `app.component.ts`, che è il componente radice) importandolo e aggiungendolo all'array `imports` del componente padre:

    ```typescript
    // app.component.ts (componente padre)
    import { Component } from '@angular/core';
    import { RouterOutlet } from '@angular/router';
    import { UserProfileComponent } from './user-profile/user-profile.component'; // 1. Importa il componente

    @Component({
      selector: 'app-root',
      standalone: true,
      imports: [RouterOutlet, UserProfileComponent], // 2. Aggiungilo a imports
      template: `
        <h1>App Component</h1>
        <app-user-profile></app-user-profile> <!-- 3. Usa il selettore nel template -->
      `,
      styleUrls: ['./app.component.css']
    })
    export class AppComponent {
      title = 'my-first-angular-app';
    }
    ```

*   **Nota sui Non-Standalone Components (per conoscenza):**
    Se stessi lavorando con componenti non-standalone (o in versioni più vecchie di Angular dove questo era il default), invece di aggiungere il componente all'array `imports` di un altro componente standalone, dovresti dichiararlo nell'array `declarations` di un `NgModule` e poi importare quel `NgModule` dove necessario.
    ```typescript
    // Esempio di come sarebbe in un NgModule (approccio più vecchio o per componenti non-standalone)
    // my-feature.module.ts
    import { NgModule } from '@angular/core';
    import { CommonModule } from '@angular/common';
    import { OldStyleComponent } from './old-style/old-style.component';

    @NgModule({
      declarations: [
        OldStyleComponent // Qui si dichiara il componente non-standalone
      ],
      imports: [
        CommonModule
      ],
      exports: [
        OldStyleComponent // Esporta il componente se deve essere usato da altri moduli che importano MyFeatureModule
      ]
    })
    export class MyFeatureModule { }
    ```
    Con i componenti standalone, la gestione delle dipendenze è più diretta a livello di componente.

---

Questa lezione ha coperto molta teoria fondamentale sui componenti. Abbiamo visto come sono strutturati, le loro parti principali (TypeScript, HTML, CSS) e come Angular CLI ci aiuta a crearli.

Nella prossima lezione, "Template Syntax - Data Binding", esploreremo più a fondo come far comunicare la logica del componente (il file `.ts`) con la sua vista (il file `.html`) attraverso le varie forme di data binding.

