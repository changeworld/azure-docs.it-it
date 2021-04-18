---
title: Portare il proprio ML in Azure Sentinel | Microsoft Docs
description: Questo articolo illustra come creare e usare algoritmi di Machine Learning personalizzati per l'analisi dei dati in Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 2164b8ac6e62b8826d5879da07384769c503bfb5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598602"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Portare il proprio Machine Learning (ML) in Azure Sentinel

Machine Learning (ML) è uno dei principali elementi di base di Azure Sentinel e uno degli attributi principali che lo distingue. Azure Sentinel ml offre ml in diverse esperienze: integrato nel motore di correlazione [Fusion](fusion.md) e nei notebook jupyter e nella nuova piattaforma Build-Your-Own ML (BYO ML). 

I modelli di rilevamento ml possono adattarsi ai singoli ambienti e alle modifiche del comportamento degli utenti, per ridurre i falsi positivi e identificare le minacce che non si trovano con un approccio tradizionale. Molte organizzazioni di sicurezza comprendono il valore di ML per la sicurezza, anche se non molte di esse hanno il lusso di professionisti che hanno competenze in sicurezza e ML. Il framework presentato qui è stato progettato per consentire alle organizzazioni e ai professionisti della sicurezza di crescere con noi nel loro percorso di Machine Learning. Le organizzazioni che non hanno esperienza in ML o senza le competenze necessarie possono ottenere un valore di protezione significativo Azure Sentinel funzionalità di Machine Learning integrate di Azure Sentinel.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="framework di Machine Learning":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Che cos'è la piattaforma Bring Your Own Machine Learning (BYO-ML) ?

Per le organizzazioni che dispongono di risorse di Machine Learning e che desiderano creare modelli di Machine Learning personalizzati per le esigenze aziendali specifiche, è offerta la **piattaforma BYO-ML.** La piattaforma usa [l'ambiente](/azure/databricks/scenarios/what-is-azure-databricks)Azure Databricks Apache Spark e / [](http://spark.apache.org/) Jupyter Notebook per produrre l'ambiente ml. Fornisce i componenti seguenti:

- un pacchetto BYO-ML, che include librerie che consentono di accedere ai dati ed eseguire il push dei risultati in Log Analytics (LA), in modo da poter integrare i risultati con il rilevamento, l'analisi e la ricerca. 

- Modelli di algoritmo ml da personalizzare per adattarsi a specifici problemi di sicurezza nell'organizzazione. 

- notebook di esempio per eseguire il training del modello e pianificare l'assegnazione dei punteggi al modello. 

Oltre a tutto questo, è possibile usare i propri modelli di Machine Learning e/o il proprio ambiente Spark per l'integrazione con Azure Sentinel.

Con la piattaforma BYO-ML è possibile iniziare rapidamente a creare modelli di Machine Learning personalizzati: 

- Il notebook con dati di esempio consente di ottenere un'esperienza utente end-to-end senza doversi preoccupare della gestione dei dati di produzione.

- Il pacchetto integrato con l'ambiente Spark riduce le sfide e i problemi di gestione dell'infrastruttura.

- Le librerie supportano gli spostamenti di dati. I notebook di training e assegnazione dei punteggi illustrano l'esperienza end-to-end e fungono da modello da adattare all'ambiente.

### <a name="use-cases"></a>Casi d'uso
 
La piattaforma e il pacchetto BYO-ML riducono significativamente il tempo e l'impegno necessari per creare rilevamenti di Machine Learning personalizzati e sfruttano la capacità di risolvere problemi di sicurezza specifici in Azure Sentinel. La piattaforma supporta i casi d'uso seguenti:

**Eseguire il training di un algoritmo di Machine Learning per ottenere un modello personalizzato:** È possibile usare un algoritmo di Machine Learning esistente (condiviso da Microsoft o dalla community degli utenti) ed eseguire facilmente il training sui propri dati per ottenere un modello di Machine Learning personalizzato più adatto ai dati e all'ambiente.

**Modificare un modello di algoritmo di Machine Learning per ottenere un modello personalizzato:** È possibile modificare un modello di algoritmo di Machine Learning (condiviso da Microsoft o dalla community degli utenti) ed eseguire il training dell'algoritmo modificato sui propri dati, in modo da derivare un modello personalizzato in base al problema specifico.

**Creare un modello personalizzato:** Creare un modello personalizzato da zero usando la Azure Sentinel e le utilità BYO-ML di Azure Sentinel.

**Integrare l'ambiente Databricks/Spark:** Integrare l'ambiente Databricks/Spark esistente in Azure Sentinel e usare librerie e modelli BYO-ML per creare modelli di Machine Learning in situazioni specifiche.

**Importare il proprio modello di Machine Learning:** È possibile importare i propri modelli di Machine Learning e usare la piattaforma e le utilità BYO-ML per integrarli con Azure Sentinel.

**Condividere un algoritmo di Machine Learning:** Condividere un algoritmo di Machine Learning che la community deve adottare e adattare.

**Usare ML per migliorare SecOps:** usare il proprio modello di Machine Learning personalizzato e i risultati per la ricerca, i rilevamenti, l'analisi e la risposta.

Questo articolo illustra i componenti della piattaforma BYO-ML e come sfruttare la piattaforma e l'algoritmo Anomalous Resource Access per offrire un rilevamento di MACHINE personalizzato con Azure Sentinel.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark Environment

[Apache Spark™](http://spark.apache.org/) fatto un passo avanti nella semplificazione dei Big Data fornendo un framework unificato per la creazione di pipeline di dati. Azure Databricks offre una piattaforma cloud a gestione zero basata su Spark. È consigliabile usare Databricks per la piattaforma BYO-ML, in modo da concentrarsi sulla ricerca di risposte che influiscono immediatamente sull'azienda, anziché affrontare le pipeline di dati e i problemi della piattaforma.
Se si ha già Databricks o qualsiasi altro ambiente Spark e si preferisce usare la configurazione esistente, anche il pacchetto BYO-ML funzionerà correttamente. 

## <a name="byo-ml-package"></a>Pacchetto BYO-ML

Il pacchetto BYO ML include le procedure consigliate e la ricerca di Microsoft nel front-end di ML per la sicurezza. In questo pacchetto viene fornito l'elenco seguente di utilità, notebook e modelli di algoritmo per i problemi di sicurezza.

| Nome file | Descrizione |
| --------- | ----------- |
| azure_sentinel_utilities.whl | Contiene utilità per la lettura dei BLOB da Azure e la scrittura in Log Analytics. |
| AnomalousRASampleData | Notebook illustra l'uso del modello Anomalous Resource Access Azure Sentinel con dati di esempio di training e test generati. |
| AnomalousRATraining.ipynb | Notebook per eseguire il training dell'algoritmo, compilare e salvare i modelli. |
| AnomalousRAScoring.ipynb | Notebook per pianificare l'esecuzione del modello, visualizzare il risultato e scrivere nuovamente il punteggio Azure Sentinel. |
|

Il primo modello di algoritmo ml offerto è per il [rilevamento anomalo dell'accesso alle risorse.](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML) Si basa su un algoritmo di filtro collaborativo ed è stato addestrato con i log di accesso alla condivisione file di Windows (Eventi di sicurezza con ID evento 5140). Le informazioni chiave necessarie per questo modello nel log sono l'associazione di utenti e risorse a cui si accede. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Procedura dettagliata di esempio: Rilevamento anomalo dell'accesso alla condivisione file 

Ora che si ha familiarità con i componenti chiave della piattaforma BYO-ML, ecco un esempio per illustrare come usare la piattaforma e i componenti per fornire un rilevamento di Machine Learning personalizzato.

### <a name="setup-the-databricksspark-environment"></a>Configurare l'ambiente Databricks/Spark

Se non si ha già un ambiente Databricks, sarà necessario configurarne uno. Per istruzioni, vedere il documento [di avvio rapido di Databricks.](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal)

### <a name="auto-export-instruction"></a>Istruzione di esportazione automatica

Per creare modelli di Machine Learning personalizzati in base ai propri dati in Azure Sentinel, è necessario esportare i dati da Log Analytics in una risorsa di archiviazione BLOB o hub eventi, in modo che il modello di Machine Learning possa accedervi da Databricks. Informazioni su come [inserire dati in Azure Sentinel](connect-data-sources.md).

Per questo esempio, è necessario avere i dati di training per il log di accesso alla condivisione file nell'archivio BLOB di Azure. Il formato dei dati è documentato nel notebook e nelle librerie.

È possibile esportare automaticamente i dati da Log Analytics usando l'interfaccia della riga di comando [di Azure.](/cli/azure/monitor/log-analytics) 

Per eseguire i  comandi, è necessario avere il ruolo Collaboratore nell'area di lavoro Log Analytics, nell'account di archiviazione e nella risorsa EventHub. 

Ecco un set di comandi di esempio per configurare l'esportazione automatica:

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>Esportare dati personalizzati

Per i dati personalizzati non supportati dall'esportazione automatica di Log Analytics, è possibile usare l'app per la logica o altre soluzioni per spostare i dati. È possibile fare riferimento al blog e allo script [Exporting Log Analytics Data to Blob Store](https://techcommunity.microsoft.com/t5/azure-monitor/log-analytics-data-export-preview/ba-p/1783530) (Esportazione di dati di Log Analytics nell'archivio BLOB).

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Correlare i dati all'esterno Azure Sentinel

È anche possibile portare i dati dall'Azure Sentinel all'archiviazione BLOB o all'hub eventi e correlarli con i dati Azure Sentinel per compilare i modelli di Machine Learning. 
 
### <a name="copy-and-install-the-related-packages"></a>Copiare e installare i pacchetti correlati

Copiare il pacchetto BYO-ML dal repository GitHub Azure Sentinel indicato in precedenza nell'ambiente Databricks. Aprire quindi i notebook e seguire le istruzioni all'interno del notebook per installare le librerie necessarie nei cluster.

### <a name="model-training-and-scoring"></a>Training del modello e assegnazione dei punteggi

Seguire le istruzioni nei due notebook per modificare le configurazioni in base all'ambiente e alle risorse, seguire i passaggi per eseguire il training e compilare il modello, quindi pianificare il modello per il punteggio dei log di accesso alla condivisione file in ingresso.

### <a name="write-results-to-log-analytics"></a>Scrivere risultati in Log Analytics

Dopo aver pianificato l'assegnazione dei punteggi, è possibile usare il modulo nel notebook di assegnazione dei punteggi per scrivere i risultati del punteggio nell'area di lavoro Log Analytics associata all'istanza Azure Sentinel dati.

### <a name="check-results-in-azure-sentinel"></a>Controllare i risultati in Azure Sentinel

Per visualizzare i risultati con punteggio insieme ai dettagli del log correlati, tornare al portale Azure Sentinel dati. In **Log** > log personalizzati verranno visualizzati i  risultati nella tabella AnomalousResourceAccessResult_CL (o nel nome della tabella personalizzata). È possibile usare questi risultati per migliorare le esperienze di analisi e ricerca.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="log di accesso alle risorse anomasi":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Creare una regola di analisi personalizzata con i risultati di Machine Learning

Dopo aver confermato che i risultati di Ml sono nella tabella dei log personalizzati e si è soddisfatti della fedeltà dei punteggi, è possibile creare un rilevamento in base ai risultati. Passare ad **Analytics** dal portale Azure Sentinel e [creare una nuova regola di rilevamento.](tutorial-detect-threats-custom.md) Di seguito è riportato un esempio che mostra la query usata per creare il rilevamento.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="creare una regola di analisi personalizzata per i rilevamenti B Y O M L":::

### <a name="view-and-respond-to-incidents"></a>Visualizzare e rispondere agli eventi imprevisti
Dopo aver configurato la regola di analisi in base ai risultati di Machine Learning, se sono presenti risultati superiori  alla soglia impostata nella query, verrà generato un evento imprevisto e verrà visualizzato nella pagina Eventi imprevisti in Azure Sentinel. 

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare la piattaforma BYO-ML di Azure Sentinel per creare o importare algoritmi di Machine Learning personalizzati per analizzare i dati e rilevare le minacce.

- Vedere i post su Machine Learning e molti altri argomenti rilevanti [nel blog Azure Sentinel .](https://aka.ms/azuresentinelblog)
