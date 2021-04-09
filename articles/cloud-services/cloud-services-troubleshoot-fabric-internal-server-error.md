---
title: Risolvere i problemi di FabricInternalServerError o ServiceAllocationFailure quando si distribuisce un servizio cloud (versione classica) in Azure | Microsoft Docs
description: Questo articolo illustra come risolvere un'eccezione FabricInternalServerError o ServiceAllocationFailure quando si distribuisce un servizio cloud (versione classica) in Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 0a4111f569b751ace80a2a886ed2ce7e4968ce16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101745726"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>Risolvere i problemi di FabricInternalServerError o ServiceAllocationFailure quando si distribuisce un servizio cloud (versione classica) in Azure

Questo articolo illustra come risolvere gli errori di allocazione in cui non è possibile allocare il controller di infrastruttura quando si distribuisce un servizio cloud di Azure (versione classica).

Quando si distribuiscono istanze a un servizio Cloud o si aggiungono nuove istanze del ruolo di lavoro o web, Microsoft Azure alloca le risorse di calcolo.

Occasionalmente è possibile che si verifichino errori durante queste operazioni anche prima di raggiungere il limite della sottoscrizione di Azure.

> [!TIP]
> Queste informazioni potrebbero risultare utili anche quando si pianifica la distribuzione dei servizi.

## <a name="symptom"></a>Sintomo

In portale di Azure passare al servizio cloud (versione classica) e nella barra laterale selezionare *log operazioni (versione classica)* per visualizzare i log.

![Image Mostra il pannello del log delle operazioni (classico).](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

Quando si esaminano i log del servizio cloud (versione classica), verrà visualizzata l'eccezione seguente:

|Eccezione  |Messaggio di errore  |
|---------|---------|
FabricInternalServerError     |L'operazione non è riuscita con codice di errore ' InternalError ' e errorMessage ' si è verificato un errore interno del server. Ripetere la richiesta '.|
|ServiceAllocationFailure     |L'operazione non è riuscita con codice di errore ' InternalError ' e errorMessage ' si è verificato un errore interno del server. Ripetere la richiesta '.|

## <a name="cause"></a>Causa

*FabricInternalServerError* e *ServiceAllocationFailure* sono eccezioni che possono verificarsi quando il controller di infrastruttura non riesce ad allocare le istanze nel cluster. La causa principale varia se il servizio cloud è **bloccato** o **non bloccato**.

- [**Non bloccato:** Errori dalla prima distribuzione di un nuovo servizio cloud](#not-pinned-to-a-cluster)
- [**Aggiunto:** Errori dei servizi cloud esistenti](#pinned-to-a-cluster)

> [!NOTE]
> Quando la prima istanza viene distribuita in un servizio cloud (in gestione temporanea o in produzione), il servizio cloud viene aggiunto a un cluster.
>
> Nel tempo, le risorse in questo pool di risorse potrebbero essere completamente utilizzate. Se un servizio cloud esegue una richiesta di allocazione per risorse aggiuntive quando nel pool di risorse aggiunto sono disponibili risorse insufficienti, la richiesta comporterà un [errore di allocazione](cloud-services-allocation-failures.md).

## <a name="solution"></a>Soluzione

Seguire le istruzioni per gli errori di allocazione negli scenari seguenti.

### <a name="not-pinned-to-a-cluster"></a>Non aggiunto a un cluster

La prima volta che si distribuisce un servizio cloud (versione classica), il cluster non è ancora stato selezionato, quindi il servizio cloud non è *bloccato*. Azure potrebbe avere un errore di distribuzione perché:

- È stata selezionata una dimensione specifica che non è disponibile nell'area.
- La combinazione di dimensioni necessarie in ruoli diversi non è disponibile nell'area.

Quando si verifica un errore di allocazione in questo scenario, la procedura consigliata consiste nel controllare le dimensioni disponibili nell'area e modificare le dimensioni specificate in precedenza.

1. È possibile controllare le dimensioni disponibili in un'area nella pagina dei [prodotti servizio cloud (versione classica)](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) .

    > [!NOTE]
    > Nella pagina *prodotti* non verrà visualizzata la capacità disponibile. Per tutte le nuove allocazioni, Azure dovrebbe essere in grado di selezionare il cluster ottimale nella propria area geografica in un determinato momento.

1. Aggiornare il file di definizione del servizio per il servizio cloud (versione classica) per specificare [dimensioni del prodotto](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) diverse dall'area geografica.

### <a name="pinned-to-a-cluster"></a>Aggiunto a un cluster

I servizi cloud esistenti vengono *aggiunti* a un cluster. Tutte le altre distribuzioni per il servizio cloud (versione classica) si verificheranno nello stesso cluster.

Quando si verifica un errore di allocazione in questo scenario, la procedura consigliata consiste nel ridistribuire in un nuovo servizio cloud (versione classica) e aggiornare il *record CNAME*.

> [!TIP]
> Questa soluzione potrebbe essere la più efficace in quanto consente alla piattaforma di scegliere tra tutti i cluster in tale area.

> [!NOTE]
> Questa soluzione non deve causare tempi di inattività.

1. Distribuire il carico di lavoro in un nuovo servizio cloud (versione classica).
    - Per altre istruzioni, vedere la Guida [come creare e distribuire un servizio cloud (classico)](cloud-services-how-to-create-deploy-portal.md) .

    > [!WARNING]
    > Se non si vuole perdere l'indirizzo IP associato a questo slot di distribuzione, è possibile usare [la soluzione 3: mantenere l'indirizzo IP](cloud-services-allocation-failures.md#solutions).

1. Aggiornare il record *CNAME* o *un* record per indirizzare il traffico al nuovo servizio cloud (versione classica).
    - Per altre istruzioni, vedere la Guida [configurazione di un nome di dominio personalizzato per un servizio cloud di Azure (versione classica)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) .

1. Una volta che il traffico verso il vecchio sito è zero, è possibile eliminare il servizio cloud precedente (versione classica).
    - Per altre istruzioni, vedere la Guida [eliminare le distribuzioni e un servizio cloud (classico)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) .
    - Per visualizzare il traffico di rete nel servizio cloud (versione classica), vedere [Introduzione al monitoraggio del servizio cloud (versione classica)](cloud-services-how-to-monitor.md).

Vedere [risoluzione dei problemi relativi a errori di allocazione del servizio cloud (versione classica) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) per ulteriori passaggi di correzione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori soluzioni di errore di allocazione e informazioni complementari:

> [!div class="nextstepaction"]
> [Errori di allocazione-servizio cloud (versione classica)](cloud-services-allocation-failures.md)

Se il problema di Azure non viene risolto in questo articolo, visitare i forum di Azure su [MSDN e stack overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in [@AzureSupport su Twitter](https://twitter.com/AzureSupport). È anche possibile inviare una richiesta di supporto tecnico di Azure. Per inviare una richiesta di supporto tecnico, nella pagina [Supporto tecnico di Azure](https://azure.microsoft.com/support/options/) selezionare *Supporto*.
