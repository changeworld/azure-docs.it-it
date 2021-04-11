---
title: Dettagli tecnici e requisiti per la migrazione ai servizi cloud di Azure (supporto esteso)
description: Fornisce dettagli tecnici e requisiti per la migrazione da servizi cloud di Azure (versione classica) a servizi cloud di Azure (supporto esteso)
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 4ff7d9aa2075b675a7ecd979c08d5621bbdd831a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286909"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Dettagli tecnici sulla migrazione ai servizi cloud di Azure (supporto esteso)   

In questo articolo vengono illustrati i dettagli tecnici relativi allo strumento di migrazione per quanto riguarda i servizi cloud (versione classica). 

> [!IMPORTANT]
> La migrazione da servizi cloud (versione classica) a servizi cloud (supporto esteso) con lo strumento di migrazione è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="details-about-feature--scenarios-supported-for-migration"></a>Informazioni dettagliate su funzionalità/scenari supportati per la migrazione 

### <a name="extensions-and-plugin-migration"></a>Estensioni e migrazione di plug-in 
- Verrà eseguita la migrazione di tutte le estensioni abilitate e supportate. 
- Non verrà eseguita la migrazione delle estensioni disabilitate. 
- I plug-in sono un concetto legacy e devono essere rimossi prima della migrazione. Sono supportati per la migrazione e, dopo la migrazione, se è necessario abilitare l'estensione, è necessario rimuovere il plug-in prima di installare l'estensione. I plug-in di desktop remoto e le estensioni sono molto interessati da questa operazione.   
 
### <a name="certificate-migration"></a>Migrazione di certificati
- Nei servizi cloud (supporto esteso) i certificati vengono archiviati in un Key Vault. Come parte della migrazione, viene creato un Key Vault per i clienti che hanno il nome del servizio cloud e trasferiscono tutti i certificati da Azure Service Manager a Key Vault. 
- Il riferimento a questo Key Vault è specificato nel modello o passato tramite PowerShell o l'interfaccia della riga di comando di Azure. 

### <a name="service-configuration-and-service-definition-files"></a>Configurazione del servizio e file di definizione del servizio
- I file con estensione cscfg e csdef devono essere aggiornati per i servizi cloud (supporto esteso) con modifiche minime. 
- I nomi delle risorse, ad esempio la rete virtuale e lo SKU della macchina virtuale, sono diversi. Vedere [traduzione di risorse e convenzione di denominazione post-migrazione](#translation-of-resources-and-naming-convention-post-migration)
- I clienti possono recuperare le nuove distribuzioni tramite [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) e l' [API REST](https://docs.microsoft.com/rest/api/compute/cloudservices/get). 

### <a name="cloud-service-and-deployments"></a>Servizio cloud e distribuzioni
- Ogni distribuzione di servizi cloud (supporto esteso) è un servizio cloud indipendente. La distribuzione non è più raggruppata in un servizio cloud usando slot.
- Se si dispone di due slot nel servizio cloud (versione classica), è necessario eliminare uno slot (gestione temporanea) e usare lo strumento di migrazione per spostare l'altro slot di produzione in Azure Resource Manager. 
- L'indirizzo IP pubblico nella distribuzione del servizio cloud rimane invariato dopo la migrazione a Azure Resource Manager ed è esposto come risorsa IP SKU (dinamica o statica) di base. 
- Il nome DNS e il dominio (cloudapp.azure.net) per il servizio cloud migrato restano invariati. 

### <a name="virtual-network-migration"></a>Migrazione della rete virtuale
- Se una distribuzione di servizi cloud si trova in una rete virtuale, durante la migrazione verranno migrati tutti i servizi cloud e le risorse di rete virtuale associate. 
- Dopo la migrazione, la rete virtuale viene posizionata in un gruppo di risorse diverso rispetto al servizio cloud. 
- Per le reti virtuali con più servizi cloud, viene eseguita la migrazione di ogni servizio cloud uno dopo l'altro. 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>Migrazione di distribuzioni non in una rete virtuale
- In 2017 Azure ha avviato automaticamente la creazione di nuove distribuzioni (senza la rete virtuale specificata dal cliente) in una piattaforma virtuale "predefinita" creata. Queste reti virtuali predefinite sono nascoste ai clienti.   
- Come parte della migrazione, questa rete virtuale predefinita verrà esposta ai clienti una sola volta in Azure Resource Manager. Per gestire o aggiornare la distribuzione in Azure Resource Manager, i clienti devono aggiungere le informazioni sulla rete virtuale nella sezione NetworkConfiguration del file. cscfg.    
- La rete virtuale predefinita, quando viene eseguita la migrazione a Azure Resource Manager, viene inserita nello stesso gruppo di risorse del servizio cloud.
- I servizi cloud creati prima di questa ora non si troveranno in nessuna rete virtuale e non possono essere migrati con lo strumento. Provare a ridistribuire questi servizi cloud direttamente in Azure Resource Manager. 
- Per verificare se una distribuzione è idonea per la migrazione, eseguire l'API Validate nella distribuzione. Il risultato dell'API Validate conterrà il messaggio di errore che indica in modo esplicito se questa distribuzione è idonea per la migrazione.     

### <a name="load-balancer"></a>Load Balancer   
- Per un servizio cloud che usa un endpoint pubblico, un servizio di bilanciamento del carico creato dalla piattaforma associato al servizio cloud viene esposto all'interno della sottoscrizione del cliente in Azure Resource Manager. Il servizio di bilanciamento del carico è una risorsa di sola lettura e gli aggiornamenti sono limitati solo tramite i file di configurazione del servizio (. cscfg) e di definizione del servizio (con estensione csdef). 

### <a name="key-vault"></a>Key Vault
- Come parte della migrazione, Azure crea automaticamente una nuova Key Vault e ne esegue la migrazione di tutti i certificati. Lo strumento non consente di utilizzare un Key Vault esistente. 
- I servizi cloud (supporto esteso) richiedono un Key Vault che si trova nella stessa area e nella stessa sottoscrizione. Questo Key Vault viene creato automaticamente come parte della migrazione. 


## <a name="translation-of-resources-and-naming-convention-post-migration"></a>Traduzione delle risorse e della convenzione di denominazione post-migrazione
Come parte della migrazione, i nomi delle risorse vengono modificati e alcune funzionalità dei servizi cloud sono esposte come risorse Azure Resource Manager. La tabella riepiloga le modifiche specifiche per la migrazione dei servizi cloud.

| Servizi cloud (versione classica) <br><br> Nome risorsa | Servizi cloud (versione classica) <br><br> Sintassi| Servizi cloud (supporto esteso) <br><br> Nome risorsa| Servizi cloud (supporto esteso) <br><br> Sintassi | 
|---|---|---|---|
| Servizio cloud | `cloudservicename` | Non associato| Non associato |
| Distribuzione (portale creato) <br><br> Distribuzione (non creato dal portale)  | `deploymentname` | Servizi cloud (supporto esteso) | `deploymentname` |  
| Rete virtuale | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> Non associato |  Rete virtuale (non creato dal portale) <br><br> Rete virtuale (portale creato) <br><br> Reti virtuali (impostazione predefinita) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `DefaultRdfevirtualnetwork_vnetid`|
| Non associato | Non associato | Key Vault | `cloudservicename` | 
| Non associato | Non associato | Gruppo di risorse per le distribuzioni di servizi cloud | `cloudservicename-migrated` | 
| Non associato | Non associato | Gruppo di risorse per la rete virtuale | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| Non associato | Non associato | IP pubblico (dinamico) | `cloudservicenameContractContract` | 
| Nome IP riservato | `reservedipname` | IP riservato (non creato dal portale) <br><br> IP riservato (portale creato) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| Non associato| Non associato | Load Balancer | `deploymentname-lb`|



## <a name="migration-issues-and-how-to-handle-them"></a>Problemi di migrazione e come gestirli. 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>Migrazione bloccata in un'operazione da molto tempo. 
- Il commit, la preparazione e l'interruzione possono richiedere molto tempo a seconda del numero di distribuzioni. Il timeout delle operazioni avverrà dopo 24 ore.   
- Le operazioni di commit, preparazione e interruzione sono idempotente. La maggior parte dei problemi è risolvibile tramite un nuovo tentativo. Potrebbero verificarsi errori temporanei, che possono essere rilevati in pochi minuti, ma è consigliabile riprovare dopo un intervallo. Se la migrazione tramite il portale di Azure e l'operazione è bloccata in uno stato "in corso", usare PowerShell per ripetere l'operazione. 
- Contattare il supporto tecnico per eseguire la migrazione o eseguire il rollback della distribuzione dal back-end. 

### <a name="migration-failed-in-an-operation"></a>Migrazione non riuscita in un'operazione. 
- Se la convalida ha esito negativo, il motivo è che la distribuzione o la rete virtuale contiene uno scenario, una funzionalità o una risorsa non supportata. Utilizzare l'elenco degli scenari non supportati per trovare la soluzione più ovvia ai documenti.  
- L'operazione di preparazione esegue innanzitutto la convalida, incluse alcune convalide dispendiose (non analizzate in Validate). L'errore di preparazione potrebbe essere dovuto a uno scenario non supportato. Trovare lo scenario e la soluzione più ovvia ai documenti pubblici. È necessario chiamare Abort per tornare allo stato originale e sbloccare la distribuzione per gli aggiornamenti e le operazioni di eliminazione.
- Se Abort non è riuscito, ripetere l'operazione. Se i tentativi hanno esito negativo, contattare il supporto tecnico.
- Se il commit non è riuscito, ripetere l'operazione. Se il tentativo ha esito negativo, contattare il supporto tecnico. Anche in caso di errore di commit, non ci sono problemi del piano dati per la distribuzione. La distribuzione deve essere in grado di gestire il traffico dei clienti senza alcun problema. 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>Il portale è stato aggiornato dopo la preparazione. L'esperienza è stata riavviata e il commit o l'interruzione non è più visibile. 
- Il portale archivia le informazioni di migrazione localmente e, di conseguenza, dopo l'aggiornamento, inizia dalla fase Validate anche se il servizio cloud si trova nella fase di preparazione.  
- È possibile utilizzare il portale per eseguire nuovamente la convalida e preparare i passaggi per esporre il pulsante Interrompi e Esegui commit. Non provocherà alcun errore.
- I clienti possono usare PowerShell o l'API REST per interrompere o eseguire il commit. 

### <a name="how-much-time-can-the-operations-takebr"></a>Quanto tempo è possibile eseguire le operazioni?<br>
Validate è progettato per essere rapido. L'esecuzione della preparazione è più lunga e richiede un certo tempo a seconda del numero totale di istanze del ruolo di cui viene eseguita la migrazione. Abort e commit possono anche richiedere tempo, ma è necessario meno tempo rispetto alla preparazione. Tutte le operazioni si timeout dopo 24 ore. 
