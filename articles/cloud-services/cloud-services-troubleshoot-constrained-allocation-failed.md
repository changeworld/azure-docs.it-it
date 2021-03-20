---
title: Risolvere i problemi di ConstrainedAllocationFailed durante la distribuzione di un servizio cloud (versione classica) in Azure | Microsoft Docs
description: Questo articolo illustra come risolvere un'eccezione ConstrainedAllocationFailed quando si distribuisce un servizio cloud (classico) in Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 346e7eb77039ab80e6f9dffb8ea8360198040504
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738285"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Risolvere i problemi di ConstrainedAllocationFailed durante la distribuzione di un servizio cloud (versione classica) in Azure

Questo articolo illustra come risolvere gli errori di allocazione in cui i servizi cloud di Azure (versione classica) non possono essere distribuiti a causa di vincoli di allocazione.

Quando si distribuiscono istanze in un servizio cloud (versione classica) o si aggiungono nuove istanze del ruolo Web o di lavoro, Microsoft Azure alloca le risorse di calcolo.

Occasionalmente è possibile che si verifichino errori durante queste operazioni anche prima di raggiungere il limite della sottoscrizione di Azure.

> [!TIP]
> Queste informazioni potrebbero risultare utili anche quando si pianifica la distribuzione dei servizi.

## <a name="symptom"></a>Sintomo

In portale di Azure passare al servizio cloud (versione classica) e nella barra laterale selezionare *log operazioni (versione classica)* per visualizzare i log.

![Image Mostra il pannello del log delle operazioni (classico).](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Quando si esaminano i log del servizio cloud (versione classica), verrà visualizzata l'eccezione seguente:

|Tipo di eccezione  |Messaggio di errore  |
|---------|---------|
|ConstrainedAllocationFailed     |L'operazione di Azure ' `{Operation ID}` ' non è riuscita con codice COMPUTE. ConstrainedAllocationFailed. Dettagli: allocazione non riuscita. non è possibile soddisfare i vincoli nella richiesta. La nuova distribuzione richiesta del servizio è legata a un gruppo di affinità o ha come destinazione una rete virtuale, oppure è presente una distribuzione esistente in questo servizio ospitato. Una di queste condizioni vincola la nuova distribuzione a risorse Azure specifiche. Riprovare più tardi o provare a ridurre le dimensioni della macchina virtuale o il numero di istanze del ruolo. In alternativa, se possibile, rimuovere i vincoli sopra indicati o provare a distribuire in un'area diversa.|

## <a name="cause"></a>Causa

Quando la prima istanza viene distribuita in un servizio cloud (in gestione temporanea o in produzione), il servizio cloud viene aggiunto a un cluster.

Nel tempo, le risorse in questo cluster possono essere completamente utilizzate. Se un servizio cloud (versione classica) effettua una richiesta di allocazione per più risorse quando nel cluster bloccato sono disponibili risorse insufficienti, la richiesta provocherà un errore di allocazione. Per ulteriori informazioni, vedere [problemi comuni relativi all'errore di allocazione](cloud-services-allocation-failures.md#common-issues).

## <a name="solution"></a>Soluzione

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