---
title: 'Azure Data Factory: domande frequenti '
description: Risposte alle domande frequenti su Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 2027e3555a7eb616ad024ec00bf6b0f8f452167c
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258521"
---
# <a name="azure-data-factory-faq"></a>Domande frequenti su Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo risponde ad alcune domande frequenti su Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Che cos'è Azure Data Factory?

Data Factory è un servizio ETL completamente gestito basato sul cloud per l'integrazione dei dati che automatizza lo spostamento e la trasformazione dei dati. Analogamente a quanto avviene in uno stabilimento di produzione, in cui vengono usate attrezzature per trasformare le materie prime in prodotti finiti, Azure Data Factory orchestra i servizi esistenti che raccolgono i dati non elaborati e li trasforma in informazioni pronte per l'uso. 

Usando Azure Data Factory, è possibile creare flussi di lavoro basati sui dati per spostare i dati tra archivi dati locali e cloud. Ed è possibile elaborare e trasformare i dati con flussi di dati. ADF supporta anche motori di calcolo esterni per le trasformazioni codificate manualmente usando servizi di calcolo come Azure HDInsight, Azure Databricks e il runtime di integrazione di SQL Server Integration Services (SSIS).

Con Data Factory, è possibile eseguire l'elaborazione dei dati in un servizio cloud basato su Azure oppure nell'ambiente di calcolo self-hosted, ad esempio SSIS, SQL Server o Oracle. Dopo aver creato una pipeline che esegue l'azione necessaria, è possibile pianificarne l'esecuzione periodica (ad esempio, ogni ora, ogni giorno o ogni settimana), la pianificazione della finestra temporale o attivare la pipeline da un'occorrenza di evento. Per altre informazioni, vedere l'[introduzione ad Azure Data Factory](introduction.md).

## <a name="compliance-and-security-considerations"></a>Considerazioni sulla conformità e sulla sicurezza

Azure Data Factory è certificato per una gamma di certificazioni di conformità, tra cui _SOC 1, 2, 3_, _HIPAA BAA_ e _HITRUST_. L'elenco completo e in continua crescita delle certificazioni è disponibile [qui](data-movement-security-considerations.md). Le copie digitali per i report di controllo e le certificazioni di conformità sono reperibili in [Service Trust Center](https://servicetrust.microsoft.com/)

### <a name="control-flows-and-scale"></a>Flussi di controllo e scalabilità

Per supportare i diversi flussi e modelli di integrazione nella data warehouse moderna, Data Factory consente la modellazione di pipeline di dati flessibile. Questo include i paradigmi di programmazione del flusso di controllo completi, tra cui l'esecuzione condizionale, la diramazione nelle pipeline di dati e la possibilità di passare in modo esplicito i parametri all'interno e in questi flussi. Il flusso di controllo comprende anche la trasformazione dei dati tramite la distribuzione di attività a motori di esecuzione esterni e funzionalità del flusso di dati, incluso lo spostamento dei dati su larga scala, tramite l'attività di copia.

Data Factory consente di modellare liberamente qualsiasi stile di flusso che risulta necessario per l'integrazione dei dati e che può essere inviato su richiesta o ripetutamente in una pianificazione. Di seguito sono riportati alcuni dei flussi comuni abilitati:

- Flussi di controllo:
    - Le attività possono essere concatenate in una sequenza all'interno di una pipeline.
    - Le attività possono essere diramate all'interno di una pipeline.
    - Parametri
        * I parametri possono essere definiti a livello di pipeline e gli argomenti possono essere passati quando si richiama la pipeline su richiesta o da un trigger.
        * Le attività possono utilizzare gli argomenti passati alla pipeline.
    - Passaggio stato personalizzato:
        * Gli output delle attività, incluso lo stato, possono essere usati da un'attività successiva nella pipeline.
    - Ciclo di contenitori:
        * L'attività ForEach eseguirà l'iterazione di una raccolta specificata di attività in un ciclo. 
- Flussi attivati da trigger:
    - Le pipeline possono essere attivate su richiesta, dal tempo reale o in risposta agli argomenti basati su griglia di eventi
- Flussi delta:
    - I parametri possono essere usati per definire il limite massimo per la copia Delta durante lo trasferimento di tabelle di dimensioni o di riferimento da un archivio relazionale, in locale o nel cloud, per caricare i dati nel Lake.

Per altre informazioni, vedere [Esercitazione: Flussi di controllo](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Dati trasformati su larga scala con pipeline senza codice

I nuovi strumenti basati su browser forniscono funzionalità di creazione e distribuzione di pipeline senza codice offrendo un'esperienza moderna e interattiva basata sul Web.

Per gli sviluppatori di dati e gli ingegneri di dati visivi, il Data Factory interfaccia utente Web è l'ambiente di progettazione senza codice che verrà usato per compilare le pipeline. È completamente integrato con Visual Studio online git e fornisce l'integrazione per CI/CD e lo sviluppo iterativo con le opzioni di debug.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>SDK completi multipiattaforma per utenti avanzati

Data Factory V2 offre un set completo di SDK che è possibile usare per creare, gestire e monitorare le pipeline usando l'IDE preferito, tra cui:

* Python SDK
* INTERFACCIA della riga di comando di PowerShell
* SDK per C#

Gli utenti possono anche usare le API REST documentate per interfacciarsi con Data Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Sviluppo e debug iterativi tramite strumenti visivi

Azure Data Factory strumenti visivi abilitano lo sviluppo e il debug iterativi. È possibile creare le pipeline ed eseguire le esecuzioni dei test usando la funzionalità di **debug** nel Canvas della pipeline senza scrivere una sola riga di codice. È possibile visualizzare i risultati delle esecuzioni dei test nella finestra **output** dell'area di disegno della pipeline. Una volta completata l'esecuzione dei test, è possibile aggiungere altre attività alla pipeline e continuare il debug in modo iterativo. È anche possibile annullare le esecuzioni dei test dopo che sono in corso.

Non è necessario pubblicare le modifiche nel servizio data factory prima di selezionare **debug**. Questa operazione è utile negli scenari in cui si desidera assicurarsi che le nuove aggiunte o modifiche funzioneranno come previsto prima di aggiornare i flussi di lavoro di data factory in ambienti di sviluppo, test o produzione.

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Possibilità di distribuire pacchetti SSIS in Azure

Per spostare i carichi di lavoro SSIS, è possibile creare un'istanza di Data Factory ed effettuare il provisioning di un runtime di integrazione SSIS di Azure. Un runtime di integrazione SSIS di Azure è un cluster completamente gestito di macchine virtuali (nodi) di Azure dedicate all'esecuzione di pacchetti SSIS nel cloud. Per istruzioni dettagliate, vedere l'esercitazione [Distribuire i pacchetti SSIS in Azure](./tutorial-deploy-ssis-packages-azure.md). 

### <a name="sdks"></a>SDK

Se si è un utente avanzato e si cerca un'interfaccia a livello di codice, Data Factory offre un set completo di SDK che è possibile usare per creare, gestire o monitorare le pipeline usando l'IDE preferito. Sono supportati, tra gli altri, i linguaggi .NET, PowerShell, Python e REST.

### <a name="monitoring"></a>Monitoraggio

È possibile monitorare le istanze di Data Factory tramite PowerShell, SDK o gli strumenti di monitoraggio visivi nell'interfaccia utente del browser. È possibile monitorare e gestire flussi personalizzati basati su richiesta, basati su trigger e basati su clock in modo efficiente ed efficace. Annulla le attività esistenti, Visualizza gli errori a colpo d'occhio, Esegui il drill-down per visualizzare i messaggi di errore dettagliati ed Esegui il debug dei problemi, tutto da un unico riquadro di vetro senza cambio di contesto o spostamento tra le schermate.

### <a name="new-features-for-ssis-in-data-factory"></a>Nuove funzionalità per SSIS in Data Factory

Dalla versione di anteprima pubblica iniziale in 2017, Data Factory ha aggiunto le funzionalità seguenti per SSIS:

-    Supporto per altre tre configurazioni/varianti del database SQL di Azure per ospitare il database SSIS (SSISDB) di progetti/pacchetti:
-    Database SQL con endpoint servizio rete virtuale
-    Istanza gestita di SQL
-    Pool elastico
-    Il supporto per un Azure Resource Manager rete virtuale in una rete virtuale classica verrà deprecato in futuro, che consente di inserire/aggiungere il runtime di integrazione Azure-SSIS a una rete virtuale configurata per il database SQL con endpoint servizio rete virtuale/MI/i dati locali. Per altre informazioni, vedere anche [aggiungere un runtime di integrazione SSIS di Azure a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md).
-    Supporto per l'autenticazione Azure Active Directory (Azure AD) e l'autenticazione SQL per la connessione al database SSISDB, consentendo l'autenticazione Azure AD con l'identità gestita Data Factory per le risorse di Azure
-    Supporto per l'introduzione della licenza di SQL Server esistente per ottenere risparmi sostanziali sui costi dall'opzione Vantaggio Azure Hybrid
-    Supporto per Enterprise Edition del runtime di integrazione SSIS di Azure che consente di usare le funzionalità avanzate/Premium, un'interfaccia di installazione personalizzata per installare componenti/estensioni aggiuntivi e un ecosistema di partner. Per ulteriori informazioni, vedere anche [Enterprise Edition, installazione personalizzata e estendibilità di terze parti per SSIS in ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-    Integrazione più approfondita di SSIS in Data Factory che consente di richiamare/attivare le attività di esecuzione dei pacchetti SSIS di prima classe in Data Factory pipeline e di pianificarle tramite SSMS. Per altre informazioni, vedere anche [modernizzare ed estendere i flussi di lavoro ETL/ELT con le attività SSIS nelle pipeline di ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).

## <a name="what-is-the-integration-runtime"></a>Che cos'è il runtime di integrazione?

Integration Runtime è l'infrastruttura di calcolo che Azure Data Factory USA per fornire le funzionalità di integrazione dei dati seguenti in diversi ambienti di rete:

- **Spostamento dei** dati: per lo spostamento dei dati, il runtime di integrazione Sposta i dati tra gli archivi dati di origine e di destinazione, fornendo al tempo stesso il supporto per connettori incorporati, conversione di formato, mapping di colonne e trasferimento di dati scalabile e a prestazioni elevate.
- **Attività di invio**: per la trasformazione, il runtime di integrazione fornisce funzionalità per l'esecuzione nativa di pacchetti SSIS.
- **Eseguire pacchetti SSIS**: il runtime di integrazione esegue in modo nativo i pacchetti SSIS in un ambiente di calcolo di Azure gestito. Integration Runtime supporta anche l'invio e il monitoraggio delle attività di trasformazione in esecuzione in un'ampia gamma di servizi di calcolo, ad esempio Azure HDInsight, Azure Machine Learning, database SQL e SQL Server.

È possibile distribuire una o più istanze del runtime di integrazione come richiesto per lo spostamento e la trasformazione dei dati. Il runtime di integrazione può essere eseguito in una rete pubblica di Azure o in una rete privata (locale, rete virtuale di Azure o Amazon Web Services cloud privato virtuale [VPC]).

Per altre informazioni, vedere il [Runtime di integrazione in Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Qual è il limite al numero di runtime di integrazione?

Non sono previsti limiti rigidi per il numero di istanze di runtime di integrazione che è possibile avere in una data factory. È stato posto tuttavia un limite al numero di core di VM che il runtime di integrazione può usare per ogni sottoscrizione per l'esecuzione del pacchetto SSIS. Per altre informazioni, vedere [Limiti della data factory](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Quali sono i principali concetti su cui si basa Azure Data Factory?

Una sottoscrizione di Azure può includere una o più istanze di Azure Data Factory (o data factory). Azure Data Factory contiene quattro componenti chiave che interagiscono come piattaforma nella quale è possibile comporre flussi di lavoro basati sui dati con passaggi per lo spostamento e la trasformazione dei dati stessi.

### <a name="pipelines"></a>Pipeline

Una data factory può comprendere una o più pipeline. Una pipeline è un raggruppamento logico di attività per eseguire un'unità di lavoro, L'insieme delle attività di una pipeline esegue un'operazione. Una pipeline, ad esempio, può contenere un gruppo di attività che inseriscono dati da un BLOB di Azure e quindi eseguono una query Hive in un cluster HDInsight per partizionare i dati. Il vantaggio è che è possibile usare una pipeline per gestire le attività come set invece che singolarmente. È possibile concatenare le attività in una pipeline per usarle in modo sequenziale o indipendentemente in parallelo.

### <a name="data-flows"></a>Flussi di dati

I flussi di dati sono oggetti compilati visivamente in Data Factory che trasformano i dati su larga scala nei servizi Spark back-end. Non è necessario comprendere gli elementi interni di programmazione o Spark. È sufficiente progettare l'intenzione di trasformazione dei dati usando grafici (mapping) o fogli di calcolo (litigi).

### <a name="activities"></a>Attività

Le attività rappresentano un passaggio di elaborazione in una pipeline. Ad esempio, è possibile usare un'attività di copia per copiare dati da un archivio dati a un altro archivio dati. Allo stesso modo, è possibile usare un'attività Hive che esegue una query Hive su un cluster Azure HDInsight per trasformare o analizzare i dati. Data Factory supporta tre tipi di attività: attività di spostamento dei dati, attività di trasformazione dei dati e attività di controllo.

### <a name="datasets"></a>Set di dati

I set di dati rappresentano strutture dei dati all'interno degli archivi dati e fanno semplicemente riferimento ai dati da usare nelle attività come input o output. 

### <a name="linked-services"></a>Servizi collegati

I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. In questo modo, un servizio collegato definisce la connessione all'origine dati e un set di dati rappresenta la struttura dei dati. Ad esempio, un servizio collegato di Archiviazione di Azure specifica la stringa per la connessione all'account di archiviazione di Azure. Un set di dati BLOB di Azure specifica il contenitore BLOB e la cartella che contiene i dati.

In Data Factory i servizi collegati hanno due scopi:

- Per rappresentare un *archivio dati* che include, a titolo esemplificativo, un'istanza SQL Server, un'istanza del database Oracle, una condivisione file o un account di archiviazione BLOB di Azure. Per un elenco di archivi dati supportati, vedere [Attività di copia in Azure Data Factory](copy-activity-overview.md).
- Per rappresentare una *risorsa di calcolo* che può ospitare l'esecuzione di un'attività. Ad esempio, l'attività HDInsight Hive viene eseguita in un cluster HDInsight Hadoop. Per un elenco delle attività di trasformazione e degli ambienti di calcolo supportati, vedere [Trasformare i dati in Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Trigger

I trigger rappresentano unità di elaborazione che determinano quando viene avviata l'esecuzione di una pipeline. Esistono diversi tipi di trigger per i diversi tipi di eventi. 

### <a name="pipeline-runs"></a>Esecuzioni di pipeline

Un'esecuzione di pipeline è un'istanza dell'esecuzione di una pipeline. In genere si crea un'istanza di un'esecuzione di pipeline passando gli argomenti ai parametri definiti nella pipeline. È possibile passare gli argomenti manualmente o nella definizione di trigger.

### <a name="parameters"></a>Parametri

I parametri sono coppie chiave-valore in una configurazione di sola lettura.Si definiscono i parametri in una pipeline ed è possibile passare gli argomenti per i parametri definiti durante l'esecuzione da un contesto di esecuzione. Il contesto di esecuzione viene creato da un trigger o da una pipeline eseguita manualmente. Le attività all'interno della pipeline usano i valori dei parametri.

Un set di dati è un parametro fortemente tipizzato e un'entità che è possibile riutilizzare o a cui fare riferimento. Un'attività può fare riferimento a set di dati e può utilizzare le proprietà definite nella definizione del set di dati.

Anche un servizio collegato è un parametro fortemente tipizzato contenente le informazioni di connessione a un archivio dati o a un ambiente di calcolo. È anche un'entità che è possibile riutilizzare o a cui fare riferimento.

### <a name="control-flows"></a>Flussi di controllo

I flussi di controllo orchestrano le attività della pipeline che includono concatenamento di attività in una sequenza, diramazione, parametri definiti a livello di pipeline e argomenti passati quando si richiama la pipeline su richiesta o da un trigger. I flussi di controllo includono anche il passaggio di stato personalizzato e i contenitori di ciclo (ovvero gli iteratori foreach).


Per altre informazioni sui concetti relativi a Data Factory, vedere gli articoli seguenti:

- [Set di dati e servizi collegati](concepts-datasets-linked-services.md)
- [Pipeline e attività](concepts-pipelines-activities.md)
- [Runtime di integrazione](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Qual è il modello di prezzi per Data Factory?

Per informazioni dettagliate sui prezzi di Azure Data Factory, vedere [Dettagli prezzi di Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Come è possibile avere sempre a disposizione le informazioni più aggiornate su Data Factory?

Per le informazioni più aggiornate su Azure Data Factory, andare ai siti seguenti:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Home page della documentazione](./index.yml)
- [Home page prodotti](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Approfondimento tecnico

### <a name="how-can-i-schedule-a-pipeline"></a>Come è possibile pianificare una pipeline?

Per pianificare una pipeline, è possibile usare il trigger di pianificazione o quello relativo agli intervalli di tempo. Il trigger usa una pianificazione del calendario del tempo reale, che può pianificare le pipeline periodicamente o in modelli ricorrenti basati sul calendario (ad esempio, il lunedì alle 6:00 PM e giovedì alle 9:00 PM). Per altre informazioni, vedere [Esecuzione e trigger della pipeline](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>È possibile passare parametri all'esecuzione di una pipeline?

Sì, i parametri sono un concetto di primo livello di livello principale in Data Factory. È possibile definire i parametri a livello di pipeline e passare gli argomenti mentre si esegue la pipeline su richiesta o usando un trigger.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>È possibile definire i valori predefiniti per i parametri della pipeline?

Sì. È possibile definire i valori predefiniti per i parametri nelle pipeline.

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Un'attività in una pipeline può utilizzare gli argomenti passati a un'esecuzione di pipeline?

Sì. Ogni attività all'interno della pipeline può utilizzare il valore del parametro passato alla pipeline ed eseguito con il costrutto `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Una proprietà di output di attività può essere utilizzata in un'altra attività?

Sì. È possibile utilizzare un output di attività in un'attività successiva con il costrutto `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Come gestire correttamente i valori null in un output di attività?

È possibile utilizzare il costrutto `@coalesce` nelle espressioni per gestire correttamente i valori null.

## <a name="mapping-data-flows"></a>Flussi di dati di mapping

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Ho bisogno di aiuto per la risoluzione dei problemi della logica del flusso di dati. Quali informazioni è necessario fornire per ottenere assistenza?

Quando Microsoft fornisce la guida o la risoluzione dei problemi relativi ai flussi di dati, specificare lo script del flusso di dati. Questo è lo script code-behind del grafico del flusso di dati. Dall'interfaccia utente di ADF aprire il flusso di dati e quindi fare clic sul pulsante "script" nell'angolo superiore destro. Copiare e incollare lo script o salvarlo in un file di testo.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Ricerca per categorie accedere ai dati usando gli altri tipi di set di dati 90 Data Factory?

La funzionalità del flusso di dati di mapping consente attualmente il database SQL di Azure, Azure sinapsi Analytics, i file di testo delimitati dall'archiviazione BLOB di Azure o Azure Data Lake Storage Gen2 e i file parquet dall'archiviazione BLOB o Data Lake Storage Gen2 in modo nativo per l'origine e il sink. 

Usare l'attività di copia per organizzare i dati da uno qualsiasi degli altri connettori, quindi eseguire un'attività flusso di dati per trasformare i dati dopo che sono stati gestiti in modo temporaneo. Ad esempio, la pipeline verrà prima copiata nell'archivio BLOB e quindi un'attività flusso di dati utilizzerà un set di dati in origine per trasformare i dati.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Il runtime di integrazione self-hosted è disponibile per i flussi di dati?

Il runtime di integrazione self-hosted è un costrutto di pipeline ADF che è possibile usare con l'attività di copia per acquisire o spostare dati da e verso origini dati e sink locali o basati su VM. Le macchine virtuali usate per un runtime di integrazione self-hosted possono anche essere inserite all'interno della stessa VNET degli archivi dati protetti per l'accesso a tali archivi dati da ADF. Con i flussi di dati, si otterranno questi stessi risultati finali usando invece il Azure IR con VNET gestito.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>Il motore di calcolo del flusso di dati serve più tenant?

I cluster non vengono mai condivisi. Garantiamo l'isolamento per ogni processo eseguito in esecuzioni di produzione. Nel caso di uno scenario di debug, una persona ottiene un cluster e tutti i bug verranno inviati a tale cluster, che vengono avviati dall'utente.

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni dettagliate per la creazione di una data factory, vedere le esercitazioni seguenti:

- [Guida introduttiva: creare un data factory](quickstart-create-data-factory-dot-net.md)
- [Esercitazione: Copiare i dati nel cloud](tutorial-copy-data-dot-net.md)
