Certamente! Passiamo al **Modulo 3: Direttive e Pipe**. Inizieremo con la **Lezione 8: Direttive di Attributo**.

---

### Modulo 3: Direttive e Pipe

Le direttive sono un concetto potente in Angular che ti permette di estendere l'HTML con nuovi comportamenti e trasformazioni. Abbiamo già visto le *direttive strutturali* (`*ngIf`, `*ngFor`, `*ngSwitchCase`) che modificano la struttura del DOM. Ora ci concentreremo sulle **Direttive di Attributo**.

**Lezione 8: Direttive di Attributo**

Le **Direttive di Attributo** modificano l'aspetto o il comportamento di un elemento HTML esistente, di un componente o di un'altra direttiva a cui sono applicate. A differenza delle direttive strutturali, non aggiungono o rimuovono elementi dal DOM, ma operano sull'elemento a cui sono "attaccate".

Sono chiamate direttive di attributo perché appaiono nei template come normali attributi HTML.

**1. Cosa sono e come funzionano (`ngStyle`, `ngClass`)**

Angular fornisce alcune direttive di attributo integrate molto utili. Le più comuni sono `ngStyle` e `ngClass`. Per utilizzarle in un componente standalone, è necessario importare `CommonModule` (o le direttive specifiche se esportate come standalone) nell'array `imports` del decoratore `@Component`.

*   **`ngClass`**:
    *   Permette di aggiungere e rimuovere dinamicamente classi CSS da un elemento HTML.
    *   Può accettare diverse forme di input:
        *   **Stringa:** Una stringa di nomi di classi separati da spazi (simile all'attributo `class` nativo).
        *   **Array:** Un array di stringhe, dove ogni stringa è un nome di classe.
        *   **Oggetto:** Un oggetto dove le chiavi sono i nomi delle classi e i valori sono espressioni booleane. La classe viene aggiunta se il valore booleano è `true`.

    ```typescript
    // my-styles-component.component.ts
    import { Component } from '@angular/core';
    import { CommonModule } from '@angular/common'; // Necessario per ngClass e ngStyle

    @Component({
      selector: 'app-my-styles',
      standalone: true,
      imports: [CommonModule],
      template: `
        <h2>Direttiva ngClass</h2>

        <!-- ngClass con Oggetto -->
        <div [ngClass]="{
          'box': true,
          'highlight': isHighlighted,
          'large-text': applyLargeText,
          'error-border': hasError
        }">
          Testo con classi dinamiche (Oggetto).
        </div>
        <button (click)="toggleHighlight()">Toggle Highlight</button>
        <button (click)="toggleLargeText()">Toggle Large Text</button>
        <button (click)="toggleError()">Toggle Error Border</button>
        <hr>

        <!-- ngClass con Stringa -->
        <div [ngClass]="activeClassesString">
          Testo con classi dinamiche (Stringa).
        </div>
        <hr>

        <!-- ngClass con Array -->
        <div [ngClass]="activeClassesArray">
          Testo con classi dinamiche (Array).
        </div>
      `,
      styles: [`
        .box { padding: 10px; margin: 5px; border: 1px solid #ccc; }
        .highlight { background-color: yellow; font-weight: bold; }
        .large-text { font-size: 20px; }
        .error-border { border: 2px solid red; }
        .base-style { color: blue; }
        .extra-style { text-decoration: underline; }
      `]
    })
    export class MyStylesComponent {
      isHighlighted: boolean = false;
      applyLargeText: boolean = true;
      hasError: boolean = false;

      activeClassesString: string = 'box base-style';
      activeClassesArray: string[] = ['box', 'extra-style'];

      toggleHighlight(): void {
        this.isHighlighted = !this.isHighlighted;
      }
      toggleLargeText(): void {
        this.applyLargeText = !this.applyLargeText;
      }
      toggleError(): void {
        this.hasError = !this.hasError;
        if (this.hasError) {
          this.activeClassesString = 'box base-style error-border';
          this.activeClassesArray = ['box', 'extra-style', 'error-border'];
        } else {
          this.activeClassesString = 'box base-style';
          this.activeClassesArray = ['box', 'extra-style'];
        }
      }
    }
    ```

*   **`ngStyle`**:
    *   Permette di applicare dinamicamente stili CSS inline a un elemento HTML.
    *   L'input per `[ngStyle]` è un oggetto dove le chiavi sono i nomi delle proprietà CSS (in camelCase, es. `fontSize`, o in kebab-case tra apici, es. `'font-size'`) e i valori sono le espressioni che definiscono i valori di quegli stili.

    ```typescript
    // my-styles-component.component.ts (continua nella stessa classe o in un'altra)
    // ... (dentro la classe MyStylesComponent)
    export class MyStylesComponent {
      // ... (proprietà precedenti per ngClass)

      currentFontSize: number = 16;
      textColor: string = 'green';
      useImportantFont: boolean = false;

      increaseFontSize(): void {
        this.currentFontSize += 2;
      }
      decreaseFontSize(): void {
        this.currentFontSize -= 2;
      }
      toggleImportantFont(): void {
        this.useImportantFont = !this.useImportantFont;
      }
    }
    ```

    ```html
    <!-- my-styles-component.component.html (continua) -->
    <h2>Direttiva ngStyle</h2>
    <div [ngStyle]="{
      'color': textColor,
      'font-size.px': currentFontSize,
      'font-weight': useImportantFont ? 'bold' : 'normal',
      'border': hasError ? '2px solid orange' : '1px solid lightblue'
    }">
      Testo con stili inline dinamici.
    </div>
    <button (click)="increaseFontSize()">Aumenta Font</button>
    <button (click)="decreaseFontSize()">Diminuisci Font</button>
    <button (click)="textColor = 'purple'">Cambia Colore Testo (Purple)</button>
    <button (click)="toggleImportantFont()">Toggle Font Importante</button>
    ```
    *   Nota l'uso di `'font-size.px'`: `ngStyle` permette di specificare unità (come `px`, `em`, `%`) direttamente nella chiave. Se non specificata e il valore è un numero, per alcune proprietà come `font-size`, Angular potrebbe aggiungere `px` automaticamente, ma è buona pratica essere espliciti.

**2. Creare Direttive di Attributo Personalizzate**

Oltre a usare quelle integrate, puoi creare le tue direttive di attributo personalizzate per incapsulare logiche di comportamento o stile riutilizzabili.

Una direttiva di attributo è una classe TypeScript decorata con `@Directive`.

**Passaggi per creare una direttiva di attributo personalizzata:**

1.  **Creare la classe della direttiva** e decorarla con `@Directive`.
2.  **Specificare un selettore CSS** nel decoratore `@Directive`. Questo selettore identificherà gli elementi a cui la direttiva verrà applicata. Solitamente si usa un nome di attributo tra parentesi quadre (es. `[appHighlight]`).
3.  **Iniettare `ElementRef` nel costruttore** per ottenere un riferimento diretto all'elemento DOM a cui la direttiva è applicata. `ElementRef` fornisce accesso all'elemento nativo tramite la sua proprietà `nativeElement`.
4.  **Iniettare `Renderer2` nel costruttore** per manipolare il DOM in modo sicuro e astratto dalla piattaforma (raccomandato rispetto alla manipolazione diretta di `nativeElement`). `Renderer2` fornisce metodi come `addClass()`, `removeClass()`, `setStyle()`, `setAttribute()`, ecc.
5.  **Usare `@HostListener`** per ascoltare eventi sull'elemento host e reagire di conseguenza.
6.  **Usare `@Input()`** per permettere alla direttiva di ricevere valori dal template (rendendola configurabile).
7.  **Registrare la direttiva:**
    *   **Per direttive standalone:** La direttiva deve avere `standalone: true` nel suo decoratore `@Directive` e poi essere importata nell'array `imports` del componente standalone che la utilizza.
    *   **Per direttive basate su NgModule:** La direttiva deve essere dichiarata nell'array `declarations` di un `NgModule` e poi quel `NgModule` deve essere importato dove necessario.

**Esempio: Creare una direttiva `appHighlight`**

Questa direttiva evidenzierà lo sfondo dell'elemento quando il mouse ci passa sopra.

*   **Generare la direttiva con Angular CLI:**
    ```bash
    ng generate directive highlight
    # o ng g d highlight
    ```
    Questo creerà `highlight.directive.ts` e `highlight.directive.spec.ts`. La direttiva generata sarà standalone per default (da Angular 17).

*   **`highlight.directive.ts`:**

    ```typescript
    import { Directive, ElementRef, HostListener, Input, Renderer2, OnInit } from '@angular/core';

    @Directive({
      selector: '[appHighlight]', // Il selettore CSS. Si applica a qualsiasi elemento con l'attributo 'appHighlight'
      standalone: true           // Direttiva standalone
    })
    export class HighlightDirective implements OnInit {
      @Input() appHighlight: string = 'yellow'; // Colore di highlight di default (può essere sovrascritto dal template)
      @Input() defaultColor: string = '';     // Colore di default quando il mouse non è sopra

      constructor(private el: ElementRef, private renderer: Renderer2) {
        // Non fare manipolazioni DOM pesanti nel costruttore.
        // Usa ngOnInit o gli host listener.
      }

      ngOnInit(): void {
        // Imposta il colore di sfondo iniziale se defaultColor è specificato
        if (this.defaultColor) {
          this.changeBackgroundColor(this.defaultColor);
        }
      }

      // @HostListener ascolta un evento sull'elemento host della direttiva
      @HostListener('mouseenter') onMouseEnter() {
        this.changeBackgroundColor(this.appHighlight || 'yellow');
      }

      @HostListener('mouseleave') onMouseLeave() {
        this.changeBackgroundColor(this.defaultColor || null); // Rimuove lo stile se defaultColor non è settato
      }

      private changeBackgroundColor(color: string | null) {
        // Usa Renderer2 per manipolare lo stile in modo sicuro
        this.renderer.setStyle(this.el.nativeElement, 'backgroundColor', color);
      }
    }
    ```

*   **Usare la direttiva in un componente:**

    ```typescript
    // some-component.component.ts
    import { Component } from '@angular/core';
    import { HighlightDirective } from '../highlight.directive'; // Importa la direttiva standalone

    @Component({
      selector: 'app-some-component',
      standalone: true,
      imports: [HighlightDirective], // Aggiungi la direttiva agli imports
      template: `
        <h2>Direttiva di Attributo Personalizzata: appHighlight</h2>

        <p appHighlight>Passa il mouse qui per vedere l'highlight (default giallo).</p>

        <p [appHighlight]="'lightgreen'">
          Passa il mouse qui (highlight verde chiaro personalizzato).
        </p>

        <p appHighlight="lightblue" defaultColor="lightgray">
          Highlight personalizzato (azzurro) e colore di default (grigio chiaro).
        </p>

        <div>
          <label for="colorPicker">Scegli un colore per l'highlight:</label>
          <input type="color" [(ngModel)]="customHighlightColor" id="colorPicker">
          <!-- Per ngModel, assicurati che FormsModule sia importato -->
        </div>
        <p [appHighlight]="customHighlightColor" defaultColor="white">
          Paragrafo con highlight dinamico dal color picker.
        </p>
      `,
      // Se usi [(ngModel)] nel template sopra, devi importare FormsModule
      // imports: [HighlightDirective, FormsModule],
    })
    export class SomeComponent {
      customHighlightColor: string = '#FFD700'; // Gold
      // Assicurati di importare FormsModule se usi [(ngModel)]
      // constructor() {}
    }
    // Per far funzionare [(ngModel)] in SomeComponent:
    // 1. npm install --save @angular/forms (se non già presente)
    // 2. In some-component.component.ts:
    //    import { FormsModule } from '@angular/forms';
    //    ...
    //    imports: [HighlightDirective, FormsModule],
    ```

**3. Manipolare lo stile e il comportamento degli elementi DOM**

*   **`ElementRef`**:
    *   Fornisce un wrapper attorno all'elemento DOM nativo.
    *   `el.nativeElement` ti dà accesso diretto all'elemento DOM.
    *   **Attenzione:** La manipolazione diretta di `nativeElement` (es. `el.nativeElement.style.backgroundColor = 'yellow';`) è sconsigliata perché crea una dipendenza stretta dal browser e può rendere l'applicazione vulnerabile a attacchi XSS se i dati provengono dall'utente. Inoltre, non funziona bene con il rendering lato server (Angular Universal) o con i Web Workers.

*   **`Renderer2`**:
    *   Fornisce un'astrazione per manipolare il DOM in modo sicuro e indipendente dalla piattaforma.
    *   È il metodo preferito per tutte le modifiche al DOM da dentro una direttiva o un componente.
    *   Metodi utili di `Renderer2`:
        *   `addClass(element, 'mia-classe')`
        *   `removeClass(element, 'mia-classe')`
        *   `setStyle(element, 'proprieta-css', 'valore', flags?: RendererStyleFlags2)` (es. `RendererStyleFlags2.Important`)
        *   `removeStyle(element, 'proprieta-css')`
        *   `setAttribute(element, 'nome-attributo', 'valore-attributo')`
        *   `removeAttribute(element, 'nome-attributo')`
        *   `setProperty(element, 'nome-proprieta', valore)` (per proprietà dell'oggetto DOM, non attributi HTML)
        *   `createText(testo)`
        *   `createElement(nomeTag)`
        *   `appendChild(elementoPadre, nuovoFiglio)`
        *   `insertBefore(elementoPadre, nuovoFiglio, nodoDiRiferimento)`
        *   `removeChild(elementoPadre, vecchioFiglio)`
        *   `listen(target, eventName, callback)`: Per registrare event listener. `@HostListener` è spesso una scorciatoia più comoda per ascoltare eventi sull'elemento host della direttiva.

**Riepilogo Direttive di Attributo:**

*   Modificano l'aspetto o il comportamento di elementi esistenti.
*   Non alterano la struttura del DOM (non aggiungono/rimuovono elementi).
*   Si usano come attributi HTML.
*   `ngClass` e `ngStyle` sono direttive integrate comuni.
*   Puoi creare direttive personalizzate usando `@Directive`, `ElementRef`, `Renderer2`, `@HostListener` e `@Input()`.
*   Usa `Renderer2` per manipolazioni DOM sicure.
*   Le direttive standalone devono essere importate nei componenti che le usano.

---

Le direttive di attributo sono uno strumento versatile per aggiungere funzionalità riutilizzabili agli elementi della tua UI. Nella prossima lezione, "Pipe", vedremo un altro modo per trasformare i dati direttamente nei tuoi template Angular.

Domande sulle direttive di attributo?