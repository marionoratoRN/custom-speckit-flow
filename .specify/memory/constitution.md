<!-- Sync Impact Report
Version change: (nuovo) → 1.1.0
Added: sezioni "Regole generali di sviluppo", "Flusso di lavoro" e "Governo", precompilate.
Origine: constitution di GoalMate 1.4.0 e di Voxli Platform 2.0.0 (callcenterai), tenendo solo le
regole non legate a quei prodotti. Escluse: Mobile-First, Community-Centric, Italian Localization,
Operatore-First, multi-surface UX, isolamento multi-vertical, e tutti i vincoli di stack.
Note: la sezione "Principi di progetto" resta da riempire con /speckit.constitution.
-->

# Project Constitution

> Questo file ha due parti. **Regole generali di sviluppo**, **Flusso di lavoro** e **Governo**
> sono il patrimonio comune a tutti i progetti: si conservano quando si rigenera il file con
> `/speckit.constitution`. **Principi di progetto** è la parte da riempire, ed è quella che cambia
> da un progetto all'altro.
>
> Ogni spec, plan e task si allinea a entrambe le parti.

---

## Principi di progetto

<!-- Da riempire con /speckit.constitution.
     Qui vanno i principi che dipendono dal dominio: cosa viene prima, cosa non si fa mai,
     quali vincoli legali o di prodotto governano le scelte. Numerarli in romano (I, II, III...)
     e scrivere per ognuno una regola verificabile, non un'intenzione. -->

---

## Regole generali di sviluppo

Valgono per ogni progetto e non dipendono dal dominio.

### Test prima del codice

Si sviluppa in TDD: il test si scrive prima del codice, nell'ordine rosso, verde, refactor. Tutto
il codice è coperto da unit test per almeno l'80 per cento. Gli integration test sono obbligatori
per gli endpoint delle API, i flussi di pagamento e il trattamento di dati sensibili.

La logica pura ha test deterministici. L'I/O (database, rete, SDK) si mocka.

Per i refactoring a comportamento invariato si scrivono characterization test prima di spostare o
spezzare il codice, e si verificano verdi prima e dopo (golden master). Nessuno split o
estrazione senza rete di test verde.

### I test girano su runner propri

La CI gira su **runner self-hosted registrati sul VPS**, non sui runner ospitati dal fornitore di
CI. I job si assegnano con l'etichetta del runner (convenzione in uso: `[self-hosted, ci]`). Il
motivo è avere un ambiente stabile, con le dipendenze di sistema e i servizi già presenti, e non
dipendere dai tempi e dai limiti dei runner condivisi.

### Dimensione dei file

Nessun file di codice supera le **500 righe**, con obiettivo fra 300 e 500. Un file che cresce
oltre la soglia si spezza in moduli coesi per responsabilità, con un barrel di re-export dove
serve a preservare l'API pubblica. Il limite vale anche per i moduli estratti. Il codice legacy si
riconduce sotto soglia quando lo si tocca, senza conversioni in blocco.

### Semplicità e iterazione

Sviluppo incrementale. Non si costruisce una cosa finche non serve davvero: le funzionalità
pensate per una fase successiva non si anticipano, e non si aggiunge un'astrazione finche non ci
sono almeno due casi concreti che la richiedono. Un requisito dichiarato vale come caso concreto,
una previsione no.

Ogni pull request risolve un problema concreto. Si preferisce una soluzione semplice e testata a
un'architettura elaborata.

### Perimetro ristretto ma completo

Meglio un perimetro piu stretto e completo che uno ampio con buchi operativi. Quando il sistema
sostituisce un processo esistente, copre il cento per cento di ciò che serve a quel processo per
funzionare davvero. Le esclusioni si approvano, non si scoprono in produzione.

### Niente placeholder

Mai funzionalità placeholder, integrazioni fittizie, opzioni non implementate o funzionalità
parziali presentate come complete. Ogni elemento visibile nell'interfaccia funziona. Se una cosa
non è pronta non compare nel menu.

### Nuove dipendenze

Non si introducono framework, ORM, librerie di interfaccia o dipendenze senza una necessità
concreta non risolvibile con quello che c'è già. Ogni nuova dipendenza si motiva nel piano e si fa
approvare. In assenza di necessità reale la risposta è riusare.

### Integrazioni esterne dietro adapter

Ogni integrazione con un servizio esterno vive in un adapter dedicato. La logica di business
dipende da un'interfaccia astratta, non dal fornitore concreto. Nessun dettaglio specifico del
fornitore (endpoint, formati, stranezze del protocollo) esce dal suo adapter. Così il fornitore
resta sostituibile e il raggio d'impatto di un suo cambiamento resta confinato.

### La logica di business è del backend

Dove esiste un backend, è lui l'owner della logica e delle integrazioni. I client consumano le sue
API e non reimplementano la stessa logica. Tutto ciò che tocca segreti, token o credenziali vive
nel backend e mai nel frontend. La specifica OpenAPI si aggiorna a ogni modifica di un endpoint,
generata automaticamente dove possibile.

Il principio riguarda la proprietà della logica, non la topologia del deploy: il backend può essere
diviso in moduli o processi, purche la logica resti unica.

### Una sola fonte di verità

Niente sistemi paralleli e niente copie divergenti degli stessi dati. Ciò che un componente scrive
è immediatamente visibile agli altri, perche leggono la stessa fonte.

### Collezioni di richieste versionate

Ogni funzionalità che aggiunge o modifica endpoint fornisce le collezioni di richieste per la
verifica manuale ed esplorativa. Convenzione in uso: Bruno, file `.bru` versionati sotto la
cartella dell'API, con le variabili d'ambiente in una cartella separata. Sono strumenti di
sviluppo e non vengono deployati.

### Branching

- `main` è produzione. Ogni merge su `main` è una release.
- `develop` è il ramo di integrazione, da cui si deploya l'ambiente di sviluppo.
- `feature/<nome>` parte da `develop` e viene mergiato in `develop`.
- `release/<x.y.z>` è opzionale, per stabilizzare prima di andare su `main`.
- `hotfix/<nome>` parte da `main`, e va mergiato sia su `main` sia su `develop`.

Regole:

- Mai committare direttamente su `main` o su `develop`.
- Subito dopo il merge su `develop`, la stessa feature si porta su `main` con un ramo isolato che
  parte da `main` e prende i commit di quella sola feature (cherry-pick), con pull request verso
  `main`. Si propone di propria iniziativa: il merge su `develop` è metà del lavoro, non la fine.
- Mai portare `develop` dentro `main` in blocco. Un travaso di decine di commit non correlati non
  è una release, e porta su anche i lavori tenuti fermi di proposito. Si rilascia una feature per
  volta.
- I merge su `main` sono `--no-ff`, con tag di versione.

### Un worktree per lavoro

Ogni sessione che modifica codice avviene in un git worktree dedicato, mai nel repository
principale. Una feature o un refactor uguale un worktree, creato a partire da `develop`. Il
repository principale non si usa come area di lavoro mentre ci sono worktree attivi. Il merge
avviene via pull request, e il worktree si rimuove a lavoro finito.

### Revisione del codice

Ogni pull request richiede almeno una revisione prima del merge.

### Pipeline di CI

Su ogni pull request: lint, controllo dei tipi, unit test, integration test.

### Migrazioni del database

Le migrazioni sono forward only: additive, retro compatibili e idempotenti, applicate una sola
volta in modo deterministico con lo stesso meccanismo in CI e al deploy. **Si applicano prima del
codice che le usa.** Il rollback è applicativo (ripristino del commit precedente e riavvio), non
sullo schema. I cambi distruttivi come drop e rename si eseguono in passi retro compatibili
secondo il pattern expand e contract, mai nello stesso deploy del codice che li richiede.

### Operazioni pericolose e irreversibili

Le operazioni pericolose chiedono conferma o avvisano, ma non lasciano mai l'operatore bloccato in
modo irrecuperabile. Le operazioni irreversibili sono idempotenti o deduplicate, così una doppia
esecuzione non produce un doppio effetto. Le azioni verso ambienti condivisi (deploy, migrazioni,
azioni distruttive) si espongono e si confermano prima di eseguirle.

### Deploy

Il deploy avviene solo via git: commit, push, pull sul server, rebuild. Mai scp, rsync o copia
diretta di file sul server. Gli ambienti di sviluppo e di produzione restano separati.

### Segreti

Nessun segreto nel codice. In locale variabili d'ambiente in un file non versionato, in produzione
un gestore di segreti. Un controllo automatico fallisce la build se trova un segreto nel
repository. Segreti, token e credenziali sono cifrati a riposo e non compaiono mai in chiaro in
interfacce, risposte delle API o log.

### Controllo degli accessi e traccia di audit

Le operazioni sensibili sono protette dal controllo del ruolo, non dalla sola autenticazione.
Sensibili sono quelle distruttive e quelle che toccano permessi, denaro, configurazioni o dati
personali. Ogni esecuzione lascia una riga in un registro append only: chi, cosa, quando, su quale
oggetto, con che esito. Il registro non si modifica e non si cancella.

### L'identità viene dalla sessione

L'identità di chi fa una richiesta, e il perimetro di dati a cui ha diritto, si ricavano sempre
dalla sessione o dal token, mai da un campo del corpo della richiesta o della query. Il client dice
su quale oggetto vuole agire, il server verifica che quell'oggetto stia dentro il perimetro di chi
chiama. Serve a impedire che cambiando un identificatore nella richiesta si arrivi ai dati di un
altro.

### Attivazione delle funzionalità

Le funzionalità sono attivabili singolarmente e restano disattivate per impostazione predefinita.
Nessuna interfaccia compare per una funzionalità non attiva.

### Dati personali

Se il progetto tratta dati personali: finalità dichiarata, crittografia a riposo e in transito per
i dati sensibili, hosting nell'Unione Europea, consenso esplicito, diritto all'oblio previsto
dalla prima versione, nessuna condivisione con terze parti senza consenso. Se tratta dati
particolari ai sensi dell'articolo 9 del GDPR (salute, biometrici, e simili), questo diventa un
principio di progetto con requisiti espliciti in ogni spec che li tocca.

### Lingua

Codice e documentazione tecnica sono in **inglese**: identificatori, commenti, nomi di file, di
funzioni e di tipi, messaggi di log tecnici, artefatti spec-kit (`specs/**`, contracts, checklist)
e README. Niente italiano nel codice e nessuna parola inglese italianizzata.

Sono esenti dal vincolo, e seguono la lingua del team: la comunicazione (chat, descrizioni
discorsive delle pull request) e i documenti di governo (`constitution.md`,
`product-foundation.md`, `spec-backlog.md`, `CLAUDE.md`). Sono esenti anche le stringhe rivolte
all'utente finale, che comunque non si scrivono a mano nel codice ma passano dal sistema di
localizzazione.

Il codice e i documenti tecnici legacy in italiano si convertono quando vengono toccati, come per
il limite di righe: nessuna conversione in blocco, ma niente italiano nuovo.

### Registro piano

Si scrive in modo piano e fattuale, nei documenti e nelle risposte. I vincoli e le dipendenze si
dichiarano come fatti.

I blocchi esistono e vanno detti quando ci sono. Quello che non serve è il registro solenne
costruito per creare urgenza: "blocca ogni progresso", "non negoziabile", "azione immediata",
"determina il successo o il fallimento", il grassetto usato per alzare la voce. Se tutto suona
critico non si distingue piu cosa lo è davvero. Una cosa seria si dice una volta, in modo asciutto,
e poi si passa oltre.

---

## Flusso di lavoro

1. Le funzionalità di dimensione media o grande si specificano con spec-kit prima
   dell'implementazione, seguendo il flusso `specify → clarify → plan → tasks → analyze →
   implement`. I fix puntuali e le modifiche minori procedono direttamente. Dentro il flusso,
   `implement` è l'unico passaggio che crea o modifica codice di produzione.
2. Le decisioni di design significative (modello dati, scelta di un adapter esterno, deviazioni
   dai principi) si documentano nel piano e si verificano contro questa constitution
   (Constitution Check).
3. **Audit di qualità prima di chiudere il ciclo.** Al termine di ogni ciclo spec-kit, prima di
   dichiararlo chiuso, si esegue un audit sul codice prodotto. Controlla almeno:
   - dimensione dei file sotto soglia;
   - copertura dei test almeno all'80 per cento, con la suite verde;
   - assenza di placeholder e di funzionalità parziali presentate come complete;
   - duplicazione di logica, e logica finita nel client invece che nel backend;
   - dettagli di fornitori esterni usciti dal loro adapter;
   - segreti, lingua del codice, aderenza ai principi della constitution.

   I problemi trovati si rifattorizzano prima della chiusura. Il ciclo non si dichiara chiuso
   finche restano aperti. Se un problema non è risolvibile nel ciclo, l'eccezione va motivata e
   approvata, e diventa un task registrato con la sua scadenza.
4. Le modifiche allo schema del database si applicano prima del codice che le usa.
5. Il deploy sull'ambiente di sviluppo avviene dopo ogni funzionalità completata, per la
   validazione, prima della promozione in produzione.

## Governo

- La constitution ha precedenza sulle altre pratiche di sviluppo.
- Le modifiche richiedono motivazione documentata, approvazione, e un piano di migrazione se
  rompono qualcosa di esistente.
- Ogni pull request e ogni revisione verificano la conformità ai principi. Le violazioni si
  giustificano nel piano e si fanno approvare.
- La complessità aggiunta si motiva con un caso d'uso concreto.
- Le divergenze fra i documenti si segnalano, non si assorbono in silenzio.
- Versionamento: patch per un chiarimento, minor per un principio o una sezione aggiunti, major
  per un principio rimosso o ridefinito in modo incompatibile. In testa al file si tiene un Sync
  Impact Report con il cambio di versione e cosa è cambiato.

**Versione**: 1.1.0 | **Ratificata**: [DATA] | **Ultima modifica**: [DATA]
