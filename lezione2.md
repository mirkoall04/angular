
### Modulo 1: Fondamenti di Angular e Configurazione dell'Ambiente

**Lezione 2: Configurazione dell'Ambiente di Sviluppo**

In questa lezione, vedremo passo dopo passo come preparare il tuo computer per iniziare a sviluppare applicazioni con Angular.

**1. Installazione di Node.js e npm/yarn**

*   **Cos'è Node.js?**
    *   Node.js è un **runtime JavaScript open-source** che permette di eseguire codice JavaScript al di fuori di un browser web. È costruito sul motore JavaScript V8 di Chrome.
    *   Per lo sviluppo Angular, Node.js è fondamentale perché fornisce l'ambiente in cui vengono eseguiti molti strumenti di build, il server di sviluppo locale e Angular CLI stessa.

*   **Cos'è npm (Node Package Manager)?**
    *   **npm** è il gestore di pacchetti predefinito per Node.js. Viene installato automaticamente insieme a Node.js.
    *   Serve per installare, gestire e condividere pacchetti (librerie e strumenti) di codice JavaScript. Angular stesso e le sue dipendenze sono distribuiti come pacchetti npm.
    *   Userai npm (o un'alternativa come yarn) per installare Angular CLI, le librerie necessarie per il tuo progetto e per eseguire script definiti nel file `package.json` del tuo progetto (come avviare il server di sviluppo o eseguire test).

*   **Yarn (Alternativa a npm):**
    *   Yarn è un altro popolare gestore di pacchetti per JavaScript, sviluppato da Facebook. Offre funzionalità simili a npm, a volte con performance migliori o una gestione delle dipendenze leggermente diversa.
    *   Puoi scegliere di usare npm o yarn. Per semplicità, in questa guida faremo principalmente riferimento a npm, ma i comandi di yarn sono spesso molto simili.

*   **Come installare Node.js e npm:**
    1.  **Vai al sito ufficiale di Node.js:** [https://nodejs.org/](https://nodejs.org/)
    2.  **Scarica la versione LTS (Long Term Support):** La versione LTS è raccomandata per la maggior parte degli utenti perché offre maggiore stabilità e supporto a lungo termine.
    3.  **Esegui l'installer:** Segui le istruzioni dell'installer per il tuo sistema operativo (Windows, macOS, Linux). L'installazione includerà sia Node.js che npm.
    4.  **Verifica l'installazione:** Apri il tuo terminale o prompt dei comandi e digita i seguenti comandi:
        ```bash
        node -v
        npm -v
        ```
        Dovresti vedere le versioni di Node.js e npm installate, ad esempio:
        ```
        v20.11.1  # (o una versione LTS più recente)
        10.2.4   # (o una versione npm più recente)
        ```
        Se vedi le versioni, l'installazione è andata a buon fine.

**2. Installazione di Angular CLI**

*   **Cos'è Angular CLI (Command Line Interface)?**
    *   Angular CLI è uno strumento a riga di comando che ti aiuta a inizializzare, sviluppare, manutenere e testare le tue applicazioni Angular. Semplifica enormemente il workflow di sviluppo.
    *   Con Angular CLI puoi:
        *   Creare nuovi progetti.
        *   Generare componenti, servizi, moduli, direttive, ecc.
        *   Avviare un server di sviluppo locale con ricaricamento automatico.
        *   Eseguire test unitari e end-to-end.
        *   Compilare l'applicazione per la produzione.
        *   Aggiornare le versioni di Angular e delle sue dipendenze.

*   **Comando per installare Angular CLI:**
    *   Apri il terminale o prompt dei comandi.
    *   Esegui il seguente comando per installare Angular CLI globalmente sul tuo sistema (l'opzione `-g` sta per global):
        ```bash
        npm install -g @angular/cli
        ```
        Potrebbe essere necessario avere i permessi di amministratore (es. usare `sudo` su macOS/Linux) per installare pacchetti globalmente.

*   **Verifica l'installazione di Angular CLI:**
    *   Dopo l'installazione, verifica che sia andato tutto a buon fine digitando:
        ```bash
        ng version
        ```        Dovresti vedere un output simile a questo (le versioni potrebbero variare):
        ```
             _                      _                 ____ _     ___
            / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
           / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
          / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
         /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                        |___/

        Angular CLI: 18.x.y  # (o la versione più recente che hai installato)
        Node: 20.11.1        # (o la tua versione di Node)
        Package Manager: npm 10.2.4 # (o la tua versione di npm/yarn)
        OS: win32 x64        # (o il tuo sistema operativo)

        Angular: ...
        ... animations, common, compiler, compiler-cli, core, forms
        ... platform-browser, platform-browser-dynamic, router

        Package                         Version
        ---------------------------------------------------------
        @angular-devkit/architect       ...
        @angular-devkit/build-angular   ...
        @angular-devkit/core            ...
        @angular-devkit/schematics      ...
        @angular/cli                    18.x.y
        @schematics/angular             ...
        rxjs                            ...
        typescript                      ...
        ```
        Se vedi l'output di Angular CLI, sei pronto per creare la tua prima applicazione!

**3. Creazione di un nuovo progetto Angular con Angular CLI**

*   **Comando per creare un nuovo progetto:**
    1.  Apri il terminale o prompt dei comandi.
    2.  **Naviga nella cartella** in cui vuoi creare il tuo progetto (ad esempio, `Documenti/Progetti` o `Development`).
        ```bash
        cd Percorso/Della/Tua/Cartella
        ```
    3.  Esegui il comando `ng new` seguito dal nome che vuoi dare al tuo progetto. Per esempio, se vuoi chiamare il progetto `my-first-angular-app`:
        ```bash
        ng new my-first-angular-app
        ```

*   **Opzioni durante la creazione:**
    *   Durante il processo di creazione, Angular CLI potrebbe farti alcune domande:
        *   **"Would you like to add Angular routing?" (y/N):**
            *   Ti chiede se vuoi includere il modulo di routing di Angular, che permette di navigare tra diverse viste/pagine nella tua SPA. È **altamente raccomandato rispondere 'y' (yes)** per la maggior parte delle applicazioni.
        *   **"Which stylesheet format would you like to use?" (Use arrow keys):**
            *   Ti permette di scegliere il formato per i tuoi fogli di stile. Le opzioni comuni sono:
                *   **CSS:** Standard CSS.
                *   **SCSS (Sass):** Un preprocessore CSS popolare che aggiunge funzionalità come variabili, nesting, mixin, ecc. (richiede `node-sass` o `sass` come dipendenza).
                *   **Sass:** Simile a SCSS ma con una sintassi diversa (indentation-based).
                *   **Less:** Un altro preprocessore CSS.
            *   Scegli quello con cui ti trovi più a tuo agio. **CSS** è una buona scelta per iniziare se non conosci i preprocessori. **SCSS** è molto popolare negli ambienti professionali.

    *   Dopo aver risposto a queste domande, Angular CLI scaricherà tutte le dipendenze necessarie e creerà la struttura base del progetto. Questo processo potrebbe richiedere qualche minuto.

*   **Navigare nella cartella del progetto:**
    *   Una volta completata la creazione, naviga nella cartella del progetto appena creato:
        ```bash
        cd my-first-angular-app
        ```

**4. Struttura di un progetto Angular**

Angular CLI genera una struttura di cartelle e file ben definita. Ecco una panoramica dei file e delle cartelle più importanti che troverai all'interno di `my-first-angular-app`:

*   `README.md`: Un file Markdown con informazioni di base sul progetto e comandi utili.
*   `angular.json`: Il file di configurazione principale per Angular CLI. Definisce le opzioni di build, serve, test, linting per il workspace e per i progetti al suo interno. Lo modificherai raramente all'inizio.
*   `package.json`: File standard di npm che elenca le dipendenze del progetto (librerie come Angular stesso, RxJS, ecc.) e gli script personalizzati (es. `npm start` che esegue `ng serve`).
*   `package-lock.json` (o `yarn.lock`): Registra le versioni esatte di ogni dipendenza installata, garantendo build consistenti tra diversi ambienti.
*   `tsconfig.json`: File di configurazione per il compilatore TypeScript. Specifica le opzioni del compilatore e i file da includere nella compilazione.
*   `tsconfig.app.json`: Configurazione TypeScript specifica per l'applicazione.
*   `tsconfig.spec.json`: Configurazione TypeScript specifica per i test.
*   `.editorconfig`: File per definire e mantenere stili di codifica consistenti tra diversi editor e IDE.
*   `.gitignore`: Specifica i file e le cartelle che Git (sistema di controllo versione) dovrebbe ignorare.
*   `node_modules/`: Questa cartella contiene tutte le dipendenze (pacchetti npm) scaricate per il progetto. Non dovresti mai modificare direttamente i file in questa cartella e di solito viene ignorata dal controllo versione.
*   **`src/`**: Questa è la **cartella più importante** perché contiene il **codice sorgente della tua applicazione**.
    *   `main.ts`: Il **punto di ingresso principale** dell'applicazione. Questo file inizializza la piattaforma Angular e avvia (bootstrap) il modulo radice dell'applicazione (o il componente radice standalone).
    *   `index.html`: L'**unica pagina HTML** che viene servita al browser. Angular inietterà dinamicamente il contenuto dell'applicazione all'interno di questa pagina (tipicamente dentro un tag come `<app-root></app-root>`).
    *   `styles.css` (o `.scss`, `.less`, `.styl` a seconda della scelta fatta durante `ng new`): Qui puoi definire gli stili globali per la tua applicazione.
    *   `assets/`: Cartella per memorizzare risorse statiche come immagini, font, file JSON, ecc., che verranno copiate così come sono durante il processo di build.
    *   `environments/`: Contiene file di configurazione specifici per i diversi ambienti (es. sviluppo, produzione).
        *   `environment.ts`: Per l'ambiente di sviluppo (default).
        *   `environment.prod.ts`: Per l'ambiente di produzione.
    *   **`app/`**: La cartella principale per i componenti, i servizi e gli altri file specifici della tua applicazione.
        *   `app.component.ts`: La classe TypeScript del componente radice (il componente principale dell'applicazione).
        *   `app.component.html`: Il template HTML del componente radice.
        *   `app.component.css` (o `.scss`, etc.): Gli stili specifici per il componente radice.
        *   `app.component.spec.ts`: Il file per gli unit test del componente radice.
        *   *Se hai scelto il routing:*
            *   `app.routes.ts` (o simile, potrebbe essere integrato in `app.config.ts` per standalone): Definisce le rotte dell'applicazione.
            *   `app.config.ts` (per progetti standalone, che è il default da Angular 17+): File di configurazione dell'applicazione che registra provider, routing, ecc. Sostituisce `app.module.ts` per il bootstrap dell'applicazione in un contesto standalone.
        *   *Per progetti più vecchi o che non usano standalone components per il root:*
            *   `app.module.ts`: Il modulo radice (`AppModule`) dell'applicazione. Tradizionalmente, qui si dichiaravano i componenti, si importavano altri moduli e si registravano i provider di servizi.

**5. Avvio del server di sviluppo e visualizzazione dell'applicazione**

*   **Comando per avviare il server:**
    *   Assicurati di essere nella cartella principale del tuo progetto Angular nel terminale (`cd my-first-angular-app`).
    *   Esegui il comando:
        ```bash
        ng serve
        ```
    *   Per aprire automaticamente l'applicazione nel tuo browser predefinito, puoi usare l'opzione `-o` (o `--open`):
        ```bash
        ng serve -o
        ```

*   **Cosa succede:**
    *   Angular CLI compila l'applicazione (in memoria) e avvia un server di sviluppo web locale.
    *   Questo server include il **live-reloading**: ogni volta che modifichi e salvi un file del codice sorgente, il browser si aggiornerà automaticamente per mostrare le modifiche.

*   **Visualizzazione dell'applicazione:**
    *   Se non hai usato l'opzione `-o`, apri il tuo browser web e naviga all'indirizzo:
        **`http://localhost:4200/`**
        (La porta `4200` è quella predefinita, ma potrebbe cambiare se è già in uso. Controlla l'output del terminale dopo `ng serve` per l'indirizzo corretto).
    *   Dovresti vedere la pagina di benvenuto predefinita dell'applicazione Angular appena creata. Mostrerà il logo di Angular e alcuni link utili.

---

Congratulazioni! Hai configurato con successo il tuo ambiente di sviluppo, creato la tua prima applicazione Angular e l'hai avviata nel browser.

