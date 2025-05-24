Certamente! Concludiamo il Modulo 11 con la **Lezione 29: Debugging di Applicazioni Angular**. Saper debuggare efficacemente è un'abilità essenziale per qualsiasi sviluppatore, poiché i bug sono una parte inevitabile del processo di sviluppo.

---

### Modulo 11: Test e Debugging

**Lezione 29: Debugging di Applicazioni Angular**

Il debugging è il processo di identificazione, analisi e correzione dei difetti (bug) nel codice del software. Fortunatamente, l'ecosistema Angular e i moderni browser web offrono potenti strumenti e tecniche per facilitare questo processo.

**1. Utilizzo dei DevTools del Browser**

I DevTools (Strumenti per Sviluppatori) integrati nei browser moderni (come Chrome, Firefox, Edge) sono la prima linea di difesa e lo strumento più utilizzato per il debugging di applicazioni web, incluse quelle Angular.

*   **Console:**
    *   **`console.log()`**: Il metodo più semplice e spesso il primo approccio per stampare i valori delle variabili, messaggi di stato, o per tracciare il flusso di esecuzione.
        ```typescript
        // Nel tuo componente o servizio
        console.log('Valore corrente:', this.miaVariabile);
        console.log('Oggetto utente:', this.utente); // Stampa l'oggetto
        console.table(this.arrayDiOggetti); // Stampa un array di oggetti in formato tabellare (molto utile)
        console.error('Si è verificato un errore:', errore);
        console.warn('Attenzione: valore imprevisto.');
        console.group('Operazione Complessa'); // Raggruppa log correlati
        console.log('Passaggio 1...');
        console.log('Passaggio 2...');
        console.groupEnd();
        ```
    *   **Errori e Warning:** La console mostra errori JavaScript, errori di rete, e warning che possono indicare problemi nella tua applicazione. Presta attenzione a questi messaggi!
    *   **Console API:** Offre molte altre funzioni utili come `console.dir()` (per ispezionare le proprietà di un oggetto), `console.time()` e `console.timeEnd()` (per misurare la durata di un'operazione), `console.trace()` (per mostrare lo stack trace).

*   **Debugger (Sources Panel in Chrome DevTools):**
    *   Permette di impostare **breakpoint** nel tuo codice TypeScript (grazie ai source maps generati da Angular CLI). Quando l'esecuzione del codice raggiunge un breakpoint, si ferma, permettendoti di:
        *   **Ispezionare Variabili:** Vedere i valori delle variabili locali e globali nello scope corrente.
        *   **Watch Expressions:** Aggiungere espressioni da monitorare il cui valore viene aggiornato mentre esegui il codice passo-passo.
        *   **Call Stack:** Vedere la catena di chiamate di funzioni che ha portato al breakpoint corrente.
        *   **Step Over, Step Into, Step Out:** Controllare l'esecuzione del codice passo-passo.
            *   **Step Over (F10):** Esegue la riga corrente e si ferma alla successiva (non entra nelle funzioni chiamate).
            *   **Step Into (F11):** Se la riga corrente è una chiamata a funzione, entra in quella funzione.
            *   **Step Out (Shift+F11):** Esegue il resto della funzione corrente e si ferma alla riga successiva nel chiamante.
            *   **Resume (F8):** Continua l'esecuzione fino al prossimo breakpoint o alla fine.
    *   **Conditional Breakpoints:** Breakpoint che si attivano solo se una certa condizione è vera.
    *   **Logpoints:** Invece di fermare l'esecuzione, stampano un messaggio o un'espressione nella console. Utile per il logging senza modificare il codice.
    *   **DOM Breakpoints:** Puoi impostare breakpoint che si attivano quando un nodo DOM specifico viene modificato (es. modifiche agli attributi, rimozione del nodo, aggiunta di figli). (Nel pannello Elements).

*   **Elements Panel:**
    *   Ispeziona e modifica l'HTML e il CSS della pagina renderizzata in tempo reale.
    *   Puoi vedere come Angular ha renderizzato i tuoi componenti, quali stili sono applicati, e sperimentare con modifiche CSS al volo.
    *   Molto utile per debuggare problemi di layout e stile.

*   **Network Panel:**
    *   Mostra tutte le richieste di rete fatte dalla tua applicazione (chiamate HTTP, download di immagini, script, stili).
    *   Puoi ispezionare:
        *   Gli header della richiesta e della risposta.
        *   Il corpo (payload) della richiesta e della risposta.
        *   I tempi di risposta.
        *   I codici di stato HTTP.
    *   Fondamentale per debuggare problemi con le chiamate API (vedi Modulo 7).
    *   Puoi filtrare le richieste, vedere il timing, e anche rieseguire richieste.

*   **Application Panel (o Storage Panel):**
    *   Ispeziona e modifica i dati memorizzati nel browser, come:
        *   `localStorage`
        *   `sessionStorage`
        *   IndexedDB
        *   Web SQL
        *   Cookies
    *   Utile se la tua applicazione usa queste API di storage.

**2. Angular DevTools (Estensione del Browser)**

Angular DevTools è un'estensione ufficiale per Chrome ed Edge che fornisce strumenti specifici per il debugging di applicazioni Angular.

*   **Installazione:** Cercala nel Chrome Web Store o Microsoft Edge Add-ons e installala.
*   **Funzionalità Principali:**
    *   **Component Explorer:**
        *   Visualizza l'albero dei componenti della tua applicazione così come è renderizzato.
        *   Puoi selezionare un componente nell'albero per ispezionare le sue proprietà (`@Input()`, stato interno, valori dei signal), e a volte anche modificarle al volo (per alcuni tipi di dati).
        *   Mostra la gerarchia dei componenti, aiutando a capire come sono annidati.
    *   **Profiler:**
        *   Aiuta a identificare i colli di bottiglia nelle performance della change detection.
        *   Puoi registrare una sessione di interazione con la tua app, e il profiler ti mostrerà quanto tempo Angular ha impiegato per la change detection in ogni componente e quali componenti sono stati controllati.
        *   Utile per ottimizzare le performance.
    *   **Router Tree:** Visualizza la configurazione delle tue rotte. (Funzionalità in evoluzione)
    *   **Injector Tree:** Permette di ispezionare la gerarchia degli injector e i provider disponibili. (Funzionalità in evoluzione)

*   **Come Usarla:**
    1.  Apri i DevTools del browser (F12).
    2.  Cerca la tab "Angular" (potrebbe essere sotto ">>" se hai molte tab).
    3.  Assicurati che la tua applicazione Angular sia in esecuzione in modalità sviluppo (non in produzione, perché la modalità produzione disabilita alcune funzionalità di debugging per ottimizzare le performance).

**3. Tecniche di Logging e Breakpoint Strategici**

*   **Logging Efficace:**
    *   Non limitarti a `console.log('qui')`. Stampa valori significativi, messaggi descrittivi, e il contesto.
    *   Usa `console.group()` per raggruppare log correlati e rendere l'output più leggibile.
    *   Considera di creare un servizio di logging personalizzato se hai bisogno di log più strutturati o di inviarli a un server in produzione.
    *   Rimuovi o disabilita i log di debug eccessivi prima di andare in produzione.

*   **Breakpoint Strategici:**
    *   Invece di mettere breakpoint a caso, pensa a dove il problema potrebbe originarsi.
    *   Metti breakpoint all'inizio e alla fine di funzioni sospette, o prima di una riga di codice che causa un errore.
    *   Usa i conditional breakpoint per fermarti solo quando si verifica una situazione specifica (es. `miaVariabile > 10`).
    *   Usa la Call Stack per capire come sei arrivato a un certo punto del codice.

**4. Debugging di Errori Comuni in Angular:**

*   **Errori di Template:**
    *   **`NG0904: Error trying to diff '[object Object]'` (o simili `NG0900: Error trying to diff...`):** Spesso significa che stai cercando di usare un oggetto complesso direttamente in un'interpolazione `{{ }}` o con `*ngFor` senza un pipe (come `| json` per l'interpolazione o `| keyvalue` per `*ngFor` su oggetti) o senza accedere a una sua proprietà specifica.
    *   **`ExpressionChangedAfterItHasBeenCheckedError`:** Questo errore si verifica quando un'espressione usata nel template cambia valore *dopo* che Angular ha completato il suo ciclo di change detection per quella vista. È un meccanismo di sicurezza per prevenire inconsistenze. Cause comuni:
        *   Modificare una proprietà del componente in `ngAfterViewInit` o `ngAfterContentChecked` senza triggerare un nuovo ciclo di change detection in modo appropriato (es. con `setTimeout` o `Promise.resolve().then()`).
        *   Binding a metodi che restituiscono valori diversi ad ogni chiamata durante lo stesso ciclo di change detection.
        *   Modificare lo stato di un servizio da un lifecycle hook "after" in modo che influenzi la vista corrente.
        *   I Signals tendono a ridurre l'incidenza di questo errore grazie al loro modello di reattività più granulare.
    *   **`NullInjectorError: No provider for ServiceName!`**: Significa che stai cercando di iniettare un servizio, ma Angular non sa come crearlo. Assicurati che il servizio sia fornito correttamente (es. `@Injectable({ providedIn: 'root' })`, o nell'array `providers` del modulo/componente/configurazione app).
    *   **Errori di Sintassi nel Template:** La console del browser spesso dà indizi sulla riga e sul tipo di errore.

*   **Debugging dei Signals:**
    *   Usa `console.log(mioSignal())` per vedere il valore corrente.
    *   Usa `effect()` per loggare i cambiamenti a un signal o a un `computed` signal.
        ```typescript
        // constructor() {
        //   effect(() => {
        //     console.log('Mio signal dipendente è:', this.mioComputedSignal());
        //   });
        // }
        ```
    *   Angular DevTools può aiutare a ispezionare i valori dei signal nei componenti.

*   **Debugging di RxJS Observables:**
    *   Usa l'operatore `tap()` per loggare i valori emessi, gli errori, o il completamento di un Observable senza alterare il flusso.
        ```typescript
        // this.myObservable$.pipe(
        //   tap(value => console.log('Valore Observable:', value)),
        //   map(value => /* ... */),
        //   catchError(err => {
        //     console.error('Errore Observable:', err);
        //     return throwError(() => err);
        //   })
        // ).subscribe();
        ```
    *   Usa i breakpoint all'interno delle callback `next`, `error`, e `complete` di `subscribe()`.
    *   Sii consapevole della gestione delle sottoscrizioni per evitare memory leak.

*   **Usare i Source Maps:**
    Angular CLI genera source maps per default in modalità sviluppo. I source maps mappano il codice JavaScript compilato (che il browser esegue) al tuo codice TypeScript originale. Questo ti permette di vedere e debuggare il tuo codice TypeScript direttamente nel pannello Sources dei DevTools del browser, invece del JavaScript generato. Assicurati che siano abilitati nel tuo browser.

**5. Debugging con l'IDE (es. VS Code)**

Molti IDE moderni, come Visual Studio Code, offrono eccellenti funzionalità di debugging per applicazioni JavaScript/TypeScript, incluse quelle Angular.

*   **Configurazione del Debugger:** Puoi configurare VS Code per lanciare l'applicazione Angular e attaccare il debugger del browser direttamente dall'IDE. Cerca l'estensione "JavaScript Debugger" (di solito integrata) e crea un file `launch.json` con la configurazione appropriata (spesso può essere generata automaticamente o con un semplice setup).
*   **Breakpoint nell'IDE:** Puoi impostare breakpoint, ispezionare variabili, vedere la call stack, ed eseguire il codice passo-passo direttamente nel tuo editor, offrendo un'esperienza di debugging più integrata.

**Consigli Generali per il Debugging:**

*   **Riproduci il Bug:** Cerca di trovare i passaggi esatti per riprodurre il bug in modo consistente.
*   **Isola il Problema:** Cerca di restringere il campo a quale parte del codice (componente, servizio, metodo) sta causando il problema. Commenta sezioni di codice per vedere se il bug scompare.
*   **Leggi i Messaggi di Errore Attentamente:** Gli errori spesso forniscono indizi preziosi.
*   **"Divide et Impera":** Se hai una funzione o un flusso complesso, dividilo in parti più piccole e verifica ogni parte.
*   **Fai Piccoli Cambiamenti e Testa Spesso:** Evita di scrivere grandi quantità di codice prima di testarlo.
*   **Rubber Duck Debugging:** Spiega il problema ad alta voce (a una paperella di gomma, a un collega, o anche solo a te stesso). Spesso, l'atto di verbalizzare il problema aiuta a identificare la soluzione.
*   **Controllo Versione (Git):** Usa il controllo versione per poter tornare a versioni precedenti del codice se introduci un bug difficile da trovare. `git bisect` può essere molto utile per trovare il commit che ha introdotto una regressione.

---

Il debugging è un'arte e una scienza. Con gli strumenti giusti e un approccio metodico, puoi diventare molto efficiente nel trovare e risolvere i problemi nelle tue applicazioni Angular.

Questo conclude il Modulo 11 su Test e Debugging. Nel prossimo e ultimo modulo, "Build, Deployment e Novità di Angular 19", vedremo come preparare la nostra applicazione per la produzione e daremo uno sguardo alle possibili novità della futura versione di Angular.
