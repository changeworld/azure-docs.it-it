---
title: Panoramica delle Attività del Registro Azure Container
description: Introduzione ad ACR Tasks, una suite di funzionalità di Registro Azure Container che offre funzionalità di creazione, gestione e applicazione di patch di immagini di contenitori sicure e automatizzate nel cloud.
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: a42a2bfcdc1621689421940c4db2fcf4f5e64b89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781002"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatizzare le compilazioni e la manutenzione delle immagini dei contenitori con le attività di Controllo di ancoraggio

I contenitori offrono nuovi livelli di virtualizzazione, isolando le dipendenze di sviluppo e delle applicazioni dai requisiti operativi e dell'infrastruttura. Ciò che rimane, tuttavia, è la necessità di gestire e applicare patch a questa virtualizzazione dell'applicazione nel ciclo di vita del contenitore.

## <a name="what-is-acr-tasks"></a>Informazioni su ACR Tasks

**ACR Tasks** è una suite di funzionalità in Registro Azure Container. Offre la creazione di immagini [](#image-platforms) contenitore basate sul cloud per piattaforme quali Linux, Windows e ARM e può automatizzare l'applicazione di patch al sistema operativo e al [framework](#automate-os-and-framework-patching) per i contenitori Docker. ACR Tasks non solo estende il ciclo di sviluppo "inner-loop" al cloud con compilazioni di immagini del contenitore su richiesta, ma consente anche compilazioni automatizzate attivate da aggiornamenti del codice sorgente, aggiornamenti all'immagine di base di un contenitore o timer. Con i trigger di aggiornamento delle immagini di base, ad esempio, è possibile automatizzare il flusso di lavoro di applicazione delle patch del sistema operativo e del framework dell'applicazione, mantenendo gli ambienti sicuri rispettando i principi dei contenitori non modificabili.

## <a name="task-scenarios"></a>Scenari di attività

ACR Tasks supporta diversi scenari per compilare e gestire immagini del contenitore e altri elementi. Per informazioni dettagliate, vedere le sezioni seguenti di questo articolo.

* **[Attività rapida:](#quick-task)** Compilare ed eseguire il push di una singola immagine del contenitore in un registro contenitori su richiesta, in Azure, senza che sia necessaria un'installazione locale del motore Docker. Pensare `docker build`, `docker push` nel cloud.
* **Attività attivate automaticamente:** abilitare uno o più *trigger per* compilare un'immagine:
  * **[Trigger all'aggiornamento del codice sorgente](#trigger-task-on-source-code-update)** 
  * **[Trigger all'aggiornamento dell'immagine di base](#automate-os-and-framework-patching)** 
  * **[Trigger in base a una pianificazione](#schedule-a-task)** 
* **[Attività in più passaggi:](#multi-step-tasks)** estendere la funzionalità di compilazione e push a immagine singola delle attività ACR con flussi di lavoro basati su più passaggi basati su più contenitori. 

A ogni attività ACR è associato un contesto di [codice](#context-locations) sorgente, il percorso di un set di file di origine usato per compilare un'immagine del contenitore o un altro artefatto. I contesti di esempio includono un repository Git o un file system locale.

Le attività possono anche sfruttare le [variabili di esecuzione](container-registry-tasks-reference-yaml.md#run-variables), in modo da poter riutilizzare le definizioni delle attività e standardizzare i tag per immagini ed elementi.

## <a name="quick-task"></a>Attività rapida

Il ciclo di sviluppo interno, processo iterativo di scrittura di codice, compilazione e test dell'applicazione prima di eseguire il commit nel controllo del codice sorgente, è in realtà l'inizio della gestione del ciclo di vita del contenitore.

Prima di eseguire il commit della prima riga di codice, la funzione [attività rapida](container-registry-tutorial-quick-task.md) di Attività del Registro Azure Container può offrire un'esperienza di sviluppo integrato eseguendo l'offload delle build di un'immagine del contenitore in Azure. Con questa funzionalità è possibile verificare le definizioni delle compilazioni automatiche e rilevare problemi potenziali prima di eseguire il commit di codice.

Usando il formato familiare, il `docker build` [comando az acr build][az-acr-build] nell'interfaccia della riga di comando di Azure accetta un contesto (il set di file da compilare), lo invia ad ACR Tasks e, per impostazione predefinita, inserisce l'immagine compilata nel registro al completamento. [](#context-locations)

Per un'introduzione, vedere la guida introduttiva per [compilare ed eseguire un'immagine del contenitore](container-registry-quickstart-task-cli.md) in Registro Azure Container.  

ACR Tasks è progettato come primitiva del ciclo di vita dei contenitori. È ad esempio possibile integrare ACR Tasks in una soluzione CI/CD. Eseguendo [az login][az-login] con un' [entità servizio][az-login-service-principal], la soluzione di integrazione continua/distribuzione continua potrà quindi eseguire comandi [az acr build][az-acr-build] per avviare compilazioni delle immagini.

Per informazioni su come usare le attività rapide, vedere la prima esercitazione su Attività del Registro Azure Container, [Compilare immagini dei contenitori nel cloud con Attività del Registro Azure Container](container-registry-tutorial-quick-task.md).

> [!TIP]
> Se si vuole compilare ed eseguire il push di un'immagine direttamente dal codice sorgente, senza un Dockerfile, Registro Azure Container il [comando az acr pack build][az-acr-pack-build] (anteprima). Questo strumento compila ed inserisce un'immagine dal codice sorgente dell'applicazione usando [Cloud Native Buildpacks.](https://buildpacks.io/)

## <a name="trigger-task-on-source-code-update"></a>Attività trigger per l'aggiornamento del codice sorgente

Attivare una compilazione di immagini del contenitore o un'attività in più passaggi quando viene eseguito il commit del codice o quando viene effettuata o aggiornata una richiesta pull in un repository Git pubblico o privato in GitHub o Azure DevOps. Ad esempio, configurare un'attività di compilazione con il comando [az acr task create][az-acr-task-create] dell'interfaccia della riga di comando di Azure specificando un repository Git e facoltativamente un ramo e un Dockerfile. Quando il team aggiorna il codice nel repository, un webhook creato da Attività di ACR attiva una compilazione dell'immagine del contenitore definita nel repository. 

ACR Tasks supporta i trigger seguenti quando si imposta un repository Git come contesto dell'attività:

| Trigger | Abilitato per impostazione predefinita |
| ------- | ------------------ |
| Commit | Sì |
| Richiesta pull | No |

Per configurare un trigger di aggiornamento del codice sorgente, è necessario fornire all'attività un token di accesso personale (PAT) per impostare il webhook nel repository gitHub o Azure DevOps pubblico o privato.

> [!NOTE]
> Attualmente, Attività del Registro Azure Container non supporta i trigger per il commit o le richieste pull nei repository di GitHub Enterprise.

Per informazioni su come attivare compilazioni in caso di commit del codice sorgente, vedere la seconda esercitazione su Attività del Registro Azure Container, [Automatizzare la compilazione di immagini dei contenitori con Attività del Registro Azure Container](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizzare l'applicazione di patch al sistema operativo e al framework

La potenza di Attività di ACR per migliorare realmente il flusso di lavoro di compilazione del contenitore deriva dalla capacità di rilevare un aggiornamento a *un'immagine di base.* Una funzionalità della maggior parte delle immagini del contenitore, un'immagine di base è un'immagine padre su cui si basano una o più immagini dell'applicazione. Le immagini di base contengono in genere il sistema operativo e talvolta i framework applicazioni. 

È possibile configurare un'attività di Registro Controllo di accesso per tenere traccia di una dipendenza da un'immagine di base durante la compilazione di un'immagine dell'applicazione. Quando si esegue il push dell'immagine di base aggiornata nel Registro di sistema o un'immagine di base viene aggiornata in un archivio pubblico, ad esempio in Docker Hub, le attività del registro di controllo di accesso possono compilare automaticamente tutte le immagini dell'applicazione basate su di essa.
Grazie al rilevamento e alla ricompilazione automatici, ACR Tasks consente di risparmiare il tempo e il lavoro normalmente necessari per monitorare e aggiornare manualmente ogni immagine di applicazione che fa riferimento all'immagine di base aggiornata.

Altre informazioni sui [trigger di aggiornamento delle immagini di base per](container-registry-tasks-base-images.md) le attività di ACR. Per informazioni su come attivare la compilazione di un'immagine quando viene inserito un'immagine di base in un registro contenitori, vedere l'esercitazione Automatizzare le compilazioni di immagini del [contenitore](container-registry-tutorial-base-image-update.md) quando un'immagine di base viene aggiornata in un Registro Azure Container

## <a name="schedule-a-task"></a>Pianificare un'attività

Facoltativamente, pianificare un'attività configurando uno o più *trigger timer* quando si crea o si aggiorna l'attività. La pianificazione di un'attività è utile per l'esecuzione di carichi di lavoro del contenitore in base a una pianificazione definita o per l'esecuzione di operazioni di manutenzione o test sulle immagini di cui è stato eseguito regolarmente il push nel Registro di sistema. Per informazioni dettagliate, [vedere Eseguire un'attività ACR in base a una pianificazione definita.](container-registry-tasks-scheduled.md)

## <a name="multi-step-tasks"></a>Attività in più passaggi

Le attività in più passi consentono di definire ed eseguire attività basate su passaggi per creare, testare e correggere le immagini del contenitore nel cloud. I passaggi delle attività definiti in [un file YAML](container-registry-tasks-reference-yaml.md) specificano singole operazioni di compilazione e push per le immagini del contenitore o altri elementi. Possono anche definire l'esecuzione di uno o più contenitori, in cui ogni passaggio usa il contenitore come ambiente di esecuzione.

Ad esempio, è possibile creare un'attività in più passaggi che consenta di automatizzare le operazioni seguenti:

1. Compilare un'immagine dell'applicazione Web
1. Eseguire il contenitore dell'applicazione Web
1. Compilare un'immagine di test dell'applicazione Web
1. Eseguire il contenitore di test dell'applicazione Web, che esegue test sul contenitore dell'applicazione in esecuzione
1. Se i test vengono superati, compilare un pacchetto di archivio del grafico di Helm
1. Eseguire un `helm upgrade` usando il nuovo pacchetto di archivio del grafico di Helm

Le attività in più passaggi consentono di dividere la creazione, l'esecuzione e il test di un'immagine in più passaggi componibili, con il supporto della dipendenza tra i passaggi. Con le attività in più passaggi in ACR Tasks si dispone di un controllo più granulare sulla compilazione delle immagini, sui test e sui flussi di lavoro di applicazione di patch al framework e al sistema operativo.

Altre informazioni sulle attività in più passaggi in [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Eseguire attività di compilazione, test e applicazione di patch in più passaggi con ACR Tasks).

## <a name="context-locations"></a>Posizioni del contesto

La tabella seguente mostra esempi di percorsi di contesto supportati per le attività del registro di controllo di acr:

| Posizione contesto | Descrizione | Esempio |
| ---------------- | ----------- | ------- |
| File system locale | File contenuti in una directory nel file System locale. | `/home/user/projects/myapp` |
| Ramo principale di GitHub | File all'interno del ramo principale (o altro ramo predefinito) di un repository GitHub pubblico o privato.  | `https://github.com/gituser/myapp-repo.git` |
| Ramo GitHub | Ramo specifico di un repository GitHub pubblico o privato.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Sottocartella di GitHub | File all'interno di una sottocartella in un repository GitHub pubblico o privato. Nell'esempio viene illustrata la combinazione di una specifica di ramo e sottocartella. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Commit github | Commit specifico in un repository GitHub pubblico o privato. L'esempio mostra la combinazione di un hash di commit (SHA) e di una specifica della sottocartella. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Azure DevOps sottocartella | File all'interno di una sottocartella in un repo di Azure pubblico o privato. L'esempio mostra una combinazione di specifica di ramo e sottocartella. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| File tarball remoto | File in un archivio compresso in un server Web remoto. | `http://remoteserver/myapp.tar.gz` |
| Artefatto nel registro contenitori | [File di artefatto OCI](container-registry-oci-artifacts.md) in un repository del registro contenitori. | `oci://myregistry.azurecr.io/myartifact:mytag` |

> [!NOTE]
> Quando si usa un repository Git privato come contesto per un'attività, è necessario fornire un token di accesso personale.

## <a name="image-platforms"></a>Piattaforme di immagini

Per impostazione predefinita, ACR Tasks compila immagini per il sistema operativo Linux e l'architettura amd64. Specificare il `--platform` tag per compilare immagini Windows o Linux per altre architetture. Specificare il sistema operativo e, facoltativamente, un'architettura supportata nel formato sistema operativo/architettura (ad esempio, `--platform Linux/arm` ). Per le architetture ARM, specificare facoltativamente una variante nel formato sistema operativo/architettura/variante (ad esempio, `--platform Linux/arm64/v8` ):

| OS | Architettura|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Visualizzare l'output dell'attività

Ogni esecuzione dell'attività genera un output del log che è possibile controllare per determinare se i passaggi dell'attività sono stati eseguiti correttamente. Quando si attiva un'attività manualmente, l'output del log per l'esecuzione dell'attività viene trasmesso alla console e archiviato anche per un successivo recupero. Quando un'attività viene attivata automaticamente, ad esempio da un commit del codice sorgente o da un aggiornamento dell'immagine di base, i log attività vengono archiviati solo. Visualizzare i log di esecuzione nel portale di Azure o usare il [comando az acr task logs.](/cli/azure/acr/task#az_acr_task_logs)

Vedere altre informazioni sulla [visualizzazione e la gestione dei log attività.](container-registry-tasks-logs.md)

## <a name="next-steps"></a>Passaggi successivi

Quando si è pronti ad automatizzare la compilazione e la manutenzione delle immagini dei contenitori nel cloud, vedere la serie di esercitazioni su [Attività di ACR.](container-registry-tutorial-quick-task.md)

Installare eventualmente l'[estensione Docker per Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e l'estensione [Account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) per l'uso dei registri contenitori di Azure. Eseguire il pull e il push delle immagini in un registro contenitori di Azure o eseguire Attività del Registro Azure Container, il tutto all'interno di Visual Studio Code.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
