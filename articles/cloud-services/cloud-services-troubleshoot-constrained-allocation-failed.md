---
title: Risolvere i problemi di ConstrainedAllocationFailed durante la distribuzione di un servizio cloud (versione classica) in Azure | Microsoft Docs
description: Questo articolo illustra come risolvere un'eccezione ConstrainedAllocationFailed durante la distribuzione di un servizio cloud (versione classica) in Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 4a92246f81ddd10840bb0dcf7edf2b01853fd148
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876599"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Risolvere i problemi di ConstrainedAllocationFailed durante la distribuzione di un servizio cloud (versione classica) in Azure

In questo articolo si risolveranno gli errori di allocazione in cui i servizi cloud di Azure (versione classica) non possono essere distribuiti a causa di vincoli di allocazione.

Quando si distribuiscono istanze in un servizio cloud (versione classica) o si aggiungono nuove istanze del ruolo Web o di lavoro, Microsoft Azure alloca le risorse di calcolo.

In alcuni casi possono verificarsi errori durante queste operazioni, anche prima di raggiungere il limite della sottoscrizione di Azure.

> [!TIP]
> Queste informazioni potrebbero risultare utili anche quando si pianifica la distribuzione dei servizi.

## <a name="symptom"></a>Sintomo

Nella portale di Azure passare al servizio cloud (versione classica) e nella barra laterale selezionare Log *operazioni (versione classica)* per visualizzare i log.

![L'immagine mostra il pannello Log operazioni (versione classica).](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Quando si esaminano i log del servizio cloud (versione classica), viene visualizzata l'eccezione seguente:

|Tipo di eccezione  |Messaggio di errore  |
|---------|---------|
|ConstrainedAllocationFailed     |Operazione di Azure `{Operation ID}` ' ' non riuscita con codice Compute.ConstrainedAllocationFailed. Dettagli: Allocazione non riuscita. non è in grado di soddisfare i vincoli nella richiesta. La nuova distribuzione richiesta del servizio è legata a un gruppo di affinità o ha come destinazione una rete virtuale, oppure è presente una distribuzione esistente in questo servizio ospitato. Una di queste condizioni vincola la nuova distribuzione a risorse Azure specifiche. Riprovare più tardi o provare a ridurre le dimensioni della macchina virtuale o il numero di istanze del ruolo. In alternativa, se possibile, rimuovere i vincoli sopra indicati o provare a distribuire in un'area diversa.|

## <a name="cause"></a>Causa

Quando la prima istanza viene distribuita in un servizio cloud (in gestione temporanea o in produzione), tale servizio cloud viene aggiunto a un cluster.

Nel corso del tempo, le risorse in questo cluster possono diventare completamente utilizzate. Se un servizio cloud (versione classica) effettua una richiesta di allocazione per più risorse quando sono disponibili risorse insufficienti nel cluster bloccato, la richiesta comporterà un errore di allocazione. Per altre informazioni, vedere i problemi [comuni relativi all'errore di allocazione.](cloud-services-allocation-failures.md#common-issues)

## <a name="solution"></a>Soluzione

I servizi cloud esistenti *vengono aggiunti* a un cluster. Eventuali altre distribuzioni per il servizio cloud (versione classica) verranno evase nello stesso cluster.

Quando si verifica un errore di allocazione in questo scenario, è consigliabile ridistribuire in un nuovo servizio cloud (versione classica) e aggiornare *il CNAME*.

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