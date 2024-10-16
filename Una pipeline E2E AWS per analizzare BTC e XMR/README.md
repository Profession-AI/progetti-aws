# Una pipeline E2E AWS per analizzare BTC e XMR

CryptoData Insights è una società innovativa nel settore della data analytics specializzata nell'analisi delle criptovalute. L'azienda offre soluzioni avanzate per raccogliere, pulire e trasformare dati di mercato sulle principali criptovalute come Bitcoin (BTC) e Monero (XMR). Utilizzando tecnologie cloud avanzate, CryptoData Insights abilita analisi approfondite e visualizzazioni di trend, supportando decisioni strategiche basate su dati concreti. 

## Sfida Aziendale
Con l'aumento delle criptovalute e la volatilità del mercato, diventa essenziale per investitori, trader e aziende disporre di insight accurati e in tempo reale sui trend di mercato. Il valore dei dati risiede nella loro accuratezza e tempestività, ma gestire grandi volumi di dati non strutturati può essere complesso, specialmente quando si richiedono trasformazioni continue e analisi avanzate. CryptoData Insights ha identificato un'opportunità per semplificare questo processo, offrendo una pipeline end-to-end (E2E) automatizzata per l'analisi delle criptovalute, sfruttando le capacità della piattaforma AWS.

## Soluzione Proposta
CryptoData Insights ha sviluppato una pipeline di analisi E2E per BTC e XMR utilizzando i servizi di AWS. Questa soluzione offre un processo automatizzato che trasforma dati grezzi di mercato e di Google Trends in tabelle pronte per analisi approfondite su Amazon Redshift. 

### Pipeline E2E AWS
- **Caricamento su S3**: Gli utenti caricano file grezzi sui prezzi delle criptovalute e sui Google Trends in un bucket S3.
- **Pulizia e Trasformazione dei Dati**: I dati vengono puliti e trasformati in formato Parquet, con opzioni per gestire valori mancanti e applicare algoritmi di smoothing (come medie mobili).
- **Ottimizzazione e Caricamento su Redshift**: Le tabelle finali ottimizzate vengono caricate su Amazon Redshift, dove possono essere analizzate con query SQL per ottenere insight dettagliati.
- **Visualizzazione (Opzionale)**: Gli utenti possono creare dashboard interattive con Amazon QuickSight per esplorare i dati trasformati e identificare pattern e tendenze.

### I file
Come detto abbiamo una coppia di files per ogni crypto valuta. Le due cryptovalute e le relative pipeline possono essere considerate indipendenti, e possono dunque procedere in parallelo generando una tabella finale ciascuno. Se lo studente, lo desidera, può anche, a valle, joinare le due tabelle per ulteriori analisi, ma questo non è parte di questo progetto.
Prendendo Bitcoin come esempio abbiamo la seguente coppia di files.

**File di prezzo (BTC/EUR)**
Questo file CSV, ha diverse colonne ma quelle interessanti, per questo progetto, sono le prime due:
‘Date’, una stringa di formato (esempio) “03/12/2024” 
‘Price’ un numero (appunto il prezzo), esempio: 145.4

Occorre notare che alcuni valori del prezzo sono mancanti (hanno valore -1), quindi lo studente deve cercare di eseguire alcune azioni su di essi, come, per esempio:
Eliminare totalmente la riga
Fillare il valore con il precedente
Fillare il valore con la media/mediana dei 5 precedenti

Ovviamente, le azioni di cui sopra, vanno eseguite durante la pipeline.

**File di Google trend**
Questo file CSV, ha due sole colonne:
Settimana: una colonna che raffigura l’indice temporale per il valore interesse.
Interesse bitcoin: un valore intero, che va fra 0 e 100, dove 100 significa massimo interesse, in accordo alle ricerche Google degli utenti di tutto il mondo.

Il file di Google trend, ha una granularità minore di quello di prezzo, il quale è giornaliero. In fase di Join, dunque alcuni valori del prezzo saranno persi. Si consiglia quindi di smussare il prezzo stesso mediante una media mobile.

### Struttura della pipeline
Il nostro progetto si articola in due pipeline distinte, dedicate rispettivamente a Bitcoin e Monero. Queste pipeline sono progettate per essere eseguite in parallelo, massimizzando l'efficienza del processo.

**Preparazione dei Dati**
I dati di partenza sono situati su Amazon S3, all'interno di un bucket denominato "raw". Il primo step di ciascuna pipeline consiste nella pulizia dei file relativi ai Google trend e ai prezzi delle due criptovalute. Una volta puliti, i dati sono salvati in formato Parquet su un nuovo bucket S3, identificato come "argento", preparandoli per l'analisi successiva.

**Analisi e Unificazione dei Dati**
La seconda fase del processo prevede la lettura degli artefatti puliti per calcolare la media mobile a 10 giorni dei prezzi, con lo scopo di minimizzare il rumore. Successivamente, avviene il join tra i dataset di prezzo e Google trend, risultando in un unico file strutturato come segue: data, prezzo, indice_google_trend.

**Caricamento su Redshift e Visualizzazione**
Infine, un'ulteriore pipeline trasferisce il file unificato su Amazon Redshift, rendendo i dati pronti per l'analisi approfondita. Come passaggio opzionale, gli utenti possono sfruttare Amazon QuickSight per visualizzare e interpretare i risultati ottenuti, facilitando l'esplorazione dei dati e la condivisione degli insight.


Per quanto riguarda le trasformazioni dei dati nelle pipeline di questo progetto, hai la possibilità di scegliere tra due servizi AWS: GLUE ETL e EMR. Ogni servizio offre specifici vantaggi e si adatta a diverse esigenze di elaborazione dei dati.
Una volta selezionato il servizio per le trasformazioni, è essenziale prestare attenzione all'orchestrazione del processo. AWS Step Functions si rivela uno strumento cruciale in questa fase, consentendo di coordinare le attività e garantire che le due pipeline possano eseguire in parallelo senza intoppi. Utilizzando Step Functions, lo studente può definire facilmente il flusso di lavoro e monitorare lo stato di esecuzione delle pipeline, assicurandosi che ogni passo sia completato come previsto.

## Vantaggi per l'Azienda
La soluzione offre diversi vantaggi competitivi:
- **Automazione Completa**: L'intera pipeline, dall'acquisizione dei dati alla loro visualizzazione, è automatizzata, riducendo il carico di lavoro manuale e minimizzando il rischio di errori.
- **Scalabilità**: La pipeline è progettata per essere scalabile, consentendo l'elaborazione di grandi volumi di dati in parallelo, e può essere facilmente estesa per includere altre criptovalute.
- **Flessibilità nelle Trasformazioni**: Grazie alla possibilità di utilizzare AWS Glue ETL o EMR, gli utenti possono personalizzare il processo di pulizia e trasformazione in base alle loro esigenze specifiche.
- **Insight Approfonditi**: Con i dati finali disponibili su Redshift e la possibilità di visualizzarli tramite QuickSight, gli utenti possono ottenere insight immediati sui trend di mercato e prendere decisioni basate su dati.

## Valore Aggiunto
CryptoData Insights si posiziona come partner strategico per trader, analisti finanziari e investitori, fornendo uno strumento avanzato e automatizzato per la gestione e l'analisi dei dati delle criptovalute. Grazie alla pipeline AWS, l'azienda aiuta i clienti a trasformare rapidamente i dati grezzi in informazioni preziose, migliorando l'efficienza operativa e accelerando il processo decisionale basato su dati concreti. Questo approccio consente alle aziende di rimanere competitive in un mercato in continua evoluzione, riducendo al contempo i costi operativi e ottimizzando le risorse tecnologiche.



# Dataset

Il dataset è scaricabile da questo link: https://drive.google.com/drive/folders/1q-6IWRebAe51xhhY_dWI5QWfiBWEH8E2
