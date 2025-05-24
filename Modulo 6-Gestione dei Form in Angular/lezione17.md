Perfetto! Passiamo ora alla **Lezione 17: Reactive Forms (Model-Driven Forms)**. Questo è l'altro approccio principale alla gestione dei form in Angular, spesso preferito per form più complessi e quando si desidera un maggiore controllo programmatico.

---

### Modulo 6: Gestione dei Form in Angular

**Lezione 17: Reactive Forms (Model-Driven Forms)**

I **Reactive Forms** (a volte chiamati Model-Driven Forms) offrono un approccio più programmatico e robusto alla gestione degli input dei form. A differenza dei Template-Driven Forms, dove la maggior parte della logica è nel template, con i Reactive Forms la "source of truth" (fonte di verità) per il form è definita e gestita principalmente nella **classe del componente TypeScript**.

In questo approccio, crei esplicitamente un modello del form (un albero di oggetti `FormControl`, `FormGroup`, e `FormArray`) nel codice del componente. Questo modello viene poi collegato agli elementi HTML del form nel template.

**Caratteristiche Principali dei Reactive Forms:**

*   **ModAssello del Form nel Codice:** La struttura, i dati e i validatori del form sono definiti esplicitamente nel codice TypeScriptolutamente! Ora che abbiamo una buona comprensione dei Template-Driven Forms, pass del componente.
*   **Immutabilità (preferita):** Le modifiche allo stato e al valore del form tendiamo all'altro approccio principale per la gestione dei form in Angular: i **Reactive Forms**.

---

###ono a restituire un nuovo stato e un nuovo valore (piuttosto che modificare l'oggetto esistente), il che si Modulo 6: Gestione dei Form in Angular

**Lezione 17: Reactive Forms (Model-Driven Forms)**

I **Reactive Forms** (a volte chiamati Model-Driven Forms) offrono un approccio più programmatico integra bene con pattern di change detection come OnPush.
*   **Sincrono:** Il modello del form è disponibile immediatamente dopo la sua creazione nel componente.
*   **Maggiore Testabilità:** Poiché la logica del form e strutturato alla gestione dei form. In questo paradigma, il modello del form (la sua struttura, i controlli, è nel componente, è più facile scrivere unit test per essa, indipendentemente dal rendering del template.
*   **F la validazione) è definito esplicitamente nella **classe TypeScript del componente**. Il template HTML si limita a "collessibilità e Scalabilità:** Molto adatto per form complessi, validazioni dinamiche, validatori personalizzati elegarsi" a questo modello di form preesistente.

Questo approccio offre maggiore controllo, è più scalabile per scenari in cui la struttura del form può cambiare programmaticamente.
*   **Controllo Granulare:** Offre un accesso form complessi, facilita la gestione di validazioni dinamiche e complesse, ed è generalmente più facile da testare unitariamente.

**Caratteristiche Principali dei Reactive Forms:**

*   **Modello del Form nel più diretto e un controllo più fine sullo stato e sui valori dei singoli controlli e del form nel suo complesso tramite API Codice TypeScript:** La fonte della verità per la struttura e la validità del form risiede nella classe del componente. programmatiche.
*   **Gestione di Valori e Validità tramite Observable:** Puoi iscriverti a Observ
*   **Immutabilità (in parte):** Le modifiche allo stato e al valore del form tendono a restituables per ascoltare i cambiamenti nei valori (`valueChanges`) o nello stato di validità (`statusChanges`) dei controllire nuove istanze di stato/valore, il che si sposa bene con pattern di programmazione reattiva e changei o del form.

**1. Utilizzo di `ReactiveFormsModule`**

Per utilizzare i Reactive Forms, devi importare `ReactiveFormsModule` (da `@angular/forms`) nel tuo componente standalone o nel `NgModule` appropriato. detection.
*   **Sincrono:** Il modello del form è disponibile immediatamente dopo la sua creazione nel componente.

*   **Importare `ReactiveFormsModule`:**
    *   **Per componenti standalone:**
        ```typescript
        
*   **Maggiore Controllo Programmatico:** Hai accesso diretto agli oggetti del form (`FormControl`, `Form// tuo-componente.component.ts
        import { Component, OnInit } from '@angular/core';
        import { ReactiveFormsModule, FormGroup, FormControl, Validators } from '@angular/forms'; // Importa ReactiveFormsModule e le classi per i formGroup`, `FormArray`) e puoi manipolarli, osservare i loro cambiamenti e aggiungere validatori dinamicamente.
*   **Migliore Testabilità:** Poiché la logica del form è nel TypeScript, è più facile scrivere
        import { CommonModule } from '@angular/common';

        @Component({
          selector: 'app-tuo-componente',
          standalone: true,
          imports: [ReactiveFormsModule, CommonModule], // Aggiungi ReactiveFormsModule
          templateUrl: './tuo-componente.component.html',
        })
        export class unit test.
*   **Scalabilità:** Ideale per form grandi e complessi con logica di validazione sofistic TuoComponente implements OnInit { /* ... */ }
        ```
    *   **Per componenti basati su NgModule:**
ata.

**1. Utilizzo di `ReactiveFormsModule`**

Per utilizzare i Reactive Forms, devi prima importare `ReactiveFormsModule` (da `@angular/forms`) nel tuo componente standalone o nel `NgModule` appropriato.

*   **Importare        ```typescript
        // tuo.module.ts
        import { NgModule } from '@angular/core';
        import { `ReactiveFormsModule`:**
    *   **Per componenti standalone:**
        ```typescript
        // tuo-reactive-form.component.ts
        import { Component } from '@angular/core';
        import { ReactiveFormsModule CommonModule } from '@angular/common';
        import { ReactiveFormsModule } from '@angular/forms'; // Importa ReactiveFormsModule
        import { TuoComponente } from './tuo-componente.component';

        @NgModule({
          declarations: [TuoComponente],
          imports: [
            CommonModule,
            ReactiveFormsModule // Aggiungi ReactiveFormsModule
          ]
        })
        export class TuoModule { }
 } from '@angular/forms'; // Importa ReactiveFormsModule
        import { CommonModule } from '@angular/common';

        @Component({
          selector: 'app-tuo-reactive-form',
          standalone: true,
          imports: [ReactiveFormsModule, CommonModule], // Aggiungi ReactiveFormsModule
          templateUrl: './tu        ```

**2. Le Classi Base dei Reactive Forms: `FormControl`, `FormGroup`, `FormArray`**

Il modello del form nei Reactive Forms è costruito usando tre classi fondamentali:

*   **`FormControl`**:
    *   o-reactive-form.component.html',
        })
        export class TuoReactiveFormComponent { /* ... */ }
        ```
    *   **Per componenti basati su NgModule:**
        ```typescript
        // tuo-reactive.module.ts
        import { NgModule } from '@angular/core';
        import { CommonModule } from '@Rappresenta un singolo controllo di input individuale (come un campo di testo, una checkbox, un radio button, un select).
    *   Traccia il valore, lo stato di validazione e l'interazione dell'utente per quel controllo.
    *   **Creazione:** `new FormControl(initialValue, validators?, asyncValidators?)angular/common';
        import { ReactiveFormsModule } from '@angular/forms'; // Importa ReactiveFormsModule
        import { TuoReactiveFormComponent } from './tuo-reactive-form.component';

        @NgModule({
          declarations: [TuoReactiveFormComponent],
          imports: [
            CommonModule,
            ReactiveFormsModule // Aggiungi ReactiveFormsModule
          ]
        })
        export class TuoReactiveModule { }
        `
        *   `initialValue`: Il valore iniziale del controllo (es. `''`, `null`, `false`).
        *   `validators` (opzionale): Un validatore sincrono o un array di validatori sinc```
    **Importante:** Non importare sia `FormsModule` che `ReactiveFormsModule` nello stesso modulo/componente a menoroni (es. `Validators.required`, `Validators.minLength(3)`).
        *   `asyncValidators` (opzionale): Un validatore asincrono o un array di validatori asincroni.

*   ** che tu non abbia una ragione molto specifica (e rara) per farlo, poiché potrebbero esserci conflitti di direttive. Generalmente, scegli un approccio per il tuo form.

**2. `FormControl`, `FormGroup`, `FormGroup`**:
    *   Rappresenta un gruppo di `FormControl` (o altri `FormGroup` o `FormArray` annidati).
    *   Traccia il valore e lo stato di validità dell'intero gruppo. Il valore di un `FormGroup` è un oggetto le cui chiavi sono i nomi dei controll`FormArray`**

Queste sono le tre classi fondamentali per costruire il modello di un Reactive Form:

*   **`FormControl`**:
    *   Rappresenta un singolo controllo di input (come un campo di testo, una checkbox, un campo select).
    *   Traccia il valore, lo stato di validazione e l'interazione uti figli e i valori sono i valori di quei controlli.
    *   Un form HTML è tipicamente rappresentato da un `FormGroup` radice.
    *   **Creazione:** `new FormGroup({ controlName1ente (dirty, touched) del singolo controllo.
    *   **Creazione:** `new FormControl(initialValue, validators: FormControl, controlName2: FormControl, ... }, options?)`
        *   Il primo argomento è un oggetto dove le chiavi sono i nomi che darai ai controlli e i valori sono le istanze di `FormControl` (o `Form?, asyncValidators?)`
        *   `initialValue`: Il valore iniziale del controllo.
        *   `validators`: Un validatore o un array di validatori sincroni.
        *   `asyncValidators`: Un validatore o un arrayGroup`/`FormArray`).
        *   `options` (opzionale): Un oggetto che può contenere validatori a livello di gruppo o validatori asincroni.

*   **`FormArray`**:
    *    di validatori asincroni.

*   **`FormGroup`**:
    *   Rappresenta un gruppo di `FormControl` (o altri `FormGroup`/`FormArray` annidati).
    *   Rappresenta un array di `FormControl`, `FormGroup`, o altri `FormArray`.
    *   Traccia il valore e lo stato di validazione dell'intero gruppo. La validità di un `FormGroup` dipende dalla validità di tutti i suoi figli.
    *   **Creazione:** `new FormGroup({ controlName1È utile quando hai un numero dinamico di controlli o gruppi di controlli (es. una lista di skill,: FormControl, controlName2: FormControl, ... })`
        *   L'argomento è un oggetto dove più indirizzi per un utente).
    *   Traccia il valore (un array) e lo stato di le chiavi sono i nomi dei controlli (che userai nel template) e i valori sono le istanze di `FormControl` (o `FormGroup`/`FormArray`).

*   **`FormArray`**:
    *   R validità dell'array.
    *   **Creazione:** `new FormArray([FormControl1, FormGroup1, ...], validators?, asyncValidators?)`

**3. Creare Form nel Codice TypeScript**

Vediamo comeappresenta un array di `FormControl`, `FormGroup` o altri `FormArray`.
    *   Utile per gestire creare un form di registrazione semplice usando Reactive Forms.

```typescript
// registration-reactive.component.ts
import { Component collezioni dinamiche di controlli, come una lista di tag, indirizzi multipli, ecc.
    *   Traccia il valore e lo stato di validazione dell'array.
    *   **Creazione:** `new, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule, FormGroup, FormControl, Validators, FormBuilder } from '@angular/forms'; // Importa le classi necessarie

@Component({
  selector: 'app-registration-reactive',
  standalone: true,
  imports: FormArray([FormControl1, FormGroup1, ...], validators?, asyncValidators?)`

**3. Creare Form nel Codice TypeScript**

Il modello del form viene costruito nella classe del componente, tipicamente nel metodo `ngOnInit [CommonModule, ReactiveFormsModule],
  templateUrl: './registration-reactive.component.html',
  styleUrls: ['./registration-reactive.component.css']
})
export class RegistrationReactiveComponent implements OnInit {
  registrationForm: FormGroup;` o nel costruttore, usando le classi sopra menzionate.

```typescript
// profile-editor.component.ts // Proprietà per contenere il nostro FormGroup
  formSubmittedData: any = null;

  constructor(private fb: FormBuilder) {} // Inietta FormBuilder (opzionale, ma comodo)

  ngOnInit(): void
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormControl, Validators, FormArray } from '@angular/forms'; // Importa le classi necessarie
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule } from '@angular/forms';

@Component({
  selector: 'app-profile- {
    // Metodo 1: Creazione diretta con new FormGroup e new FormControl
    /*
    this.registrationForm = new FormGroup({
      // 'username' è il nome del controllo che useremo nel template con formControlName
editor',
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule],
  templateUrl: './profile-editor.component.html',
  styleUrls: ['./profile-editor.component.css']
})
export      'username': new FormControl(null, [Validators.required, Validators.minLength(3)]),
      'email': new FormControl(null, [Validators.required, Validators.email]),
      'password': new FormControl(null, [Validators.required, class ProfileEditorComponent implements OnInit {
  profileForm: FormGroup; // La proprietà che terrà il nostro FormGroup

  constructor() {}

  ngOnInit(): void {
    // Definisci il modello del form
    this.profileForm Validators.minLength(6)]),
      'address': new FormGroup({ // FormGroup annidato
        'street': new FormControl(null),
        'city': new FormControl(null, Validators.required)
      })
    });
     = new FormGroup({
      // 'firstName' è il nome del controllo
      firstName: new FormControl('', [
        Validators.required, // Validatore sincrono: campo obbligatorio
        Validators.minLength(3) // Valid*/

    // Metodo 2: Creazione con FormBuilder (più conciso e leggibile)
    this.registrationForm = this.fb.group({
      // [initialValue, syncValidators, asyncValidators]
      atore sincrono: lunghezza minima 3
      ]),
      lastName: new FormControl('', Validators.required),
      email: new FormControl('', [
        Validators.required,
        Validators.email // Validatore sincrono: formato email validousername: [null, [Validators.required, Validators.minLength(3), this.forbiddenNameValidator(/admin/i)]
      ]),
      address: new FormGroup({ // FormGroup annidato per l'indirizzo
        street: new FormControl(''),
        city: new FormControl(''),
        zip: new FormControl('', Validators.pattern('^[0-9]{], // Aggiunto validatore personalizzato
      email: [null, [Validators.required, Validators.email]],
      password: ['', [Validators.required, Validators.minLength(6)]], // Stringa vuota come5}$')) // Validatore per CAP italiano
      }),
      aliases: new FormArray([ // FormArray per una valore iniziale
      confirmPassword: ['', Validators.required], // Per validazione di corrispondenza password
      address: lista di alias
        new FormControl('') // Inizia con un alias vuoto
      ])
    });

    // Asc this.fb.group({ // FormGroup annidato con FormBuilder
        street: [''],
        city: ['',oltare i cambiamenti di valore (opzionale)
    this.profileForm.valueChanges.subscribe(value Validators.required]
      }),
      skills: this.fb.array([]) // FormArray per una lista dinamica di skill
    }, { validators: this.passwordMatchValidator }); // Validatore a livello di FormGroup
  }

   => {
      console.log('Valore del Form cambiato:', value);
    });

    // Ascoltare i cambiamenti di stato (opzionale)
    this.profileForm.statusChanges.subscribe(status => {
      // Getter per un accesso più facile ai controlli nel template (opzionale ma comodo)
  get username()console.log('Stato del Form cambiato:', status); // Es. VALID, INVALID
    });
  }

  // { return this.registrationForm.get('username'); }
  get email() { return this.registrationForm.get('email'); }
  get password() { return this.registrationForm.get('password'); }
   Getter per accedere facilmente ai controlli nel template (opzionale ma comodo)
  get firstName() { return this.profileForm.get('firstName'); }
  get lastName() { return this.profileForm.get('get confirmPassword() { return this.registrationForm.get('confirmPassword'); }
  get city() { return this.registrationForm.get('address.city'); } // Accesso a controlli annidati

  // GetterlastName'); }
  get email() { return this.profileForm.get('email'); }
  get zip() { return this.profileForm.get('address.zip'); } // Accesso a controlli annidati
  get per il FormArray 'skills'
  get skills() {
    return this.registrationForm.get('skills') as FormArray;
  }

  // Metodo per aggiungere un nuovo FormControl al FormArray 'skills'
  add aliases() { return this.profileForm.get('aliases') as FormArray; } // Cast a FormArray

  addAlias(): void {
    // Aggiunge un nuovo FormControl all'array 'aliases'
    thisSkill(): void {
    const skillControl = this.fb.control('', Validators.required); // Crea un nuovo FormControl per la skill
    this.skills.push(skillControl); // Aggiunge il controllo al FormArray
  }

.aliases.push(new FormControl('', Validators.required));
  }

  removeAlias(index: number): void {
    this.aliases.removeAt(index);
  }

  onSubmit(): void {
      removeSkill(index: number): void {
    this.skills.removeAt(index);
  }

  onSubmit(): void {
    console.log('Form inviato:', this.registrationForm);
    console.logif (this.profileForm.valid) {
      console.log('Form inviato:', this.profileForm.value);
      // Qui invieresti this.profileForm.value al backend
      // this.profileForm.('Valori del form:', this.registrationForm.value);
    console.log('Validità del form:', this.registrationForm.valid);
    console.log('Stato del form:', this.registrationForm.statusreset(); // Resetta il form (opzionale)
    } else {
      console.error('Il form non è valido!');
      // Marca tutti i controlli come 'touched' per mostrare gli errori
      this);
    console.log('Errori username:', this.username?.errors);

    if (this.registrationForm.valid) {
      this.formSubmittedData = { ...this.registrationForm.value };
      console.validateAllFormFields(this.profileForm);
    }
  }

  // Funzione helper per mar.log('Dati da inviare:', this.formSubmittedData);
      // this.registrationForm.reset(); // Rescare tutti i campi come touched
  validateAllFormFields(formGroup: FormGroup | FormArray) {
    Object.keys(formGroup.controls).forEach(field => {
      const control = formGroup.get(field);
etta il form
    } else {
      console.error('Il form contiene errori!');
      // Marca tutti i controlli come 'touched' per mostrare gli errori
      this.validateAllFormFields(this.registrationForm);
    }      if (control instanceof FormControl) {
        control.markAsTouched({ onlySelf: true });
      } else if (control instanceof FormGroup || control instanceof FormArray) {
        this.validateAllFormFields(control);
  }

  // Utility per marcare tutti i campi come touched
  validateAllFormFields(formGroup: FormGroup | FormArray) {
    Object.keys(formGroup.controls).forEach(field => {
      
      }
    });
  }

  // Metodi per popolare o aggiornare il form (opzionale)
  updateProfile(): void {
    this.profileForm.patchValue({ // patchValue aggiorna solo le proprietà specificate
const control = formGroup.get(field);
      if (control instanceof FormControl) {
        control.mark      firstName: 'Nancy',
      address: {
        street: '123 Drew Street'
      AsTouched({ onlySelf: true });
      } else if (control instanceof FormGroup || control instanceof FormArray) {
        this.validateAllFormFields(control);
      }
    });
  }

  // Validatore personal}
    });
  }

  setProfile(): void {
    this.profileForm.setValue({ // setValue richiede che TUTTE le proprietà siano specificate
      firstName: 'John',
      lastName: 'Doe',
      email:izzato sincrono (esempio)
  forbiddenNameValidator(nameRe: RegExp): ValidatorFn {
    return (control: AbstractControl): ValidationErrors | null => {
      const forbidden = nameRe.test(control.value);
      return 'john.doe@example.com',
      address: {
        street: 'Main St',
        city: 'Anytown',
        zip: '12345'
      },
      aliases: [' forbidden ? { 'forbiddenName': { value: control.value } } : null;
    };
  }

  // Validatore personalizzato a livello di FormGroup per confrontare le password
  passwordMatchValidator: ValidatorFn = (controlJD'] // Se aliases è un FormArray di FormControl
    });
  }
}
```

**Collegare il Modello del Form al Template HTML:**

```html
<!-- profile-editor.component.html -->
: AbstractControl): ValidationErrors | null => {
    const password = control.get('password');
    const confirmPassword = control.get('confirmPassword');

    // Se uno dei campi non è ancora stato toccato, non valid<form [formGroup]="profileForm" (ngSubmit)="onSubmit()">
  <h4>Editor Profilo (Reactive)</h4>

  <div>
    <label for="firstName">Nome:</label>
    <input id="firstName" type="text" formControlName="firstName">
    <!-- Messaggi di errore per firstName -->
    <div *ngare (o gestisci come preferisci)
    if (!password || !confirmPassword || !password.value || !confirmPassword.value) {
      return null;
    }

    return password.value === confirmPassword.value ? null :If="firstName?.invalid && (firstName?.dirty || firstName?.touched)" class="error-messages">
      <div *ngIf="firstName?.errors?.['required']">Il nome è obbligatorio.</div>
      <div * { 'passwordMismatch': true };
  };
}
// Importa ValidatorFn, AbstractControl, ValidationErrors da '@angular/forms'
// Per i validatori personalizzati, e FormArray
```

*   **`FormngIf="firstName?.errors?.['minlength']">
        Il nome deve avere almeno {{ firstName?.errors?.['minlength'].requiredLength }} caratteri.
      </div>
    </div>
  </div>

  <div>
    <label for="lastName">Cognome:</label>
    <input id="lastName" type="text" formControlName="Builder`**:
    *   È un servizio helper (iniettabile) che semplifica la creazione di istanze di `FormControl`, `FormGroup`, e `FormArray`. Fornisce una sintassi più concisa.
    *   lastName">
    <div *ngIf="lastName?.invalid && (lastName?.dirty || lastName?.touched)"`fb.control(initialValue, validators?, asyncValidators?)`
    *   `fb.group({ control class="error-messages">
      <div *ngIf="lastName?.errors?.['required']">Il cognome è obbligatorio.</div>
    </div>
  </div>

  <div>
    <label for="email">Email:</label>
    <input id="email" type="email" formControlName="email">
    <div *Name: [initialValue, validators?], ... })`
    *   `fb.array([control1, control2, ...])`

*   **`Validators`**:
    *   La classe `Validators` (da `@angular/ngIf="email?.invalid && (email?.dirty || email?.touched)" class="error-messages">
      <div *ngIf="email?.errors?.['required']">L'email è obbligatoria.</div>forms`) fornisce una collezione di validatori sincroni standard pronti all'uso:
        *   `Validators.required
      <div *ngIf="email?.errors?.['email']">Inserisci un'email valida.</div>`
        *   `Validators.minLength(x)`
        *   `Validators.maxLength(y)`
        *   `Validators.pattern(regex)`
        *   `Validators.email`
        *   `Validators.min(
    </div>
  </div>

  <!-- FormGroup Annidato per l'indirizzo -->
  <div formGroupName="address">
    <h5>Indirizzo:</h5>
    <div>
      <label for="street">Vian)`
        *   `Validators.max(m)`
        *   ...e altri.
    *   Puoi passare un array di questi validatori al secondo argomento quando crei un `FormControl` o un `FormGroup`.

**4:</label>
      <input id="street" type="text" formControlName="street">
    </div>
    <div>
      <label for="city">Città:</label>
      <input id="city" type="text" formControlName="city">
    </div>
    <div>
      <label for="zip">. Collegare il Modello del Form al Template HTML**

Una volta creato il modello del form nel componente, devi collegarloCAP:</label>
      <input id="zip" type="text" formControlName="zip">
      <div *ngIf="zip?.invalid && (zip?.dirty || zip?.touched)" class="error-messages agli elementi HTML nel template usando direttive specifiche dei Reactive Forms:

*   **`[formGroup]="nome">
        <div *ngIf="zip?.errors?.['pattern']">Il CAP deve essere di 5 cifre numerFormGroup"`**:
    *   Applicata al tag `<form>`. Collega l'elemento `<form>` HTML all'istanza di `FormGroup` definita nel tuo componente.
*   **`formControlName="nomeiche.</div>
      </div>
    </div>
  </div>

  <!-- FormArray per gli alias -->
  <div formArrayName="aliases">
    <h5>Alias:
      <button type="button" (click)="addAliasControllo"`**:
    *   Applicata a un elemento di input (es. `<input>`, `<select>`). Collega l'input HTML a un `FormControl` specifico all'interno del `FormGroup` genitore. Il()" class="add-button">+</button>
    </h5>
    <div *ngFor="let aliasCtrl of aliases.controls; let i=index" class="alias-item">
      <label for="alias-{{ i }}"> valore di `formControlName` deve corrispondere a una delle chiavi usate quando hai definito il `FormGroup` nel componente.
*   **`formGroupName="nomeGruppoAnnidato"`**:
    *   UsAlias {{ i + 1 }}:</label>
      <input id="alias-{{ i }}" type="ata per collegare un elemento HTML (spesso un `<div>` o `<fieldset>`) a un `FormGrouptext" [formControlName]="i"> <!-- Binding a un indice del FormArray -->
      <button type="button" (click)="removeAlias(i)" class="remove-button">-</button>
      <!-- Messaggi di errore per` annidato all'interno del `FormGroup` principale.
*   **`formArrayName="nomeArray"`**:
    *   Usata per collegare un elemento HTML a un `FormArray`.

```html
<!-- registration l'alias -->
      <div *ngIf="aliasCtrl.invalid && (aliasCtrl.dirty || aliasCtrl.touched) && aliasCtrl.errors?.['required']" class="error-messages">
        L'alias è-reactive.component.html -->
<form [formGroup]="registrationForm" (ngSubmit)="onSubmit()">
  <div>
    <label for="username">Nome Utente:</label>
    <input type="text" id obbligatorio.
      </div>
    </div>
  </div>

  <button type="submit" [disabled]="profileForm.invalid">Invia</button>
  <button type="button" (click)="updateProfile()">Popola Par="username" formControlName="username">
    <!-- Messaggi di errore per username -->
    <div *ngIf="username?.invalid && (username?.dirty || username?.touched)" class="error-messages">
      <zialmente</button>
  <!-- <button type="button" (click)="setProfile()">Popola Completamente</button> -->
</form>

<div *ngIf="profileForm.value">
  <h3>Valore Corrente del Form:div *ngIf="username?.errors?.['required']">
        Il nome utente è obbligatorio.
      </div>
      <div *ngIf="username?.errors?.['minlength']">
        Deve avere</h3>
  <pre>{{ profileForm.value | json }}</pre>
  <h3>Stato di Validità: {{ profileForm.status }}</h3>
</div>
``````css
/* profile-editor.component.css ( almeno {{ username?.errors?.['minlength'].requiredLength }} caratteri.
      </div>
      <div *ngIf="username?.errors?.['forbiddenName']">
        Il nome "{{ username?.errors?.['forbiddenName'].value }}" non è consentito.
      </div>
    </div>
  </div>

  <div>
    <label for="email">esempio) */
.error-messages { color: red; font-size: 0.9em; margin-top: 4px; }
input.ng-invalid.ng-touched { border: 1px solid red; }Email:</label>
    <input type="email" id="email" formControlName="email">
    <!-- Messaggi di errore per email -->
    <div *ngIf="email?.invalid && (email?.dirty || email?.touched)" class="error-messages">
      <div *ngIf="email?.errors?.
.alias-item { margin-bottom: 10px; display: flex; align-items: center; }
.alias-item label { margin-right: 5px; }
.alias-item input { flex-grow: 1; margin-right: 5px; }
.add-button, .['required']">L'email è obbligatoria.</div>
      <div *ngIf="email?.errors?.['email']">Formato email non valido.</div>
    </div>
  </div>

  <div>
    <labelremove-button { margin-left: 10px; }
```

*   **Direttive Chiave nel Template:**
    *   `[formGroup]="profileForm"`: Collega l'elemento `<form>` all for="password">Password:</label>
    <input type="password" id="password" formControlName="password">
    <!-- Messaggi di errore per password -->
  </div>

  <div>
    <label for="confirmPassword">Conferma Password:</label>
    <input type="password" id="confirmPassword" formControlName'istanza di `FormGroup` (`profileForm`) definita nel componente.
    *   `formControlName="confirmPassword">
    <div *ngIf="confirmPassword?.touched && registrationForm.errors?.['passwordMismatch'] && password?.value" class="error-messages">
      Le password non corrispondono.
    </div>
  </div>="controlName"`: Collega un elemento di input (come `<input>`, `<select>`) a un `FormControl` specifico all'interno del `FormGroup`. Il `controlName` deve corrispondere alla chiave usata quando hai creato il `FormControl

  <!-- FormGroup Annidato per l'indirizzo -->
  <div formGroupName="address">
    <h3>Indirizzo</h3>
    <div>
      <label for="street">Via:</label>
      <input type="text" id` nel `FormGroup` (es. `firstName`).
    *   `formGroupName="groupName"`: Collega un elemento contenitore (come un `<div>`) a un `FormGroup` annidato all'interno del `FormGroup` principale.
    *   `formArrayName="arrayName"`: Collega un elemento contenitore a un ="street" formControlName="street">
    </div>
    <div>
      <label for="city">Città:</label>
      <input type="text" id="city" formControlName="city">
      `FormArray` all'interno del `FormGroup` principale. All'interno di questo, userai `*ngFor` per iterare sui controlli del `FormArray` e `[formControlName]="i"` (dove `i` è<!-- Messaggi di errore per city -->
      <div *ngIf="city?.invalid && (city?.dirty || city?.touched)" class="error-messages">
        <div *ngIf="city?.errors?.['required l'indice) per collegare ogni input a un controllo specifico nell'array.

**4. Validatori S']">La città è obbligatoria.</div>
      </div>
    </div>
  </div>

  <!-- FormArray per le skill -->
  <div formArrayName="skills">
    <h3>Skills <button type="button" (click)="addSkillincroni e Asincroni Personalizzati**

Angular fornisce una suite di validatori comuni tramite la classe `Validators` (es. `Validators.required`, `Validators.minLength`, `Validators.email`, `Validators.pattern`). Puoi()">Aggiungi Skill</button></h3>
    <div *ngFor="let skillCtrl of skills.controls; let anche creare i tuoi validatori personalizzati.

*   **Validatori Sincroni Personalizzati:**
    Un i = index" [formGroupName]="i" class="skill-item">
      <input type="text" [ validatore sincrono è una funzione che prende un `AbstractControl` (la classe base per `FormControl`, `FormGroupformControlName]="i"> <!-- Questo non è il modo corretto di bindare al FormControl dentro il FormArray -->`, `FormArray`) come argomento e restituisce:
    *   `null` se il controllo è valido.
    *   Un oggetto `ValidationErrors` (es. `{ myCustomError: true }` o `{ errorDetails: {
      <!-- CORREZIONE: Quando itero su skills.controls, skillCtrl è già il FormControl. -->
      <!-- Quindi dovrei usare [formControl]="skillCtrl" invece di formGroupName e formControlName. -->
      <!-- ... } }`) se il controllo è invalido.

    ```typescript
    // forbidden-name.validator.ts Però, per semplicità, spesso si fa un array di FormGroup nel FormArray. -->
      <!-- Rived (esempio)
    import { AbstractControl, ValidationErrors, ValidatorFn } from '@angular/forms';

    export function forbiddenNameValidator(forbiddenName: RegExp): ValidatorFn {
      return (control: AbstractControl): ValidationErrors |iamo l'esempio di 'skills' per usare FormControl direttamente o un FormGroup per ogni skill -->
    </div>
  </div>
  <!-- CORREZIONE ESEMPIO FormArray 'skills':
       Se 'skills' è un FormArray di FormControl null => {
        const isForbidden = forbiddenName.test(control.value);
        return isForbidden ? { forbiddenName: { value: control.value } } : null;
      };
    }

    // Nel componente, semplici, come aggiunto da addSkill():
  -->
  <div formArrayName="skills">
    <h3>Skills <button type="button" (click)="addSkill()">Aggiungi Skill</button></h3>
    <div * quando crei il FormControl:
    // username: new FormControl('', [Validators.required, forbiddenNameValidator(/bobngFor="let skillCtrl of skills.controls; let i = index" class="skill-item">
      <label for="skill-{{i}}">Skill {{i + 1}}:</label>
      <input type/i)])
    ```

*   **Validatori Asincroni Personalizzati:**
    Un validatore asincrono è una funzione che prende un `AbstractControl` e restituisce una `Promise<ValidationErrors | null>` o un="text" id="skill-{{i}}" [formControl]="skillCtrlAsFormControl(i)"> <!-- Lega direttamente `Observable<ValidationErrors | null>`. Sono utili per validazioni che richiedono una chiamata a un server (es. verificare se un nome utente è già in uso).

    ```typescript
    // unique-username.validator.ts ( il FormControl -->
      <button type="button" (click)="removeSkill(i)" class="remove-skill-btn">Rimuovi</button>
      <div *ngIf="skillCtrlAsFormControl(i).invalid && (skillCtrlAsFormControl(i).dirty || skillCtrlAsFormControl(i).touched)" class="error-messagesesempio concettuale)
    import { AbstractControl, AsyncValidatorFn, ValidationErrors } from '@angular/forms';
    import { Observable, of } from 'rxjs';
    import { map, delay, catchError } from 'rxjs/operators';
    // import { UserService } from './user.service'; // Assumendo un servizio che controlla l'univ">
        <div *ngIf="skillCtrlAsFormControl(i).errors?.['required']">La skill è obbligatoria.</div>
      </div>
    </div>
  </div>


  <button type="submit" [disabled]="registrationocità

    export function createUniqueUsernameValidator(/* userService: UserService */): AsyncValidatorFn {
      return (control: AbstractControl): Promise<ValidationErrors | null> | Observable<ValidationErrors | null> => {
        if (!Form.invalid">Registrati</button>
</form>

<div *ngIf="formSubmittedData">
  <h3>Dati Inviati (Reactive):</h3>
  <pre>{{ formSubmittedData | json }}</pre>
control.value) {
          return of(null); // Non validare se il campo è vuoto
        }
</div>
```

Nel componente, per far funzionare il binding `[formControl]` nel `*ngFor` delle skills:
```typescript
// registration-reactive.component.ts (aggiunta)
// ...
export        // Simula una chiamata a un servizio
        // return userService.isUsernameTaken(control.value).pipe(
        return of(control.value === 'testuser').pipe( // Simula 'testuser' come già preso
           class RegistrationReactiveComponent implements OnInit {
  // ...
  skillCtrlAsFormControl(index: number): FormControl {
    return this.skills.at(index) as FormControl;
  }
  // ...
}
```
*delay(1000), // Simula latenza di rete
          map(isTaken => (isTaken ? { usernameTaken: true } : null)),
          catchError(() => of(null)) // Gestisci errori della chiamata,   **Nessun `[(ngModel)]`:** Nota che non usiamo `ngModel` o `name` attributes considera il campo valido
        );
      };
    }

    // Nel componente:
    // email: new FormControl('', per il binding nei Reactive Forms (a meno che tu non stia specificamente mescolando gli approcci, il che è raro e generalmente non raccomandato per i controlli principali del form).

**5. Validatori Sincroni e As
    //   [Validators.required, Validators.email], // Validatori sincroni
    //   [createUniqueUsernameValidator(/* this.userService */)] // Validatori asincroni
    // )
    ```

**5. Gestincroni Personalizzati**

*   **Validatori Sincroni Personalizzati:**
    *   Sono funzioni che prendono un `AbstractControl` come argomento e restituiscono:
        *   `null` se il controllo è validoione Dinamica dei Form**

Con i Reactive Forms, è facile:

*   **Aggiungere o Rimuovere Controlli Dinamicamente:** Specialmente con `FormArray` (usando `push()`, `removeAt.
        *   Un oggetto `ValidationErrors` (es. `{ 'nomeErrore': true }` o `{ '()`, `insert()`).
*   **Impostare o Aggiornare Valori:**
    *   nomeErrore': { dettagli: 'info' } }`) se il controllo è invalido.
    *   L`form.setValue(obj)`: Imposta i valori per *tutti* i controlli nel gruppo. L'oggetto 'esempio `forbiddenNameValidator` e `passwordMatchValidator` sopra mostra come crearli.

*   **Validatori Asincroni Personalizzati:**
    *   Sono funzioni che prendono un `AbstractControl` e restituiscono un`obj` deve avere la stessa struttura del `FormGroup`.
    *   `form.patchValue(obj)`: Aggiorna i valori solo per i controlli specificati nell'oggetto `obj`. Non dà errore se mancano dei `Promise<ValidationErrors | null>` o un `Observable<ValidationErrors | null>`.
    *   Utili per validazioni che richiedono una chiamata al server (es. verificare se un username è già in uso).
    * controlli.
    *   `control.setValue(val)`: Imposta il valore di un singolo `FormControl`.
*   Esempio concettuale:
        ```typescript
        // uniqueUsernameValidator(userService: UserService): Async   **Resettare il Form:**
    *   `form.reset(optionalValue?)`: Resetta loValidatorFn {
        //   return (control: AbstractControl): Observable<ValidationErrors | null> => {
        //     return userService.checkUsernameExists(control.value).pipe(
        //       map(exists => stato del form (valori a `null` o al valore iniziale, stato a `pristine`, `untouched`) e rimuove gli errori. Puoi passare un oggetto per resettare a valori specifici.
*   **Ab (exists ? { 'usernameTaken': true } : null)),
        //       catchError(() => of(null)) // Gestisci errori API
        //     );
        //   };
        // }
        // ...
        ilitare/Disabilitare Controlli:**
    *   `control.disable()`
    *   `control.// email: [null, [Validators.required, Validators.email], [this.uniqueUsernameValidator(this.enable()`

**Quando Usare i Reactive Forms:**

*   Per form complessi con molti campi e logica di validuserService)]],
        ```

**6. Gestione Dinamica dei Form (es. con `FormArrayazione sofisticata.
*   Quando hai bisogno di validazioni dinamiche (es. un campo diventa obbligatorio in`)**

`FormArray` è ideale per scenari in cui l'utente può aggiungere o rimuovere dinamicamente un insieme di controlli (es. più numeri di telefono, indirizzi, o le "skills" nel nostro esempio).

*    base al valore di un altro).
*   Quando vuoi creare validatori personalizzati complessi (specialmente asincroniNell'esempio sopra, `addSkill()` aggiunge un nuovo `FormControl` all'array `skills`.
*   ).
*   Se preferisci un approccio più programmatico e avere il modello del form definito esplicitamente nel codice`removeSkill(index)` rimuove un controllo da una specifica posizione.
*   Nel template, usi `*ngFor TypeScript.
*   Quando la testabilità unitaria del form è una priorità alta.
*   Per form generati dinamicamente in base a metadati.

---

I Reactive Forms offrono un controllo molto più granul` per iterare sui `skills.controls`. Ogni item dell'iterazione è un `AbstractControl` (in questo caso, un `FormControl`).

**Quando Usare i Reactive Forms:**

*   Per form complessi con molte interare e un approccio più robusto alla gestione dei form rispetto ai Template-Driven Forms, specialmente per scenari compldipendenze tra campi.
*   Quando hai bisogno di validazioni dinamiche o personalizzate complesse.essi. Richiedono un po' più di codice di setup nel componente, ma la flessibilità e la test
*   Se la struttura del form può cambiare programmaticamente.
*   Quando la testabilità della logica del formabilità che offrono sono spesso un grande vantaggio.

Questo conclude la panoramica sui Reactive Forms e anche il Modulo 6 sulla Gestione dei Form.
