---
title: Arrestare o avviare manualmente il gruppo di contenitori
description: Informazioni su come arrestare o avviare manualmente un gruppo di contenitori in Istanze di Azure Container.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 43498a7d31f0cb78751d2f318aede8523b7b9345
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787000"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Arrestare o avviare manualmente i contenitori in Istanze di Azure Container

[L'impostazione dei criteri di](container-instances-restart-policy.md) riavvio di un gruppo di contenitori determina la modalità di avvio o arresto delle istanze del contenitore per impostazione predefinita. È possibile ignorare l'impostazione predefinita arrestando o avviando manualmente un gruppo di contenitori.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>Interrompere

Arrestare manualmente un gruppo di contenitori in esecuzione, ad esempio usando il [comando az container stop][az-container-stop] o portale di Azure. Per determinati carichi di lavoro del contenitore, potrebbe essere necessario arrestare un gruppo di contenitori con esecuzione lunga dopo un periodo definito per risparmiare sui costi. 

*Quando un gruppo di contenitori entra nello stato Arrestato, termina e ricicla tutti i contenitori nel gruppo. Non mantiene lo stato del contenitore.*

Quando i contenitori vengono riciclati, le [risorse vengono](container-instances-container-groups.md#resource-allocation) deallocate e la fatturazione viene arrestata per il gruppo di contenitori.

L'azione di arresto non ha alcun effetto se il gruppo di contenitori è già terminato (si trova nello stato Operazione completata o Non riuscita). Ad esempio, un gruppo di contenitori con attività del contenitore run-once che sono state eseguite correttamente termina nello stato Operazione completata. I tentativi di arrestare il gruppo in tale stato non modificano lo stato. 

## <a name="start"></a>Avvio

Quando un gruppo di contenitori viene arrestato, perché i contenitori sono terminati per conto proprio o il gruppo è stato arrestato manualmente, è possibile avviare i contenitori. Ad esempio, usare il [comando az container start][az-container-start] o portale di Azure per avviare manualmente i contenitori nel gruppo. Se l'immagine di un contenitore viene aggiornata, verrà inserita una nuova immagine. 

L'avvio di un gruppo di contenitori inizia una nuova distribuzione con la stessa configurazione del contenitore. Questa azione consente di riusare rapidamente la configurazione di un gruppo di contenitori nota che funziona come previsto. Non è necessario creare un nuovo gruppo di contenitori per eseguire lo stesso carico di lavoro.

Tutti i contenitori in un gruppo di contenitori vengono avviati da questa azione. Non è possibile avviare un contenitore specifico nel gruppo.

Dopo aver avviato o riavviato manualmente un gruppo di contenitori, questo funzionerà in base ai criteri di riavvio configurati.
  
## <a name="restart"></a>Riavvio

È possibile riavviare un gruppo di contenitori mentre è in esecuzione, ad esempio usando il [comando az container restart.][az-container-restart] Questa operazione riavvia tutti i contenitori nel gruppo di contenitori. Se l'immagine di un contenitore viene aggiornata, verrà inserita una nuova immagine. 

Il riavvio di un gruppo di contenitori è utile quando si desidera risolvere un problema di distribuzione. Ad esempio, se una limitazione temporanea delle risorse impedisce la corretta esecuzione dei contenitori, riavviare il gruppo potrebbe risolvere il problema.

Tutti i contenitori in un gruppo di contenitori vengono riavviati da questa azione. Non è possibile riavviare un contenitore specifico nel gruppo.

Dopo aver riavviato manualmente un gruppo di contenitori, il gruppo di contenitori viene eseguito in base ai criteri di riavvio configurati.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [impostazioni dei criteri di riavvio](container-instances-restart-policy.md) in Istanze di Azure Container.

Oltre ad arrestare e avviare manualmente un gruppo di contenitori con la configurazione esistente, è possibile [aggiornare le impostazioni](container-instances-update.md) di un gruppo di contenitori in esecuzione.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container#az_container_restart
[az-container-start]: /cli/azure/container#az_container_start
[az-container-stop]: /cli/azure/container#az_container_stop
