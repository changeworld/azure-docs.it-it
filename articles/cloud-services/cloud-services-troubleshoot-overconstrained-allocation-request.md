---
title: Risolvere i problemi di OverconstrainedAllocationRequest durante la distribuzione di un servizio cloud (versione classica) in Azure | Microsoft Docs
description: Questo articolo illustra come risolvere un'eccezione OverconstrainedAllocationRequest durante la distribuzione di un servizio cloud (versione classica) in Azure.
services: cloud-services
documentationcenter: ''
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 1a5880107aaa414da42fe5e36e0cb3315071d8a0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877427"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>Risolvere i problemi relativi a OverconstrainedAllocationRequest durante la distribuzione di servizi cloud (versione classica) in Azure

In questo articolo verrà illustrata la risoluzione degli errori di allocazione vincolata che impediscono la distribuzione di Servizi cloud di Azure (versione classica).

Quando si distribuiscono istanze a un servizio Cloud o si aggiungono nuove istanze del ruolo di lavoro o web, Microsoft Azure alloca le risorse di calcolo.

In alcuni casi possono verificarsi errori durante queste operazioni, anche prima di raggiungere il limite della sottoscrizione di Azure.

> [!TIP]
> Queste informazioni potrebbero risultare utili anche quando si pianifica la distribuzione dei servizi.

## <a name="symptom"></a>Sintomo

![L'immagine mostra il pannello Log operazioni (versione classica).](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|Tipo di eccezione  |Messaggio di errore  |
|---------|---------|
|OverconstrainedAllocationRequest |Non è possibile effettuare il provisioning delle dimensioni della macchina virtuale (o combinazione di dimensioni di VM) richieste da questa distribuzione a causa di vincoli della richiesta di distribuzione. Se possibile, provare a eliminare i vincoli, ad esempio le associazioni di rete virtuale, la distribuzione in un servizio ospitato senza altre distribuzioni e in un gruppo di affinità diverso o senza gruppi di affinità oppure provare a eseguire la distribuzione in un'area diversa.|

## <a name="cause"></a>Causa

La causa radice varia se il servizio cloud **è stato aggiunto** o **meno.**

- [**Non bloccato:** Errori di una prima distribuzione di un nuovo servizio cloud (versione classica)](#not-pinned-to-a-cluster)
- [**Elementi aggiunti:** Errori da un servizio cloud esistente (versione classica)](#pinned-to-a-cluster)

> [!NOTE]
> Quando la prima istanza viene distribuita in un servizio cloud (in gestione temporanea o in produzione), tale servizio cloud viene aggiunto a un cluster.
>
> Nel corso del tempo, le risorse nel cluster possono diventare completamente utilizzate. Se un servizio cloud (versione classica) effettua una richiesta di allocazione per risorse aggiuntive quando sono disponibili risorse insufficienti nel cluster bloccato, la richiesta comporterà un errore [di allocazione.](cloud-services-allocation-failures.md)

## <a name="solution"></a>Soluzione

Seguire le indicazioni per gli errori di allocazione negli scenari seguenti.

### <a name="not-pinned-to-a-cluster"></a>Non aggiunto a un cluster

La prima volta che si distribuisce un servizio cloud (versione classica), il cluster non è ancora stato selezionato, quindi il servizio cloud non *è stato aggiunto.* Azure potrebbe avere un errore di distribuzione perché:

- È stata selezionata una dimensione specifica che non è disponibile nell'area.
- La combinazione di dimensioni necessarie per ruoli diversi non è disponibile nell'area.

Quando si verifica un errore di allocazione in questo scenario, è consigliabile controllare le dimensioni disponibili nell'area e modificare le dimensioni specificate in precedenza.

1. È possibile controllare le dimensioni disponibili in un'area nella [pagina Prodotti del servizio cloud (versione](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) classica).

    > [!NOTE]
    > La *pagina* Prodotti non mostra la capacità disponibile. Per qualsiasi nuova allocazione, Azure dovrebbe essere in grado di scegliere il cluster ottimale nell'area in quel momento.

1. Aggiornare il file di definizione del servizio cloud (versione classica) per specificare dimensioni [del prodotto diverse](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) rispetto all'area.

### <a name="pinned-to-a-cluster"></a>Aggiunta a un cluster

I servizi cloud esistenti *vengono aggiunti* a un cluster. Eventuali altre distribuzioni per il servizio cloud (versione classica) verranno evase nello stesso cluster.

Quando si verifica un errore di allocazione in questo scenario, è consigliabile ridistribuire in un nuovo servizio cloud (versione classica) e aggiornare *CNAME*.

> [!TIP]
> Questa soluzione potrebbe essere la più efficace in quanto consente alla piattaforma di scegliere tra tutti i cluster in tale area.

> [!NOTE]
> Questa soluzione non deve causare tempi di inattività.

1. Distribuire il carico di lavoro in un nuovo servizio cloud (versione classica).
    - Per altre istruzioni, vedere la guida Come creare e distribuire un servizio [cloud (versione](cloud-services-how-to-create-deploy-portal.md) classica).

    > [!WARNING]
    > Se non si vuole perdere l'indirizzo IP associato a questo slot di distribuzione, è possibile usare [Soluzione 3 - Mantenere l'indirizzo IP](cloud-services-allocation-failures.md#solutions).

1. Aggiornare il *record CNAME* o *A* in modo che punti al nuovo servizio cloud (versione classica).
    - Per altre istruzioni, vedere la guida Configurazione di un nome di dominio personalizzato per un servizio cloud di [Azure (versione](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) classica).

1. Dopo aver eliminato il traffico verso il sito precedente, è possibile eliminare il servizio cloud precedente (versione classica).
    - Per altre istruzioni, vedere la guida Eliminare distribuzioni e un servizio [cloud (versione](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) classica).
    - Per visualizzare il traffico di rete nel servizio cloud (versione classica), vedere Introduzione al monitoraggio del servizio [cloud (versione classica).](cloud-services-how-to-monitor.md)

Vedere Risoluzione dei problemi relativi agli errori di allocazione del [servizio cloud (versione classica) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) per altri passaggi di correzione.

## <a name="next-steps"></a>Passaggi successivi

Per altre soluzioni di errore di allocazione e informazioni di base:

> [!div class="nextstepaction"]
> [Errori di allocazione - Servizio cloud (versione classica)](cloud-services-allocation-failures.md)

Se il problema di Azure non viene risolto in questo articolo, visitare i forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in [@AzureSupport su Twitter](https://twitter.com/AzureSupport). È anche possibile inviare una richiesta di supporto tecnico di Azure. Per inviare una richiesta di supporto tecnico, nella pagina [Supporto tecnico di Azure](https://azure.microsoft.com/support/options/) selezionare *Supporto*.
