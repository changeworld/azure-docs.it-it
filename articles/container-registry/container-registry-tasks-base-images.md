---
title: Aggiornamenti delle immagini di base - Attività
description: Informazioni sulle immagini di base per le immagini del contenitore dell'applicazione e su come un aggiornamento dell'immagine di base può attivare un'Registro Azure Container lavoro.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: fc501cc1db654c11675e5a4f0d19a5a56b63a165
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781182"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Informazioni sugli aggiornamenti delle immagini di base per Attività di ACR

Questo articolo fornisce informazioni generali sugli aggiornamenti all'immagine di base di un'applicazione e su come questi aggiornamenti possono attivare un'Registro Azure Container lavoro.

## <a name="what-are-base-images"></a>Che cosa sono le immagini di base?

I Dockerfile che definiscono la maggior parte delle immagini del contenitore specificano un'immagine padre da cui si basa l'immagine, spesso definita immagine *di base.* Le immagini di base contengono in genere il sistema operativo, ad esempio [Alpine Linux][base-alpine] o [Windows Server Nano][base-windows], su cui vengono applicati i restanti livelli del contenitore. Questi possono includere anche framework applicazioni come [Node.js][base-node] o [.NET Core][base-dotnet]. Queste immagini di base sono in genere basate su immagini upstream pubbliche. Diverse immagini di applicazioni possono condividere un'immagine di base comune.

Un'immagine di base viene spesso aggiornata dal gestore delle immagini per includere nuove funzionalità o nuovi miglioramenti del sistema operativo o del framework. Un'altra comune causa di aggiornamento dell'immagine di base è costituita dalle patch di sicurezza. Quando si verificano questi aggiornamenti upstream, è necessario aggiornare anche le immagini di base per includere la correzione critica. È quindi necessario anche ricompilare ogni immagine di applicazione per includere gli aggiornamenti upstream ora inclusi nell'immagine di base.

In alcuni casi, ad esempio un team di sviluppo privato, un'immagine di base potrebbe specificare più del sistema operativo o del framework. Ad esempio, un'immagine di base può essere un'immagine del componente del servizio condiviso che deve essere rilevata. I membri di un team potrebbero dover tenere traccia di questa immagine di base per il test o aggiornare regolarmente l'immagine durante lo sviluppo di immagini dell'applicazione.

## <a name="maintain-copies-of-base-images"></a>Gestire copie di immagini di base

Per qualsiasi contenuto nei registri che dipende dal contenuto di base gestito in un registro pubblico, ad esempio Docker Hub, è consigliabile copiare il contenuto in un registro contenitori di Azure o in un altro registro privato. Assicurarsi quindi di compilare le immagini dell'applicazione facendo riferimento alle immagini di base private. Registro Azure Container offre una funzionalità [di importazione di](container-registry-import-images.md) immagini per copiare facilmente il contenuto da registri pubblici o altri registri contenitori di Azure. La sezione successiva descrive l'uso di ACR Tasks per tenere traccia degli aggiornamenti delle immagini di base durante la compilazione degli aggiornamenti dell'applicazione. È possibile tenere traccia degli aggiornamenti delle immagini di base nei registri contenitori di Azure e, facoltativamente, nei registri pubblici upstream.

## <a name="track-base-image-updates"></a>Tenere traccia degli aggiornamenti delle immagini di base

Attività del Registro Azure Container include la possibilità di compilare automaticamente le immagini quando viene aggiornata l'immagine di base di un contenitore. È possibile usare questa funzionalità per gestire e aggiornare copie di immagini di base pubbliche nei registri contenitori di Azure e quindi per ricompilare le immagini dell'applicazione che dipendono dalle immagini di base.

ACR Tasks individua dinamicamente le dipendenze dell'immagine di base quando compila un'immagine del contenitore. Di conseguenza, può rilevare quando viene aggiornata l'immagine di base di un'immagine dell'applicazione. Con un'attività di compilazione preconfigurata, ACR Tasks può ricompilare automaticamente ogni immagine dell'applicazione che fa riferimento all'immagine di base. Grazie al rilevamento e alla ricompilazione automatici, ACR Tasks consente di risparmiare il tempo e il lavoro normalmente necessari per monitorare e aggiornare manualmente ogni immagine di applicazione che fa riferimento all'immagine di base aggiornata.

## <a name="base-image-locations"></a>Posizioni delle immagini di base

Per la creazione di immagini da un documento Dockerfile, un'attività di Registro Azure Container rileva le dipendenze da immagini di base nelle posizioni seguenti:

* La stessa istanza di Registro Azure Container in cui viene eseguita l'attività
* Un altro registro azure container privato nella stessa area o in un'area diversa 
* Un repository pubblico in Docker Hub 
* Un repository pubblico in Registro contenitori di Microsoft

Se l'immagine di base specificata nell'istruzione si trova in una di queste posizioni, l'attività ACR aggiunge un hook per assicurarsi che l'immagine sia ricompilata ogni volta che la `FROM` relativa base viene aggiornata.

## <a name="base-image-notifications"></a>Notifiche delle immagini di base

Il tempo tra l'aggiornamento di un'immagine di base e l'attivazione dell'attività dipendente dipende dalla posizione dell'immagine di base:

* Immagini di base da un repository pubblico in Docker Hub o **MCR:** per le immagini di base nei repository pubblici, un'attività ACR verifica la presenza di aggiornamenti delle immagini in un intervallo casuale compreso tra 10 e 60 minuti. Le attività dipendenti vengono eseguite di conseguenza.
* **Immagini di base da un Registro Azure Container:** per le immagini di base nei registri contenitori di Azure, un'attività di Registro Azure Container attiva immediatamente un'esecuzione quando viene aggiornata l'immagine di base. L'immagine di base può essere nello stesso ACR in cui viene eseguita l'attività o in un ACR diverso in qualsiasi area.

## <a name="additional-considerations"></a>Altre considerazioni

* **Immagini di base per le immagini dell'applicazione:** attualmente, un'attività ACR tiene traccia solo degli aggiornamenti delle immagini di base per le immagini dell'applicazione *(runtime).* Non tiene traccia degli aggiornamenti delle immagini di base per le immagini intermedie (*buildtime*) usate in Dockerfile in più fasi.  

* **Abilitato per impostazione predefinita:** quando si crea un'attività ACR con il  [comando az acr task create,][az-acr-task-create] per impostazione predefinita l'attività viene abilitata per il trigger da un aggiornamento dell'immagine di base. Ciò significa che la proprietà `base-image-trigger-enabled` è impostata su True. Se si vuole disabilitare questo comportamento in un'attività, aggiornare la proprietà impostandola su False. Eseguire ad esempio il comando [az acr task update][az-acr-task-update] seguente:

  ```azurecli
  az acr task update --registry myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Trigger per** tenere traccia delle dipendenze: per consentire a un'attività ACR di determinare e tenere traccia delle dipendenze di un'immagine del contenitore, che includono la relativa immagine di base, è prima necessario attivare l'attività per compilare l'immagine almeno una **volta.** Attivare ad esempio l'attività manualmente usando il comando [az acr task run][az-acr-task-run].

* **Tag stabile per l'immagine di base:** per attivare un'attività sull'aggiornamento dell'immagine di base, l'immagine di base deve avere un *tag* stabile, ad esempio `node:9-alpine` . Questo tag è tipico per un'immagine di base che viene aggiornata con le patch di sistema operativo e framework all'ultima versione stabile. Se l'immagine di base viene aggiornata con un nuovo tag di versione, l'attività non viene attivata. Per altre informazioni sull'uso di tag per le immagini, vedere le [procedure consigliate](container-registry-image-tag-version.md). 

* **Altri trigger di attività:** in un'attività attivata dagli aggiornamenti dell'immagine di base, è anche possibile abilitare i trigger in base al [commit](container-registry-tutorial-build-task.md) del codice sorgente o [a una pianificazione.](container-registry-tasks-scheduled.md) Un aggiornamento dell'immagine di base può anche attivare [un'attività in più passaggi.](container-registry-tasks-multi-step.md)

## <a name="next-steps"></a>Passaggi successivi

Vedere le esercitazioni seguenti per gli scenari per automatizzare la compilazione di immagini dell'applicazione dopo l'aggiornamento di un'immagine di base:

* [Automatizzare la compilazione di immagini del contenitore quando un'immagine di base viene aggiornata nello stesso registro](container-registry-tutorial-base-image-update.md)

* [Automatizzare la compilazione di immagini del contenitore quando un'immagine di base viene aggiornata in un registro diverso](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
