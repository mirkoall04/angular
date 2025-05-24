Perfetto! Concludiamo il nostro programma di studi con il **Modulo 12: Build, Deployment e Novità di Angular 19**. Questo modulo si concentra su come preparare la tua applicazione Angular per il mondo reale e su come rimanere aggiornati con le evoluzioni del framework.

---

### Modulo 12: Build, Deployment e Novità di Angular 19

**Lezione 30: Build di Produzione e Ottimizzazioni**

Una volta che la tua applicazione Angular è sviluppata e testata, il passo successivo è prepararla per il **deployment in produzione**. Angular CLI fornisce comandi e configurazioni per creare una build ottimizzata dell'applicazione, pronta per essere servita agli utenti finali.

**1. Comando `ng build` per la Produzione**

Il comando principale per creare una build di produzione è `ng build`. Quando eseguito senza ulteriori flag, crea una build di sviluppo. Per una build di produzione, si usa una configurazione specifica (spesso chiamata `production`).

*   **Sintassi di Base:**
    ```bash
    ng build
    # Per una build di produzione (usa la configurazione 'production' definita in angular.json):
    ng build --configuration production
    # Spesso, la configurazione 'production' è quella di default se NODE_ENV è 'production'
    # o se si omette --configuration, ma è buona pratica essere espliciti.
    # Nelle versioni più recenti, `ng build` senza flag spesso abilita già molte ottimizzazioni
    # di produzione, ma `--configuration production` garantisce tutte quelle definite.
    ```
    Nelle versioni più recenti di Angular (es. da v16/v17 in poi), `ng build` da solo tende già ad applicare molte ottimizzazioni di produzione. Tuttavia, la configurazione `production` in `angular.json` permette di personalizzare ulteriormente queste ottimizzazioni.

*   **Cosa fa `ng build --configuration production` (tipicamente):**
    1.  **Compilazione Ahead-of-Time (AOT):** (Vedi sotto)
    2.  **Minificazione del Codice:** Rimuove spazi bianchi, commenti, accorcia i nomi delle variabili nel codice JavaScript e CSS per ridurre la dimensione dei file.
    3.  **Tree Shaking:** Analizza il codice e rimuove le parti non utilizzate (es. funzioni, classi, moduli importati ma mai usati), riducendo ulteriormente la dimensione del bundle.
    4.  **Ottimizzazione degli Asset:** Ottimizza immagini e altri asset (se configurato).
    5.  **Hashing dei Nomi dei File:** Aggiunge un hash univoco ai nomi dei file dei bundle (es. `main.<hash>.js`, `styles.<hash>.css`). Questo aiuta con il **cache busting**: quando rilasci una nuova versione, i nomi dei file cambiano, costringendo il browser a scaricare le nuove versioni invece di usare quelle vecchie dalla cache.
    6.  **Sostituzione delle Variabili d'Ambiente:** Sostituisce le variabili definite nel file `src/environments/environment.prod.ts` (o il file specificato per la configurazione di produzione).
    7.  **Bundle Splitting (Suddivisione dei Bundle):** Suddivide il codice dell'applicazione in chunk più piccoli. Questo include:
        *   Un bundle principale (`main.js`).
        *   Bundle per il codice vendor (librerie di terze parti).
        *   Bundle per il codice comune condiviso tra moduli lazy-loaded.
        *   Chunk separati per ogni modulo/componente lazy-loaded.
        Questo migliora il tempo di caricamento iniziale perché il browser carica solo i chunk necessari per la vista corrente.
    8.  **Dead Code Elimination:** Rimuove codice che non verrà mai eseguito.

*   **Output della Build:**
    I file generati vengono solitamente messi nella cartella `dist/<nome-progetto>/browser/` (o solo `dist/<nome-progetto>/` in versioni precedenti o a seconda della configurazione `outputPath` in `angular.json`).
    Questa cartella contiene tutti i file statici (HTML, CSS, JS, immagini) necessari per eseguire l'applicazione. È questa cartella che devi deployare sul tuo server web.

**2. Ahead-of-Time (AOT) Compilation**

La compilazione AOT è una delle ottimizzazioni più importanti in Angular ed è abilitata per default nelle build di produzione.

*   **Just-in-Time (JIT) Compilation (usata in sviluppo):**
    *   Il browser scarica il codice Angular (incluso il compilatore Angular).
    *   Il compilatore Angular compila i template HTML e i componenti nel browser *prima* che l'applicazione possa essere renderizzata.
    *   **Svantaggi:**
        *   Bundle più grande (perché include il compilatore).
        *   Tempo di avvio più lento (a causa della compilazione nel browser).
        *   Gli errori di template vengono rilevati solo a runtime nel browser.

*   **Ahead-of-Time (AOT) Compilation (usata in produzione):**
    *   I template HTML e i componenti vengono compilati in codice JavaScript efficiente **durante la fase di build** (sul server o sulla macchina di sviluppo), prima che l'applicazione venga scaricata dal browser.
    *   Il browser scarica codice già compilato e pronto per essere eseguito.
    *   **Vantaggi:**
        *   **Bundle più Piccoli:** Il compilatore Angular non è incluso nel bundle di produzione.
        *   **Rendering più Veloce:** L'applicazione si avvia più rapidamente perché non c'è bisogno di compilare nel browser.
        *   **Rilevamento Precoce degli Errori:** Gli errori di sintassi nei template vengono rilevati durante la fase di build, non a runtime.
        *   **Maggiore Sicurezza:** Riduce il rischio di injection attacchi perché i template HTML non vengono valutati dinamicamente nel browser.

    AOT è abilitato per default quando usi `ng build --configuration production` (o spesso anche con `ng build` semplice nelle versioni recenti).

**3. Tree Shaking e altre Ottimizzazioni**

*   **Tree Shaking:**
    *   Come menzionato, è un processo che elimina il codice "morto" (non referenziato) dagli artefatti della build.
    *   Funziona bene con i moduli ES2015 (import/export statici). Angular e TypeScript sono progettati per facilitare il tree shaking.
    *   Assicurati che le tue importazioni siano specifiche e che le librerie di terze parti che usi siano tree-shakable.

*   **Differential Loading (Caricamento Differenziale - meno rilevante con la fine del supporto a IE11):**
    *   Storicamente, Angular CLI creava due set di bundle: uno per i browser moderni (ES2015+) e uno per i browser legacy (ES5) che non supportavano le feature più recenti. Il browser scaricava il set appropriato.
    *   Con la fine del supporto per Internet Explorer 11 e l'adozione diffusa di browser moderni, il target di compilazione predefinito di Angular è ora ES2020 o più recente, rendendo il differential loading per ES5 meno necessario o non più attivo per default. Controlla la configurazione del tuo `browserslist` nel progetto.

*   **Ottimizzazione del CSS:**
    *   Minificazione del CSS.
    *   Rimozione di commenti e ridondanze.
    *   Inline di CSS critico (a volte, per migliorare il First Contentful Paint).
    *   Rimozione di stili non usati (se configurato con strumenti come PurgeCSS, anche se Angular stesso fa un buon lavoro con gli stili incapsulati dei componenti).

**4. Bundle-Budgets (Budget dei Bundle)**

Angular CLI permette di definire dei "budget" per la dimensione dei tuoi bundle. Se un bundle supera il limite di warning o di errore specificato, la build ti avviserà o fallirà. Questo aiuta a tenere sotto controllo la dimensione dell'applicazione nel tempo.

*   **Configurazione in `angular.json`:**
    All'interno della configurazione del tuo progetto (es. `projects.<nome-progetto>.architect.build.configurations.production`), puoi definire la sezione `budgets`:
    ```json
    // angular.json (estratto)
    // ...
    //   "production": {
    //     "budgets": [
    //       {
    //         "type": "initial", // Budget per il caricamento iniziale
    //         "maximumWarning": "500kb",
    //         "maximumError": "1mb"
    //       },
    //       {
    //         "type": "anyComponentStyle", // Budget per gli stili di qualsiasi componente
    //         "maximumWarning": "2kb",
    //         "maximumError": "4kb"
    //       },
    //       {
    //         "type": "all", // Budget per la dimensione totale dell'applicazione
    //         "maximumWarning": "2mb",
    //         "maximumError": "4mb"
    //       }
    //       // Altri tipi di budget: 'bundle', 'anyScript', 'any', ecc.
    //     ],
    // ...
    ```
    È una buona pratica impostare dei budget ragionevoli per il tuo progetto e monitorarli.

**Analizzare il Bundle di Produzione:**

Per capire cosa contribuisce alla dimensione del tuo bundle, puoi usare strumenti come:

*   **`webpack-bundle-analyzer`**: Una utility che genera una mappa visuale interattiva del contenuto dei tuoi bundle Webpack (Webpack è il bundler usato da Angular CLI dietro le quinte).
    1.  Esegui la build con il flag `--stats-json`:
        ```bash
        ng build --configuration production --stats-json
        ```
    2.  Installa `webpack-bundle-analyzer`:
        ```bash
        npm install -g webpack-bundle-analyzer
        # o npx webpack-bundle-analyzer
        ```
    3.  Analizza il file `stats.json` generato nella cartella `dist/`:
        ```bash
        webpack-bundle-analyzer dist/<nome-progetto>/browser/stats.json
        ```
        Questo aprirà una visualizzazione nel browser.

*   **Source Map Explorer**: Un altro strumento che analizza i source map generati per capire quali file originali contribuiscono a ciascun bundle.
    ```bash
    ng build --configuration production --source-map
    npx source-map-explorer dist/<nome-progetto>/browser/*.js
    ```

Questi strumenti ti aiutano a identificare moduli o dipendenze grandi che potresti voler ottimizzare, caricare pigramente, o sostituire.

---

Creare una build di produzione ottimizzata è un passo cruciale prima del deployment. Angular CLI fa molto del lavoro pesante per te, ma capire cosa succede dietro le quinte e come usare strumenti di analisi può aiutarti a ottenere le migliori performance possibili.

Nella prossima lezione, "Deployment", discuteremo le strategie per mettere la tua applicazione Angular online.
