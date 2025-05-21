Assolutamente! Continuiamo con la **Lezione 3: Introduzione a TypeScript**.

---

### Modulo 1: Fondamenti di Angular e Configurazione dell'Ambiente

**Lezione 3: Introduzione a TypeScript**

TypeScript è un linguaggio di programmazione open-source sviluppato da Microsoft. È un **superset tipizzato di JavaScript**, il che significa che tutto il codice JavaScript valido è anche codice TypeScript valido, ma TypeScript aggiunge funzionalità extra, la più importante delle quali è la **tipizzazione statica opzionale**. Angular è scritto interamente in TypeScript e lo raccomanda (e di fatto lo richiede in pratica) per lo sviluppo delle applicazioni.

**1. Perché TypeScript in Angular?**

L'utilizzo di TypeScript in Angular offre numerosi vantaggi, specialmente per applicazioni di grandi dimensioni e complesse:

*   **Tipizzazione Statica (Static Typing):**
    *   **Definizione:** Permette di specificare i tipi di dati per variabili, parametri di funzioni, valori di ritorno delle funzioni e proprietà degli oggetti.
    *   **Vantaggi:**
        *   **Rilevamento Precoce degli Errori:** Molti errori comuni (es. passare una stringa a una funzione che si aspetta un numero) vengono individuati durante la fase di compilazione (o addirittura dall'IDE mentre scrivi il codice), prima che l'applicazione venga eseguita nel browser. Questo riduce significativamente i bug in produzione.
        *   **Migliore Leggibilità e Comprensione del Codice:** I tipi rendono il codice più auto-documentante. È più facile capire che tipo di dati una funzione si aspetta o restituisce.
        *   **Refactoring più Sicuro:** Gli strumenti di refactoring possono sfruttare le informazioni sui tipi per apportare modifiche al codice in modo più affidabile.
        *   **Migliore Autocompletamento e IntelliSense:** Gli editor di codice (come VS Code) possono fornire suggerimenti più accurati e autocompletamento basati sui tipi definiti.

*   **Funzionalità Avanzate del Linguaggio:**
    *   TypeScript include funzionalità delle versioni più recenti di JavaScript (ECMAScript), come classi, moduli, arrow functions, async/await, e le rende disponibili anche se il browser di destinazione non le supporta nativamente (TypeScript compilerà il codice in una versione di JavaScript compatibile).
    *   Aggiunge anche proprie funzionalità come interfacce, enums, generics, decorators, che sono estremamente utili in Angular.

*   **Scalabilità e Manutenibilità:**
    *   In progetti grandi con molti sviluppatori, la tipizzazione statica aiuta a mantenere la coerenza e a ridurre gli errori dovuti a incomprensioni sull'uso dei dati.
    *   Rende più facile per i nuovi sviluppatori comprendere una codebase esistente.

*   **Strumentazione Migliore:**
    *   La community e gli strumenti attorno a TypeScript sono eccellenti, con un forte supporto negli IDE più popolari.

**Come funziona TypeScript?**
Il codice TypeScript (`.ts` files) non può essere eseguito direttamente dai browser. Deve prima essere **compilato** (o **transpilato**) in JavaScript standard (`.js` files). Questo processo di compilazione viene gestito automaticamente da Angular CLI quando esegui `ng serve` o `ng build`. Durante la compilazione, il compilatore TypeScript (chiamato `tsc`) controlla la correttezza dei tipi e converte il codice TypeScript in JavaScript che i browser possono capire.

**2. Tipi di Base**

TypeScript estende i tipi di base di JavaScript con un sistema di tipi più ricco. Ecco i più comuni:

*   **`boolean`**: Rappresenta un valore booleano, `true` o `false`.
    ```typescript
    let isActive: boolean = true;
    let isLoggedIn: boolean = false;
    ```

*   **`number`**: Rappresenta tutti i tipi di numeri (interi e decimali).
    ```typescript
    let userId: number = 123;
    let price: number = 19.99;
    ```

*   **`string`**: Rappresenta sequenze di caratteri. Puoi usare apici singoli (`'`) o doppi (`"`). Sono supportate anche le *template literals* (stringhe con backtick `` ` ``) per stringhe multi-linea e interpolazione.
    ```typescript
    let username: string = "JohnDoe";
    let greeting: string = 'Hello';
    let message: string = `User ${username} has ID ${userId}.`;
    ```

*   **`array`**: Rappresenta un array di valori. Ci sono due modi per definire un tipo array:
    1.  `tipoElemento[]`
    2.  `Array<tipoElemento>`
    ```typescript
    let numbers: number[] = [1, 2, 3, 4, 5];
    let names: string[] = ["Alice", "Bob", "Charlie"];
    let list: Array<number> = [10, 20, 30];
    ```

*   **`tuple`**: Permette di definire un array con un numero fisso di elementi i cui tipi sono noti, ma non necessariamente uguali. L'ordine è importante.
    ```typescript
    let userProfile: [string, number, boolean];
    userProfile = ["Alice", 30, true]; // OK
    // userProfile = [30, "Alice", true]; // Errore: l'ordine dei tipi non corrisponde
    // userProfile = ["Bob", 25]; // Errore: numero di elementi non corretto
    ```

*   **`enum` (Enumeration)**: Un modo per dare nomi più amichevoli a insiemi di valori numerici (o a volte stringhe).
    ```typescript
    enum Color {Red, Green, Blue}
    let c: Color = Color.Green; // c avrà il valore 1 (di default partono da 0)

    enum HttpStatus {
        Ok = 200,
        NotFound = 404,
        InternalServerError = 500
    }
    let status: HttpStatus = HttpStatus.Ok; // status avrà il valore 200
    console.log(Color[0]); // Stampa "Red"
    ```

*   **`any`**: Un tipo "jolly". Indica che una variabile può essere di qualsiasi tipo. **Usalo con cautela**, perché disabilita i controlli di tipo per quella variabile, perdendo molti dei benefici di TypeScript. È utile quando si lavora con codice JavaScript di terze parti non tipizzato o quando il tipo di un valore è veramente dinamico e sconosciuto.
    ```typescript
    let notSure: any = 4;
    notSure = "maybe a string instead";
    notSure = false; // okay, definitely a boolean
    ```

*   **`void`**: Usato principalmente come tipo di ritorno per le funzioni che non restituiscono alcun valore.
    ```typescript
    function logMessage(message: string): void {
        console.log(message);
        // non c'è un'istruzione return con un valore
    }
    ```

*   **`null` e `undefined`**: Rappresentano rispettivamente l'assenza intenzionale di un valore oggetto e una variabile non inizializzata. Di default, `null` e `undefined` sono sottotipi di tutti gli altri tipi. Ciò significa che puoi assegnare `null` o `undefined` a una variabile di tipo `string` o `number`, a meno che non sia abilitata l'opzione di compilazione `strictNullChecks` (altamente raccomandata e abilitata di default nei nuovi progetti Angular).
    ```typescript
    let u: undefined = undefined;
    let n: null = null;

    // Con strictNullChecks = true (default in Angular)
    // let name: string = null; // Errore
    // let age: number = undefined; // Errore

    // Per permettere null o undefined esplicitamente:
    let nullableName: string | null = null;
    let optionalAge: number | undefined = undefined;
    ```

*   **`never`**: Rappresenta il tipo di valori che non si verificano mai. Ad esempio, una funzione che lancia sempre un'eccezione o che ha un loop infinito ha `never` come tipo di ritorno.
    ```typescript
    function throwError(message: string): never {
        throw new Error(message);
    }

    function infiniteLoop(): never {
        while (true) {}
    }
    ```

*   **`object`**: Rappresenta un tipo non primitivo, cioè qualsiasi cosa che non sia `number`, `string`, `boolean`, `bigint`, `symbol`, `null`, o `undefined`. È meno specifico di tipi come interfacce o classi.
    ```typescript
    let genericObject: object;
    genericObject = { name: "Test" };
    // genericObject = 42; // Errore
    ```

**3. Interfacce e Classi**

Questi sono costrutti fondamentali per definire la "forma" degli oggetti e creare oggetti riutilizzabili.

*   **Interfacce (Interfaces):**
    *   Un'interfaccia definisce un **contratto** per la struttura di un oggetto. Specifica quali proprietà un oggetto dovrebbe avere e i tipi di quelle proprietà.
    *   Le interfacce sono utili per la type-checking e non vengono compilate in JavaScript (sono un costrutto di design-time).
    *   Possono descrivere la forma di oggetti, parametri di funzioni, e classi (attraverso la clausola `implements`).
    ```typescript
    interface Person {
        firstName: string;
        lastName: string;
        age?: number; // '?' indica una proprietà opzionale
        readonly id: number; // 'readonly' indica che la proprietà non può essere modificata dopo l'inizializzazione
        greet?(): string; // Metodo opzionale che restituisce una stringa
    }

    function printPersonDetails(person: Person) {
        console.log(`Name: ${person.firstName} ${person.lastName}`);
        if (person.age) {
            console.log(`Age: ${person.age}`);
        }
        console.log(`ID: ${person.id}`);
        if (person.greet) {
            console.log(person.greet());
        }
    }

    let user1: Person = {
        firstName: "John",
        lastName: "Doe",
        age: 30,
        id: 1,
        greet: () => `Hello, I'm John`
    };

    let user2: Person = {
        firstName: "Jane",
        lastName: "Smith",
        id: 2
        // age e greet sono opzionali
    };

    printPersonDetails(user1);
    printPersonDetails(user2);
    // user1.id = 5; // Errore: 'id' è readonly
    ```

*   **Classi (Classes):**
    *   Le classi sono un blueprint per creare oggetti (istanze). Sono una funzionalità di ECMAScript 2015 (ES6) e TypeScript le estende con funzionalità come la tipizzazione.
    *   Le classi possono avere proprietà, metodi, costruttori, e possono implementare interfacce.
    *   A differenza delle interfacce, le classi vengono compilate in funzioni costruttore JavaScript e quindi esistono a runtime.
    ```typescript
    class Employee implements Person { // La classe Employee deve rispettare il contratto dell'interfaccia Person
        firstName: string;
        lastName: string;
        age?: number;
        readonly id: number;
        department: string;

        constructor(id: number, firstName: string, lastName: string, department: string, age?: number) {
            this.id = id;
            this.firstName = firstName;
            this.lastName = lastName;
            this.department = department;
            if (age) {
                this.age = age;
            }
        }

        greet(): string {
            return `Hello, my name is ${this.firstName} ${this.lastName} and I work in ${this.department}.`;
        }

        getEmployeeInfo(): string {
            return `${this.firstName} ${this.lastName} (ID: ${this.id}) - ${this.department}`;
        }
    }

    let emp1 = new Employee(101, "Peter", "Jones", "IT", 35);
    console.log(emp1.greet());
    console.log(emp1.getEmployeeInfo());
    printPersonDetails(emp1); // Un'istanza di Employee è anche una Person
    ```
    *   Le classi supportano anche l'**ereditarietà** (con `extends`), **modificatori di accesso** (`public`, `private`, `protected`), proprietà e metodi **statici**, e **classi astratte**. Questi concetti saranno più chiari quando inizieremo a costruire componenti Angular.

**4. Decorators (Cenni)**

*   I Decorators sono una speciale tipo di dichiarazione che può essere associata a una **classe**, un **metodo**, un **accessor**, una **proprietà**, o un **parametro**.
*   Utilizzano la sintassi `@expression`, dove `expression` deve valutare a una funzione che verrà chiamata a runtime con informazioni sulla dichiarazione decorata.
*   **Angular usa ampiamente i Decorators** per aggiungere metadati alle classi e per modificarne il comportamento. Ad esempio:
    *   `@Component` (per definire un componente Angular)
    *   `@NgModule` (per definire un modulo Angular - meno comune con i standalone components)
    *   `@Injectable` (per definire un servizio che può essere iniettato)
    *   `@Input` e `@Output` (per la comunicazione tra componenti)
*   Li vedremo in azione molto presto quando creeremo il nostro primo componente. Per ora, è sufficiente sapere che esistono e che sono una parte cruciale di come funziona Angular.
    ```typescript
    // Esempio concettuale di un decorator (non codice Angular specifico qui)
    function simpleDecorator(constructor: Function) {
        console.log('Decorator called on:', constructor);
    }

    @simpleDecorator
    class MyClass {
        constructor() {
            console.log('MyClass instance created.');
        }
    }

    let instance = new MyClass();
    ```

**5. Funzioni e Tipi di Funzione**

TypeScript permette di definire tipi per i parametri delle funzioni e per i valori di ritorno.

```typescript
// Funzione con parametri tipizzati e tipo di ritorno
function add(x: number, y: number): number {
    return x + y;
}

let result: number = add(5, 3); // result = 8
// let wrongResult = add("5", 3); // Errore: il primo argomento non è un numero

// Arrow function con tipi
const subtract = (x: number, y: number): number => {
    return x - y;
};

// Tipi di funzione (per definire la "forma" di una funzione)
let multiply: (a: number, b: number) => number;

multiply = function(num1, num2) { // TypeScript può inferire i tipi num1, num2 qui
    return num1 * num2;
};
// Oppure:
// multiply = (num1: number, num2: number): number => num1 * num2;

console.log(multiply(4, 5)); // 20

// Parametri opzionali e di default
function buildName(firstName: string, lastName?: string): string { // lastName è opzionale
    if (lastName) {
        return firstName + " " + lastName;
    } else {
        return firstName;
    }
}
console.log(buildName("Bob")); // "Bob"
console.log(buildName("Bob", "Adams")); // "Bob Adams"

function greetUser(name: string, greeting: string = "Hello"): string { // greeting ha un valore di default
    return `${greeting}, ${name}!`;
}
console.log(greetUser("Alice")); // "Hello, Alice!"
console.log(greetUser("Alice", "Hi")); // "Hi, Alice!"
```

**6. Moduli TypeScript**

*   In JavaScript (da ES6 in poi) e quindi in TypeScript, i moduli permettono di organizzare il codice in file separati.
*   Ogni file TypeScript può essere considerato un modulo.
*   Per rendere disponibile una parte di codice (come una classe, interfaccia, funzione, variabile) da un modulo all'altro, si usa la parola chiave `export`.
*   Per utilizzare il codice esportato da un altro modulo, si usa la parola chiave `import`.
    ```typescript
    // --- utils.ts ---
    export const PI: number = 3.14159;

    export function calculateCircumference(diameter: number): number {
        return diameter * PI;
    }

    export interface Shape {
        area(): number;
    }

    // --- main.ts ---
    import { PI, calculateCircumference, Shape } from './utils';
    // Si può anche importare tutto da un modulo:
    // import * as Utils from './utils';
    // console.log(Utils.PI);

    console.log(`PI is ${PI}`);
    console.log(`Circumference of a circle with diameter 10: ${calculateCircumference(10)}`);

    class Circle implements Shape {
        constructor(public radius: number) {}
        area(): number {
            return PI * this.radius * this.radius;
        }
    }

    let myCircle = new Circle(5);
    console.log(`Area of circle with radius 5: ${myCircle.area()}`);
    ```
    Angular CLI gestisce la compilazione e il bundling di questi moduli per te.

---

Questa è stata un'introduzione abbastanza densa a TypeScript! Non preoccuparti se non hai afferrato tutto subito. Molti di questi concetti diventeranno più chiari e naturali man mano che li applicherai nello sviluppo di componenti e servizi Angular.

**Cosa abbiamo imparato:**

*   Perché TypeScript è importante per Angular (tipi, errori precoci, leggibilità).
*   I tipi di dati di base (boolean, number, string, array, tuple, enum, any, void, null, undefined, never, object).
*   Come definire e usare Interfacce (contratti) e Classi (blueprint per oggetti).
*   Una prima occhiata ai Decorators (che vedremo molto in Angular).
*   Come definire funzioni tipizzate e tipi di funzione.
*   L'uso di Moduli per organizzare il codice con `export` e `import`.

Nella prossima lezione, "Anatomia di un Componente", inizieremo finalmente a vedere come questi concetti di TypeScript vengono usati per costruire la struttura fondamentale di un'applicazione Angular: i **Componenti**.
