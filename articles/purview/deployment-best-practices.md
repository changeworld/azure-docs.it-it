---
title: Procedura consigliate per la distribuzione
description: Questo articolo fornisce le procedure consigliate per la distribuzione di Azure. Azure Purview consente a qualsiasi utente di registrare, individuare, comprendere e utilizzare le origini dati.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e02ad9187743603d46259d70965e49d6839ecd71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104949839"
---
# <a name="azure-purview-deployment-best-practices"></a>Procedure consigliate per la distribuzione di Azure

Questo articolo identifica le attività comuni che consentono di distribuire la competenza nell'ambiente di produzione. Queste attività possono essere completate in fasi, nel corso di un mese o più. Anche le organizzazioni che hanno già distribuito competenze possono usare questa guida per assicurarsi che stiano ottenendo il massimo dagli investimenti.

Una distribuzione ben pianificata di una piattaforma di governance dei dati, ad esempio Azure, può offrire i vantaggi seguenti:

- Migliore individuazione dati
- Collaborazione analitica migliorata
- Redditività ingrandita sull'investimento.

## <a name="prerequisites"></a>Prerequisiti

* Accesso a Microsoft Azure con una sottoscrizione di sviluppo o di produzione
* Possibilità di creare risorse di Azure, incluse le competenze
* Accesso alle origini dati, ad esempio Azure Data Lake Storage o Azure SQL negli ambienti di test, sviluppo o produzione
  * Per Data Lake Storage, il ruolo richiesto per l'analisi è ruolo Lettore
  * Per SQL, l'identità deve essere in grado di eseguire query sulle tabelle per il campionamento di classificazioni
* Accesso al centro sicurezza di Azure o possibilità di collaborare con l'amministratore del Centro sicurezza per l'assegnazione di etichette ai dati

## <a name="identify-objectives-and-goals"></a>Identificare obiettivi e obiettivi

Molte organizzazioni hanno iniziato il percorso di governance dei dati sviluppando soluzioni singole che soddisfano requisiti specifici di gruppi isolati e domini di dati nell'intera organizzazione. Sebbene le esperienze possano variare a seconda del settore, del prodotto e delle impostazioni cultura, la maggior parte delle organizzazioni risulta difficile mantenere controlli coerenti e criteri per questi tipi di soluzioni.

Alcuni degli obiettivi comuni di governance dei dati che possono essere identificati nelle fasi iniziali includono:

* Ottimizzazione del valore aziendale dei dati
* Abilitazione di una cultura dei dati in cui i consumer di dati possono trovare, interpretare e considerare attendibili
* Aumento della collaborazione tra le diverse unità aziendali per offrire un'esperienza di dati coerente
* Promuovere l'innovazione accelerando l'analisi dei dati per sfruttare i vantaggi del cloud
* Riduzione del tempo per l'individuazione dei dati tramite opzioni self-service per diversi gruppi di competenze
* Riduzione del time-to-market per la distribuzione di soluzioni di analisi che migliorano il servizio ai clienti
* Riduzione dei rischi operativi dovuti all'utilizzo di strumenti specifici del dominio e tecnologia non supportata

L'approccio generale consiste nel suddividere gli obiettivi più inarcanti in diverse categorie e obiettivi. Di seguito sono riportati alcuni esempi:

|Category|Obiettivo|
|---------|---------|
|Individuazione|Gli utenti amministratori devono essere in grado di analizzare le origini dati Azure e non Azure (incluse le origini locali) per raccogliere automaticamente le informazioni sugli asset di dati.|
|Classificazione|La piattaforma deve classificare automaticamente i dati in base a un campionamento dei dati e consentire l'override manuale usando le classificazioni personalizzate.|
|Consumo|Gli utenti aziendali dovrebbero essere in grado di trovare informazioni su ogni asset per i metadati aziendali e tecnici.|
|Derivazione|Ogni asset deve mostrare una visualizzazione grafica dei set di dati sottostanti, in modo che gli utenti conoscano le origini originali e quali modifiche sono state apportate.|
|Collaborazione|La piattaforma deve consentire agli utenti di collaborare fornendo informazioni aggiuntive su ogni asset di dati.|
|Creazione di report|Gli utenti devono essere in grado di visualizzare i report relativi ai dati, inclusi i dati sensibili e i dati che richiedono un arricchimento aggiuntivo.|
|Governance dei dati|La piattaforma deve consentire all'amministratore di definire i criteri per il controllo di accesso e di applicare automaticamente l'accesso ai dati in base a ogni utente.|
|Flusso di lavoro|La piattaforma deve avere la possibilità di creare e modificare il flusso di lavoro in modo da semplificare la scalabilità orizzontale e automatizzare diverse attività all'interno della piattaforma.|
|Integrazione|Altre tecnologie di terze parti, come la creazione di ticket o l'orchestrazione, devono essere in grado di integrarsi nella piattaforma tramite script o API REST.|

## <a name="top-questions-to-ask"></a>Domande principali da porre

Quando l'organizzazione concorda sugli obiettivi e gli obiettivi di alto livello, ci saranno molte domande da più gruppi. È fondamentale riunire queste domande per creare un piano per risolvere tutti i problemi. Di seguito sono riportate alcune domande di esempio che è possibile eseguire durante la fase iniziale:

1. Quali sono le origini dati dell'organizzazione e i sistemi dati principali?
2. Per le origini dati che non sono ancora supportate da competenza, quali sono le opzioni disponibili?
3. Quante istanze di competenza sono necessarie?
4. Chi sono gli utenti?
5. Chi può analizzare nuove origini dati?
6. Utenti che possono modificare il contenuto all'interno di competenza
7. Quale processo è possibile utilizzare per migliorare la qualità dei dati in ambito?
8. Come eseguire il bootstrap della piattaforma con asset critici, termini di glossario e contatti esistenti?
9. Come eseguire l'integrazione con i sistemi esistenti
10. Come raccogliere commenti e suggerimenti e creare un processo sostenibile?

Anche se non si ha la possibilità di rispondere alla maggior parte di queste domande, può aiutare l'organizzazione a incorniciare questo progetto e garantire che tutti i requisiti di "necessità" siano soddisfatti.

## <a name="include-the-right-stakeholders"></a>Includi gli stakeholder appropriati

Per garantire la riuscita dell'implementazione di competenze per l'intera azienda, è importante coinvolgere gli stakeholder appropriati. Nella fase iniziale sono interessati solo pochi utenti. Tuttavia, quando si espande l'ambito, è necessario che altri utenti possano collaborare al progetto e fornire commenti e suggerimenti.

Di seguito sono riportate alcune parti principali che è opportuno includere:

|Utente tipo|Ruoli|
|---------|---------|
|**Responsabile dati**|Il CDO sovrintende a una gamma di funzioni che possono includere la gestione dei dati, la qualità dei dati, la gestione dei dati master, data science, business intelligence e la creazione di una strategia per i dati. Possono essere gli sponsor del progetto di implementazione di competenza.|
|**Proprietario dominio/aziendale**|Un utente aziendale che influisce sull'utilizzo degli strumenti e ha un controllo del budget|
|**Analista dati**|In grado di inquadrare un problema aziendale e analizzare i dati per consentire ai responsabili di prendere decisioni aziendali|
|**Data Architect**|Progettare database per app line-of-business cruciali insieme alla progettazione e all'implementazione della sicurezza dei dati|
|**Data engineer**|Gestisci e Gestisci lo stack di dati, Estrai i dati da origini diverse, integra e prepara i dati, configura le pipeline di dati|
|**Data scientist**|Compilare modelli analitici e configurare i prodotti di dati a cui accedere dalle API|
|**Amministratore di database**|Gestire, rilevare e risolvere gli eventi imprevisti e le richieste correlati al database entro i contratti di servizio (SLA); Può configurare pipeline di dati|
|**DevOps**|Sviluppo e implementazione di applicazioni line-of-business; può includere la scrittura di script e le funzionalità di orchestrazione|
|**Specialista per la sicurezza dei dati**|Valutazione della sicurezza complessiva della rete e dei dati, che implica i dati in entrata e in uscita.|

## <a name="identify-key-scenarios"></a>Identificare gli scenari principali

La competenza può essere usata per gestire centralmente la governance dei dati in un'area dati di un'organizzazione che si estende su ambienti cloud e locali. Per un'implementazione corretta, è necessario identificare gli scenari principali cruciali per l'azienda. Questi scenari possono superare i limiti delle business unit o avere un effetto su più utenti a Monte o downstream.

Questi scenari possono essere scritti in vari modi, ma è necessario includere almeno queste cinque dimensioni:

1. Persona: chi sono gli utenti?
2. Sistema di origine: quali sono le origini dati, ad esempio Azure Data Lake Storage Gen2 o il database SQL di Azure?
3. Area di interesse: qual è la categoria di questo scenario?
4. Scenari dettagliati: come gli utenti usano la competenza per risolvere i problemi?
5. Risultato previsto: quali sono i criteri di riuscita?

Gli scenari devono essere specifici, interoperabili ed eseguibili con risultati misurabili. Di seguito sono riportati alcuni scenari di esempio che è possibile usare:

|Scenario|Dettaglio|Utente tipo|
|---------|---------|---------|
|Asset aziendali critici del catalogo|È necessario disporre di informazioni su ogni set di dati per avere una conoscenza approfondita di ciò che si tratta. Questo scenario include i dati dei metadati aziendali e tecnici relativi al set di dati nel catalogo. Le origini dati includono Azure Data Lake Storage Gen2, Azure sinapsi DW e/o Power BI. Questo scenario include anche risorse locali come SQL Server.|Analista aziendale, data scientist, Data Engineer|
|Individuare asset aziendali critici|È necessario disporre di un motore di ricerca in grado di eseguire ricerche in tutti i metadati del catalogo. Dovrebbe essere possibile eseguire una ricerca usando un termine tecnico, un termine aziendale con una ricerca semplice o complessa usando caratteri jolly.|Business Analyst, data scientist, Data Engineer, data Admins|
|Tenere traccia dei dati per comprenderne l'origine e risolvere i problemi relativi ai dati|È necessario disporre di una derivazione dei dati per tenere traccia dei dati in report, stime o modelli nell'origine originale e comprendere le modifiche e la posizione dei dati durante il ciclo di vita dei dati. Questo scenario deve supportare le pipeline di dati con priorità Azure Data Factory e databricks.|Data Engineer, data scientist|
|Arricchire i metadati in asset di dati critici|È necessario arricchire il set di dati nel catalogo con i metadati tecnici generati automaticamente. Alcuni esempi sono la classificazione e l'assegnazione di etichette.|Data Engineer, dominio/proprietario aziendale|
|Governare gli asset di dati con esperienza utente intuitiva|È necessario disporre di un glossario aziendale per i metadati specifici dell'azienda. Gli utenti aziendali possono utilizzare la competenza per gli scenari self-service per aggiungere annotazioni ai dati e consentire l'individuazione semplice dei dati tramite la ricerca.|Dominio/proprietario aziendale, analista aziendale, data scientist, Data Engineer|

## <a name="deployment-models"></a>Modelli di distribuzione

Se si dispone solo di un piccolo gruppo che usa la competenza con i casi di utilizzo di base del consumo, l'approccio potrebbe essere semplice come avere un'istanza di ambito per servire l'intero gruppo. Tuttavia, è anche possibile chiedersi se l'organizzazione ha bisogno di più di un'istanza di competenza. E se si usano più istanze di ambito, in che modo i dipendenti possono promuovere gli asset da una fase a un'altra.

### <a name="determine-the-number-of-purview-instances"></a>Determinare il numero di istanze di competenza

Nella maggior parte dei casi, deve essere presente un solo account di competenza per l'intera organizzazione. Questo approccio sfrutta al massimo i vantaggi degli "effetti della rete", in cui il valore della piattaforma aumenta in modo esponenziale come funzione dei dati che risiedono all'interno della piattaforma.

Esistono tuttavia eccezioni a questo modello:

1. **Test di nuove configurazioni** : è possibile che le organizzazioni desiderino creare più istanze per testare le configurazioni o le classificazioni di analisi in ambienti isolati. Sebbene esista una funzionalità di "controllo delle versioni" in alcune aree della piattaforma, ad esempio il glossario, è più facile avere un'istanza "Disposable" per testare liberamente.
2. **Separazione di test, pre-produzione e produzione: le** organizzazioni desiderano creare piattaforme diverse per diversi tipi di dati archiviati in ambienti diversi. Non è consigliabile perché questi tipi di dati sono tipi di contenuto diversi. Per separare i tipi di contenuto, è possibile usare un termine di glossario al livello superiore della gerarchia o alla categoria.
3. **Conglomerati e modelli federati** : i conglomerati spesso hanno molte business unit (bus) che operano separatamente e, in alcuni casi, non condividono neanche la fatturazione. In questi casi, l'organizzazione creerà un'istanza di competenza per ogni BU. Questo modello non è ideale, ma potrebbe essere necessario, soprattutto perché il BUs spesso non è disposto a condividere la fatturazione.
4. **Conformità** : esistono alcuni severi regimi di conformità che trattano anche i metadati come sensibili e richiedono che si trovino in una specifica area geografica. Se una società ha più aree geografiche, l'unica soluzione consiste nel disporre di più istanze di competenza, una per ogni geografia.

### <a name="create-a-process-to-move-to-production"></a>Creare un processo per passare all'ambiente di produzione

Alcune organizzazioni possono decidere di semplificare l'uso di una singola versione di produzione di competenza. Probabilmente non devono superare gli scenari di individuazione, ricerca ed esplorazione. Se alcune risorse hanno termini di glossario non corretti, è piuttosto indulgente consentire agli utenti di correggersi autonomamente. Tuttavia, la maggior parte delle organizzazioni che desiderano distribuire le competenze in diverse business unit desidera avere una forma di processo e di controllo.

Un altro aspetto importante da includere nel processo di produzione è il modo in cui è possibile migrare le classificazioni e le etichette. La competenza ha oltre 90 classificatori di sistema. È possibile applicare classificazioni di sistema o personalizzate a risorse di file, tabelle o colonne. Le classificazioni sono simili ai tag Subject e vengono usate per contrassegnare e identificare il contenuto di un tipo specifico trovato all'interno dell'area dati durante l'analisi. Le etichette di riservatezza vengono utilizzate per identificare le categorie di tipi di classificazione nei dati aziendali, quindi raggruppare i criteri che si desidera applicare a ogni categoria. USA gli stessi tipi di informazioni riservate Microsoft 365, consentendo di ampliare i criteri di sicurezza e la protezione esistenti nell'intero contenuto e nell'intero patrimonio di dati. Consente di analizzare e classificare automaticamente i documenti. Se, ad esempio, si dispone di un file denominato multiple.docx e di un numero di ID nazionale nel contenuto, l'ambito aggiungerà la classificazione, ad esempio il numero di identificazione nazionale UE, nella pagina dei dettagli dell'asset.

In ambito di competenza sono disponibili diverse aree in cui gli amministratori del catalogo devono garantire la coerenza e le procedure consigliate per la manutenzione nel ciclo di vita:

* **Asset di dati** : le origini dati devono essere sottoposte a analisi tra gli ambienti. Non è consigliabile eseguire l'analisi solo in fase di sviluppo e quindi rigenerarle usando le API in produzione. Il motivo principale è che gli scanner di competenza eseguono molto più "collegamenti" dietro le quinte degli asset di dati, che possono essere complessi per spostarli in un'istanza di ambito di competenza diversa. È molto più semplice aggiungere la stessa origine dati in produzione e analizzare nuovamente le origini. La procedura consigliata generale prevede la documentazione di tutte le analisi, le connessioni e i meccanismi di autenticazione utilizzati.
* **Scan set** di regole: si tratta della raccolta di regole assegnata a un'analisi specifica, ad esempio il tipo di file e le classificazioni da rilevare. Se non si dispone di molti set di regole di analisi, è possibile crearli nuovamente manualmente tramite la produzione. Questa operazione richiede un processo interno e una documentazione adeguata. Tuttavia, se si regolano le modifiche su base giornaliera o settimanale, questo problema può essere risolto esplorando la route dell'API REST.
* **Classificazioni personalizzate** : le classificazioni potrebbero non essere modificate a intervalli regolari. Durante la fase iniziale della distribuzione, potrebbe essere necessario un po' di tempo per comprendere i vari requisiti per ottenere classificazioni personalizzate. Tuttavia, una volta risolti, questa operazione richiederà una piccola modifica. Quindi, la raccomandazione qui è eseguire manualmente la migrazione di tutte le classificazioni personalizzate o usare l'API REST.
* **Glossario** : è possibile esportare e importare i termini di glossario tramite la UX. Per gli scenari di automazione, è anche possibile usare l'API REST.
* **Criteri dei modelli di set di risorse** : questa funzionalità è molto avanzata per l'applicazione di tutte le organizzazioni tipiche. In alcuni casi, il Azure Data Lake Storage dispone di convenzioni di denominazione delle cartelle e di una struttura specifica che possono causare problemi per la generazione del set di risorse da parte di competenza. La Business Unit può anche voler modificare la costruzione del set di risorse con personalizzazioni aggiuntive per soddisfare le esigenze aziendali. Per questo scenario, è consigliabile tenere traccia di tutte le modifiche tramite l'API REST e documentare le modifiche tramite la piattaforma di controllo delle versioni esterna.
* **Assegnazione di ruolo** : consente di controllare gli utenti che hanno accesso a competenza e le autorizzazioni di cui dispongono. La competenza dispone anche di un'API REST per supportare l'esportazione e l'importazione di utenti e ruoli, ma non è compatibile con l'API Atlas. È consigliabile assegnare un gruppo di sicurezza di Azure e gestire l'appartenenza al gruppo.

### <a name="plan-and-implement-different-integration-points-with-purview"></a>Pianificare e implementare punti di integrazione diversi con la competenza

È probabile che un'organizzazione matura abbia già un catalogo dati esistente. La domanda fondamentale è se continuare a usare la tecnologia esistente e la sincronizzazione con l'ambito. Per gestire la sincronizzazione con i prodotti esistenti in un'organizzazione, la competenza fornisce le API REST Atlas. Le API Atlas forniscono un meccanismo potente e flessibile per la gestione di scenari push e pull. Le informazioni possono essere pubblicate per la competenza usando le API Atlas per il bootstrap o per effettuare il push degli aggiornamenti più recenti da un altro sistema in ambito. Le informazioni disponibili in ambito di competenza possono essere lette anche usando le API Atlas e quindi sincronizzate con i prodotti esistenti. 

Per altri scenari di integrazione, ad esempio il ticketing, l'interfaccia utente personalizzata e l'orchestrazione, è possibile usare le API Atlas e gli endpoint Kafka. In generale, sono disponibili quattro punti di integrazione con competenza:

* **Asset di dati** : consente alle competenze di analizzare le risorse di un negozio per enumerare le risorse e raccogliere tutti i metadati prontamente disponibili. Per SQL, quindi, potrebbe trattarsi di un elenco di database, tabelle, stored procedure, viste e dati di configurazione su di essi mantenuti in luoghi come `sys.tables` . Per elementi come Azure Data Factory (ADF), è possibile che l'enumerazione di tutte le pipeline e il recupero dei dati al momento della creazione, dell'ultima esecuzione, dello stato corrente.
* Derivazione **: consente** alle competenze di raccogliere informazioni da un sistema di mutazione di analisi/dati sul modo in cui i dati si spostano. Per un elemento come Spark, questo potrebbe raccogliere informazioni dall'esecuzione di un notebook per visualizzare i dati inseriti dal notebook, il modo in cui sono stati trasformati e la posizione in cui sono stati generati. Per quanto riguarda SQL, è possibile che si stia analizzando i log di query per decodificare le operazioni di mutazione eseguite e le operazioni eseguite. Sono supportate sia la derivazione basata su push che quella basata su pull, a seconda delle esigenze.
* **Classificazione** : consente alle competenze di eseguire esempi fisici dalle origini dati ed eseguirli tramite il sistema di classificazione. Il sistema di classificazione calcola la semantica di una parte di dati. Ad esempio, è possibile che un file sia un file parquet con tre colonne e la terza è una stringa. Tuttavia, i classificatori eseguiti sugli esempi ci indicano che la stringa è un nome, un indirizzo o un numero di telefono. L'illuminazione di questo punto di integrazione significa che è stato definito il modo in cui la competenza può aprire oggetti quali notebook, pipeline, file parquet, tabelle e contenitori.
* **Esperienza incorporata** : i prodotti che hanno un'esperienza simile a "studio", ad esempio ADF, sinapsi, SQL Studio, PBI e Dynamics, in genere vogliono consentire agli utenti di individuare i dati che vogliono interagire con e trovare anche le posizioni per l'output dei dati. Il catalogo di una competenza può aiutare ad accelerare queste esperienze offrendo un'esperienza di incorporamento. Questa esperienza può verificarsi a livello di API o di UX nell'opzione del partner. Incorporando una chiamata a competenza, l'organizzazione può sfruttare i vantaggi della mappa del patrimonio di competenze per individuare gli asset di dati, vedere derivazione, controllare gli schemi, esaminare le classificazioni, i contatti e così via.

## <a name="phase-1-pilot"></a>Fase 1: progetto pilota

In questa fase, è necessario creare e configurare la competenza per un set di utenti molto ridotto. In genere, è solo un gruppo di 2-3 persone che collaborano per eseguire gli scenari end-to-end. Sono considerati i sostenitori delle competenze della propria organizzazione. L'obiettivo principale di questa fase è garantire che siano soddisfatte le funzionalità chiave e che le parti interessate appropriate siano consapevoli del progetto.

### <a name="tasks-to-complete"></a>Attività da completare

|Attività|Dettaglio|Duration|
|---------|---------|---------|
|Raccogliere & accettare i requisiti|Discussione con tutte le parti interessate per raccogliere un set completo di requisiti. Per ogni fase del progetto, è necessario che altri utenti debbano accettare un subset di requisiti.|una settimana|
|Configurare la starter kit|Esaminare il [avvio rapido di competenza](create-catalog-portal.md) e configurare lo [Starter Kit di competenza](tutorial-scan-data.md) per le competenze dimostrative per tutte le parti interessate.|1 giorno|
|Esplorazione della competenza|Informazioni su come usare la competenza dal home page.|1 giorno|
|Configurare ADF per la derivazione|Identificare le pipeline chiave e gli asset di dati. Raccogliere tutte le informazioni necessarie per connettersi a un account ADF interno.|1 giorno|
|Eseguire l'analisi di un'origine dati, ad esempio Azure Data Lake Storage|Aggiungere l'origine dati e configurare un'analisi. Verificare che l'analisi rilevi correttamente tutti gli asset.|2 giorni|
|Cerca ed Esplora|Consenti agli utenti finali di accedere alle competenze ed eseguire ricerche end-to-end e scenari di esplorazione.|1 giorno|

### <a name="acceptance-criteria"></a>Criteri di accettazione

* L'account di competenza è stato creato correttamente nella sottoscrizione dell'organizzazione nel tenant dell'organizzazione.
* Un piccolo gruppo di utenti con più ruoli può accedere a competenza.
* La competenza è configurata per l'analisi di almeno un'origine dati.
* Gli utenti devono essere in grado di estrarre i valori chiave di competenza, ad esempio:
  * Cerca ed Esplora
  * Derivazione
* Gli utenti devono essere in grado di assegnare la proprietà Asset nella pagina asset.
* Presentazione e demo per sensibilizzare le principali parti interessate.
* Acquistare da gestione per approvare risorse aggiuntive per la fase MVP.

## <a name="phase-2-minimum-viable-product"></a>Fase 2: prodotto minimo valido

Una volta che sono stati concordati i requisiti e le business unit che hanno partecipato a onboarding, il passaggio successivo consiste nell'utilizzare una versione del prodotto (MVP) minima. In questa fase si espanderà l'utilizzo di competenza per più utenti che avranno esigenze aggiuntive orizzontalmente e verticalmente. Ci saranno scenari principali che devono essere soddisfatti orizzontalmente per tutti gli utenti, ad esempio i termini di glossario, la ricerca e il browse. Per ogni business unit o gruppo verranno inoltre rilevati requisiti di approfondimento in modo verticale per coprire scenari end-to-end specifici, ad esempio la derivazione da Azure Data Lake Storage ad Azure sinapsi DW al Power BI.

### <a name="tasks-to-complete"></a>Attività da completare

|Attività|Dettaglio|Duration|
|---------|---------|---------|
|[Analizza analisi sinapsi di Azure](register-scan-azure-synapse-analytics.md)|Iniziare a caricare le origini del database e analizzarle per popolare gli asset chiave|2 giorni|
|[Creare regole e classificazioni personalizzate](create-a-custom-classification-and-classification-rule.md)|Una volta analizzate le risorse, gli utenti possono tenere presente che esistono altri casi d'uso per una maggiore classificazione accanto alle classificazioni predefinite di competenza.|2-4 settimane|
|[Analizza Power BI](register-scan-power-bi-tenant.md)|Se l'organizzazione USA Power BI, è possibile analizzare Power BI per raccogliere tutti gli asset di dati usati dai data scientist o dagli analisti dei dati che hanno requisiti per includere la derivazione dal livello di archiviazione.|1-2 settimane|
|[Importa termini di glossario](how-to-create-import-export-glossary.md)|Nella maggior parte dei casi, l'organizzazione potrebbe già sviluppare una raccolta di termini di glossario e di assegnazione di termini agli asset. Questa operazione richiede un processo di importazione nel file con estensione CSV.|una settimana|
|Aggiungere contatti agli asset|Per gli asset principali, potrebbe essere necessario stabilire un processo per consentire ad altri utenti di assegnare contatti o importare tramite le API REST.|una settimana|
|Aggiungere etichette e analisi riservate|Questo potrebbe essere facoltativo per alcune organizzazioni, a seconda dell'utilizzo delle etichette da M365.|1-2 settimane|
|Ottenere informazioni di classificazione e sensibili|Per la creazione di report e informazioni dettagliate, è possibile accedere a questa funzionalità per ottenere vari report e fornire una presentazione alla gestione.|1 giorno|
|Onboarding di utenti aggiuntivi con gli utenti gestiti di competenza|Questo passaggio richiederà all'amministratore di competenza di collaborare con l'amministratore di Azure Active Directory per stabilire nuovi gruppi di sicurezza per concedere l'accesso a livello di competenza.|una settimana|

### <a name="acceptance-criteria"></a>Criteri di accettazione

* L'onboarding di un gruppo di utenti più ampio è stato completato (50 +)
* Analizza origini dati aziendali critiche
* Importa e assegna tutti i termini di glossario critici
* Test delle etichette importanti sugli asset chiave completata
* Sono stati soddisfatti i minimi scenari per gli utenti delle unità aziendali partecipanti

## <a name="phase-3-pre-production"></a>Fase 3: pre-produzione

Una volta superata la fase MVP, è possibile pianificare l'attività cardine di pre-produzione. L'organizzazione può decidere di disporre di un'istanza separata di ambito per la pre-produzione e la produzione oppure di mantenete la stessa istanza, ma limitando l'accesso. In questa fase, è anche possibile includere l'analisi sulle origini dati locali, ad esempio SQL Server. Se si verificano gap nelle origini dati non supportate da competenza, è possibile esplorare l'API Atlas per comprendere le opzioni aggiuntive.

### <a name="tasks-to-complete"></a>Attività da completare

|Attività|Dettaglio|Duration|
|---------|---------|---------|
|Perfezionare l'analisi con il set di regole di analisi|L'organizzazione avrà una grande quantità di origini dati per la pre-produzione. È importante predefinire i criteri chiave per l'analisi, in modo che le classificazioni e l'estensione di file possano essere applicate in modo coerente nella bacheca.|1-2 giorni|
|Valutazione della disponibilità dell'area per l'analisi|A seconda dell'area geografica delle origini dati e dei requisiti aziendali per la conformità e la sicurezza, è opportuno considerare le aree che devono essere disponibili per l'analisi.|1 giorno|
|Informazioni sul concetto di firewall durante l'analisi|Questo passaggio richiede un'esplorazione del modo in cui l'organizzazione configura il firewall e il modo in cui la competenza può autenticarsi per accedere alle origini dati per l'analisi.|1 giorno|
|Informazioni sul concetto di collegamento privato durante l'analisi|Se l'organizzazione usa un collegamento privato, è necessario stabilire la base della sicurezza di rete per includere il collegamento privato come parte dei requisiti.|1 giorno|
|[Analizza SQL Server locale](register-scan-on-premises-sql-server.md)|Questa opzione è facoltativa se si dispone di SQL Server locali. Per eseguire l'analisi è necessario configurare [Integration runtime indipendenti](manage-integration-runtimes.md) e aggiungere SQL Server come origine dati.|1-2 settimane|
|Usare l'API REST di competenza per gli scenari di integrazione|Se si dispone di requisiti per integrare la competenza con altre tecnologie di terze parti, ad esempio il sistema di orchestrazione o di ticket, può essere utile esplorare l'area API REST.|1-4 settimane|
|Informazioni sui prezzi di competenza|Questo passaggio fornirà all'organizzazione importanti informazioni finanziarie per prendere decisioni.|1-5 giorni|

### <a name="acceptance-criteria"></a>Criteri di accettazione

* L'onboarding di almeno una business unit con tutti gli utenti è stata completata
* Eseguire l'analisi dell'origine dati locale, ad esempio SQL Server
* POC almeno uno scenario di integrazione con l'API REST
* Completare un piano per passare all'ambiente di produzione, che dovrebbe includere aree principali sull'infrastruttura e sulla sicurezza

## <a name="phase-4-production"></a>Fase 4: produzione

È necessario seguire le fasi precedenti per creare una governance delle informazioni efficace, che costituisce la base per migliorare i programmi di governance. Governance dei dati consente all'organizzazione di prepararsi per le tendenze crescenti, ad esempio AI, Hadoop, Internet e blockchain. Si tratta solo dell'inizio per i dati e l'analisi di molti elementi ed è possibile discutere molto altro. Il risultato di questa soluzione fornirebbe:

* **Focalizzato sull'azienda** : una soluzione allineata ai requisiti e agli scenari aziendali rispetto ai requisiti tecnici.
* Una soluzione pronta per il **futuro** consente di ottimizzare le funzionalità predefinite della piattaforma e di utilizzare le procedure di settore standardizzate per le attività di configurazione o di scripting per supportare i miglioramenti e l'evoluzione della piattaforma.

### <a name="tasks-to-complete"></a>Attività da completare

|Attività|Dettaglio|Duration|
|---------|---------|---------|
|Analizza le origini dati di produzione con il firewall abilitato|Se questa opzione è facoltativa quando il firewall è presente, ma è importante esplorare le opzioni per la protezione avanzata dell'infrastruttura.|1-5 giorni|
|Abilita collegamento privato|Se questa opzione è facoltativa quando si usa il collegamento privato. In caso contrario, è possibile ignorare questo perché è necessario avere un criterio quando privato è abilitato.|1-5 giorni|
|Creazione di un flusso di lavoro automatizzato|Il flusso di lavoro è importante per automatizzare i processi, ad esempio approvazione, escalation, revisione e gestione dei problemi.|2-3 settimane|
|Documentazione operativa|La governance dei dati non è un progetto una volta. Si tratta di un programma in corso per alimentare il processo decisionale basato sui dati e creare opportunità per le aziende. È fondamentale per documentare la procedura chiave e gli standard aziendali.|una settimana|

### <a name="acceptance-criteria"></a>Criteri di accettazione

* Onboarding di tutte le business unit e degli utenti
* Soddisfare i requisiti di infrastruttura e sicurezza per la produzione
* Tutti i casi d'uso richiesti dagli utenti sono stati soddisfatti

## <a name="platform-hardening"></a>Protezione avanzata della piattaforma

È possibile eseguire ulteriori passaggi di protezione avanzata:

* Aumentare il comportamento di sicurezza abilitando l'analisi sulle risorse del firewall o usando il collegamento privato
* Ottimizzazione dell'analisi dell'ambito per migliorare le prestazioni di analisi
* Usare le API REST per esportare i metadati e le proprietà critici per il backup e il ripristino
* Usare il flusso di lavoro per automatizzare i ticket e la gestione degli eventi per evitare errori umani

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: eseguire il starter kit e analizzare i dati](tutorial-scan-data.md)
- [Esercitazione: esplorare il home page e cercare un asset](tutorial-asset-search.md)