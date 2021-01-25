---
title: Informazioni sui servizi cloud di Azure (supporto esteso)
description: Informazioni sugli elementi figlio dell'elemento di configurazione di rete del file di configurazione del servizio, che specifica la rete virtuale e i valori DNS.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 969f60cd92e8c1cbe93f1646cccd08c942ad9923
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762835"
---
# <a name="about-azure-cloud-services-extended-support"></a>Informazioni sui servizi cloud di Azure (supporto esteso)

> [!IMPORTANT]
> Servizi cloud (supporto esteso) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Servizi cloud (supporto esteso) è un nuovo modello di distribuzione basato su [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) per il prodotto [servizi cloud di Azure](https://azure.microsoft.com/services/cloud-services/) ed è attualmente disponibile in anteprima pubblica. Servizi cloud (supporto esteso) offre il vantaggio principale di fornire resilienza a livello di area insieme alla parità di funzionalità con i servizi cloud di Azure distribuiti con Azure Service Manager. Offre anche alcune funzionalità ARM, ad esempio controllo degli accessi in base al ruolo e controllo (RBAC), tag, criteri e supporta i modelli di distribuzione.  

Con questa modifica, il modello di distribuzione basato su Azure Service Manager per i servizi cloud verrà rinominato [servizi cloud (versione classica)](../cloud-services/cloud-services-choose-me.md). Si manterrà la possibilità di creare e distribuire rapidamente le applicazioni e i servizi Web e cloud. Potrai ridimensionare l'infrastruttura dei servizi cloud in base alla richiesta corrente e garantire che le prestazioni delle tue applicazioni possano restare al passo con la riduzione simultanea dei costi.  

## <a name="what-does-not-change"></a>Cosa non cambia 
- È possibile creare il codice, definire le configurazioni e distribuirlo in Azure. Azure configura l'ambiente di calcolo, esegue il codice, quindi lo monitora e lo gestisce.
- I servizi cloud (supporto esteso) supportano anche due tipi di ruoli, [Web e di lavoro](../cloud-services/cloud-services-choose-me.md). Non sono state apportate modifiche alla progettazione, all'architettura o ai componenti dei ruoli Web e di lavoro. 
- I tre componenti di un servizio cloud, la definizione del servizio (con estensione csdef), la configurazione del servizio (. cscfg) e il pacchetto del servizio (. cspkg) vengono portati avanti e non vi sono modifiche nei rispettivi [formati](cloud-services-model-and-package.md). 
- Non è necessario apportare modifiche al codice di runtime perché il piano dati è lo stesso e il piano di controllo sta cambiando. 
- Le versioni Guest di Azure e gli aggiornamenti associati sono allineati ai servizi cloud (versione classica)
- Processo di aggiornamento sottostante per quanto riguarda i domini di aggiornamento, il modo in cui l'aggiornamento procede, il rollback e le modifiche al servizio consentite durante un aggiornamento non cambiano

## <a name="changes-in-deployment-model"></a>Modifiche nel modello di distribuzione

Le modifiche minime sono necessarie per la configurazione del servizio (. cscfg) e i file di definizione del servizio (. csdef) per distribuire i servizi cloud (supporto esteso). Per il codice di runtime non sono necessarie modifiche. Tuttavia, gli script di distribuzione dovranno essere aggiornati per chiamare le nuove API basate su Azure Resource Manager. 

:::image type="content" source="media/overview-image-1.png" alt-text="Image Mostra la configurazione del servizio cloud classico con l'aggiunta della sezione template. ":::

Le principali differenze tra i servizi cloud (versione classica) e i servizi cloud (supporto esteso) rispetto alla distribuzione sono: 

- Azure Resource Manager le distribuzioni usano [modelli ARM](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) , ovvero un file JavaScript Object Notation (JSON) che definisce l'infrastruttura e la configurazione per il progetto. Il modello usa una sintassi dichiarativa che consente di indicare ciò che si intende distribuire senza dover scrivere la sequenza dei comandi di programmazione per crearlo. La configurazione del servizio e il file di definizione del servizio devono essere coerenti con il [modello ARM](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) durante la distribuzione di servizi cloud (supporto esteso). Questa operazione può essere eseguita [creando manualmente il modello ARM](deploy-template.md) o usando [PowerShell](deploy-powershell.md), il [portale](deploy-portal.md) e [Visual Studio](deploy-visual-studio.md).  

- I clienti devono usare [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) per [gestire i certificati nei servizi cloud (supporto esteso)](certificates-and-key-vault.md). Azure Key Vault ti permette di archiviare e gestire in modo sicuro le credenziali dell'applicazione, ad esempio segreti, chiavi e certificati, in un repository Cloud centralizzato e sicuro. Le applicazioni possono eseguire l'autenticazione a Key Vault in fase di esecuzione per recuperare le credenziali. 

- Tutte le risorse distribuite tramite il [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) devono trovarsi all'interno di una rete virtuale. Le reti virtuali e le subnet vengono create in Azure Resource Manager usando le API di Azure Resource Manager esistenti ed è necessario fare riferimento all'interno della sezione NetworkConfiguration del. cscfg quando si distribuiscono servizi cloud (supporto esteso).   

- Ogni servizio cloud (supporto esteso) è una singola distribuzione indipendente. Servizi cloud (supporto esteso) non supporta più slot in un singolo servizio cloud.  
    - La <sup>*</sup> funzionalità di scambio di indirizzi VIP può essere usata per scambiare tra due servizi cloud (supporto esteso). Per eseguire il test e la gestione temporanea di una nuova versione di un servizio cloud, distribuire un servizio cloud (supporto esteso) e contrassegnarlo come indirizzo VIP scambiabile con un altro servizio cloud (supporto esteso)  

- L'etichetta Domain Name Service (DNS) è facoltativa per un servizio cloud (supporto esteso). In Azure Resource Manager, l'etichetta DNS è una proprietà della risorsa IP pubblica associata al servizio cloud. 


<sup>*</sup> Lo scambio di indirizzi VIP per i servizi cloud (supporto esteso) non è disponibile durante l'anteprima pubblica.  

## <a name="migration-to-azure-resource-manager"></a>Migrazione a Azure Resource Manager

Servizi cloud (supporto esteso) fornisce due percorsi per la migrazione da [Service Manager di Azure](https://docs.microsoft.com/powershell/azure/servicemanagement/overview?view=azuresmps-4.0.0&preserve-view=true ) a [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview). 
1) I clienti distribuiscono i servizi cloud direttamente in Azure Resource Manager e quindi eliminano il servizio cloud precedente in Azure Service Manager. 
2) La migrazione sul posto supporta la possibilità di eseguire la migrazione dei servizi cloud (versione classica) con un tempo di inattività minimo a servizi cloud (supporto esteso). 

### <a name="additional-migration-options"></a>Opzioni di migrazione aggiuntive

Quando si valutano i piani di migrazione dai servizi cloud (versione classica) ai servizi cloud (supporto esteso), è consigliabile esaminare altri servizi di Azure, ad esempio i [set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview), il [servizio app](https://docs.microsoft.com/azure/app-service/overview), il [servizio Azure Kubernetes](https://docs.microsoft.com/azure/aks/intro-kubernetes)e [Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview). Questi servizi continueranno a presentare funzionalità aggiuntive, mentre i servizi cloud (supporto esteso) manterranno principalmente la parità di funzionalità con i servizi cloud (versione classica). 

A seconda dell'applicazione, è possibile che i servizi cloud (supporto esteso) richiedano un impegno sostanzialmente inferiore a Azure Resource Manager rispetto ad altre opzioni. Se l'applicazione non è in continua evoluzione, servizi cloud (supporto esteso) è un'opzione valida da considerare poiché fornisce un percorso di migrazione rapido. Viceversa, se l'applicazione è in continua evoluzione e necessita di un set di funzionalità più moderno, esplorare gli altri servizi di Azure per soddisfare al meglio i requisiti attuali e futuri. 

## <a name="next-steps"></a>Passaggi successivi
- Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
