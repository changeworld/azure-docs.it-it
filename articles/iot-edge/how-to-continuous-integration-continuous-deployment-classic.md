---
title: Integrazione continua e distribuzione continua nei dispositivi Azure IoT Edge (editor classico) - Azure IoT Edge
description: Configurare l'integrazione continua e la distribuzione continua usando l'editor classico Azure IoT Edge con Azure DevOps, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f7c28ecbaa58731c528a9ecb5f869eba2bc0c99f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484422"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Integrazione continua e distribuzione continua in Azure IoT Edge dispositivi (editor classico)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Si può facilmente adottare DevOps con le applicazioni Azure IoT Edge con le attività integrate Azure IoT Edge in Azure Pipelines. Questo articolo illustra come usare le funzionalità di integrazione continua e distribuzione continua di Azure Pipelines per compilare, testare e distribuire applicazioni in modo rapido ed efficiente nel Azure IoT Edge usando l'editor classico. In alternativa, è possibile [usare YAML.](how-to-continuous-integration-continuous-deployment.md)

![Diagramma - rami CI e CD per lo sviluppo e la produzione](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

In questo articolo si apprenderà [](/azure/devops/pipelines/tasks/build/azure-iot-edge) come usare le attività Azure IoT Edge per Azure Pipelines per creare pipeline di compilazione e versione per la IoT Edge soluzione. Ogni Azure IoT Edge aggiunta alla pipeline implementa una delle quattro azioni seguenti:

 | Azione | Descrizione |
 | --- | --- |
 | Compilare immagini del modulo | Accetta il codice IoT Edge soluzione e compila le immagini del contenitore.|
 | Eseguire il push delle immagini del modulo | Inserisce le immagini del modulo nel registro contenitori specificato. |
 | Generare il manifesto della distribuzione | Accetta un deployment.template.jsfile e le variabili, quindi genera il file manifesto della IoT Edge di distribuzione finale. |
 | Distribuire nei dispositivi IoT Edge | Crea IoT Edge in uno o più IoT Edge dispositivi. |

Se non diversamente specificato, le procedure descritte in questo articolo non esplorino tutte le funzionalità disponibili tramite i parametri dell'attività. Per altre informazioni, vedere gli argomenti seguenti:

* [Versione dell'attività](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Avanzate:** se applicabile, specificare i moduli che non si desidera compilati.
* [Opzioni di controllo](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Variabili di ambiente](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Variabili di output](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Prerequisiti

* Un repository Azure Repos. Se non se ne ha uno, è possibile [creare un nuovo repository Git nel progetto](/azure/devops/repos/git/create-new-repo). Per questo articolo è stato creato un repository denominato **IoTEdgeRepo**.
* Una soluzione IoT Edge di cui sia stato eseguito il commit e il push nel repository. Se si vuole creare una nuova soluzione di esempio per testare le procedure di questo articolo, seguire i passaggi descritti in [Usare Visual Studio Code per sviluppare moduli per Azure IoT Edge ed eseguirne il debug](how-to-vs-code-develop-module.md) oppure [Usare Visual Studio 2017 per sviluppare ed eseguire il debug di moduli C# per Azure IoT Edge](./how-to-visual-studio-develop-module.md). Per questo articolo è stata creata una soluzione nel repository denominata **IoTEdgeSolution,** che contiene il codice per un modulo denominato **filtermodule.**

   Per questo articolo, tutto quello che serve è la cartella della soluzione creata dai modelli IoT Edge in Visual Studio Code o Visual Studio. Non è necessario compilare, eseguire il push, distribuire o eseguire il debug di questo codice prima di procedere. Questi processi verranno impostati in Azure Pipelines.

   Se si sta creando una nuova soluzione, prima di tutto clonare il repository in locale. Quindi, quando si crea la soluzione, sarà possibile crearla direttamente nella cartella del repository. Da questa posizione è possibile eseguire facilmente il commit e il push dei nuovi file.

* Un registro contenitori in cui eseguire il push delle immagini dei moduli. È possibile usare il [Registro Azure Container](../container-registry/index.yml) o un registro di terze parti.
* Un hub [IoT di](../iot-hub/iot-hub-create-through-portal.md) Azure attivo con almeno due IoT Edge per testare le fasi di distribuzione di test e produzione separate. È possibile seguire gli articoli di avvio rapido per creare un dispositivo IoT Edge in [Linux](quickstart-linux.md) o [Windows](quickstart.md)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Creare una pipeline di compilazione per l'integrazione continua

In questa sezione viene creata una nuova pipeline di compilazione. La pipeline viene configurata per l'esecuzione automatica quando si archiviano le modifiche apportate alla soluzione di esempio IoT Edge e per pubblicare i log di compilazione.

1. Accedere all'organizzazione Azure DevOps ( ) e aprire il progetto che contiene il repository `https://dev.azure.com/{your organization}` IoT Edge soluzione.

    ![Aprire il progetto DevOps](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. Nel menu del riquadro sinistro del progetto selezionare **Pipeline.** Selezionare **Crea pipeline** al centro della pagina. In caso contrario, se sono già presenti pipeline di compilazione, selezionare il **pulsante Nuova pipeline** in alto a destra.

    ![Creazione di una nuova pipeline di compilazione](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. Nella parte inferiore della pagina Where is your code? (Dove si **trova il codice?)** **selezionare Use the classic editor (Usa l'editor classico).** Se si vuole usare YAML per creare le pipeline di compilazione del progetto, vedere la [guida YAML](how-to-continuous-integration-continuous-deployment.md).

    ![Selezionare Usa l'editor classico](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Seguire le istruzioni visualizzate per creare la pipeline.

   1. Specificare le informazioni sull'origine per la nuova pipeline di compilazione. Selezionare l'origine **GIT Azure Repos**, quindi selezionare progetto, repository e ramo in cui si trova il codice della soluzione IoT Edge. Selezionare quindi **Continua.**

      ![Selezionare l'origine della pipeline](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Selezionare l'opzione per iniziare con una **fase vuota** anziché un modello.

      ![Iniziare con un processo vuoto per la pipeline di compilazione](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. Dopo la creazione della pipeline, viene visualizzato l'editor pipeline. Qui è possibile modificare il nome della pipeline, il pool di agenti e la specifica dell'agente.

   È possibile selezionare un pool ospitato da Microsoft o un pool self-hosted che si gestisce.

   Nella descrizione della pipeline scegliere la specifica dell'agente corretta in base alla piattaforma di destinazione:

   * Se si desidera compilare i moduli nella piattaforma amd64 per contenitori Linux, scegliere **ubuntu-16.04**

   * Se si desidera compilare i moduli nella piattaforma amd64 per i contenitori Windows 1809, è necessario [configurare un agente auto-ospitato in Windows](/azure/devops/pipelines/agents/v2-windows).

   * Se si desidera compilare i moduli nella piattaforma arm32v7 o arm64 per contenitori Linux, è necessario configurare l'agente [self-hosted in Linux.](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent)

    ![Configurare la specifica dell'agente di compilazione](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. La pipeline è preconfigurata con un processo denominato **Processo agente 1**. Selezionare il segno più ( ) per aggiungere quattro attività al processo: Azure IoT Edge due volte, Copia file una sola e Pubblica **+** **artefatti di compilazione** una sola volta.   Cercare ogni attività e passare il puntatore del mouse sul nome dell'attività per visualizzare il **pulsante** Aggiungi.

   ![Aggiungere l'attività Azure IoT Edge](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Quando vengono aggiunte tutte e quattro le attività, il processo di Agent è simile all'esempio seguente:

   ![Quattro attività nella pipeline di compilazione](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Selezionare la prima attività **Azure IoT Edge** per modificarla. Questa attività compila tutti i moduli nella soluzione con la piattaforma di destinazione specificata. Modificare l'attività con i valori seguenti:

    | Parametro | Descrizione |
    | --- | --- |
    | Nome visualizzato | Il nome visualizzato viene aggiornato automaticamente quando il campo Azione cambia. |
    | Azione | Selezionare **Compila immagini modulo**. |
    | .template.jssul file | Selezionare i puntini di sospensione (**...**) e passare al file **deployment.template.json** nel repository che contiene la soluzione IoT Edge. |
    | Piattaforma predefinita | Selezionare il sistema operativo appropriato per i moduli in base al dispositivo IoT Edge destinazione. |
    | Variabili di output | Specificare un nome di riferimento da associare al percorso del file in cui viene generato deployment.jsfile, ad esempio **edge**. |

   Queste configurazioni usano il repository di immagini e il tag definiti nel file per assegnare un nome `module.json` e contrassegnare l'immagine del modulo. **Le immagini del modulo** di compilazione consentono anche di sostituire le variabili con il valore esatto definito nel `module.json` file. In Visual Studio o Visual Studio Code si specifica il valore effettivo in un `.env` file. In Azure Pipelines il valore viene impostato nella scheda **Variabili pipeline.** Selezionare la **scheda Variabili** nel menu dell'editor della pipeline e configurare il nome e il valore come segue:

    * **ACR_ADDRESS:** valore del Registro Azure Container **del server di accesso.** È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure.

    Se nel progetto sono presenti altre variabili, è possibile specificare il nome e il valore in questa **scheda.** Le immagini del modulo di compilazione riconoscono solo le variabili in `${VARIABLE}` formato . Assicurarsi di usare questo formato nei `**/module.json` file.

8. Selezionare la seconda attività **Azure IoT Edge** per modificarla. Questa attività esegue il push di tutte le immagini dei moduli nel registro contenitori selezionato.

    | Parametro | Descrizione |
    | --- | --- |
    | Nome visualizzato | Il nome visualizzato viene aggiornato automaticamente quando il campo Azione viene modificato. |
    | Azione | Selezionare **Push module images (Eseguire il push delle immagini del modulo).** |
    | Tipo di registro contenitori | Usare il tipo predefinito: `Azure Container Registry` . |
    | Sottoscrizione di Azure | Scegliere la propria sottoscrizione. |
    | Registro Azure Container | selezionare il tipo di registro contenitori che si usa per archiviare le immagini dei moduli. A seconda del tipo di registro scelto, il modulo cambia. Se si sceglie **Registro Azure Container**, usare gli elenchi a discesa per selezionare la sottoscrizione di Azure e il nome del registro contenitori. Se si sceglie **Generic Container Registry** (Registro contenitori generico), selezionare **Nuovo** per creare una connessione al servizio di registro. |
    | .template.jssul file | Selezionare i puntini di sospensione (**...**) e passare al file **deployment.template.json** nel repository che contiene la soluzione IoT Edge. |
    | Piattaforma predefinita | Selezionare il sistema operativo appropriato per i moduli in base al dispositivo IoT Edge destinazione. |
    | Aggiungere le credenziali del registro al manifesto della distribuzione | Specificare true per aggiungere le credenziali del registro per eseguire il push delle immagini Docker nel manifesto della distribuzione. |

   Se si dispone di più registri contenitori per ospitare le immagini del modulo, è necessario duplicare questa attività, selezionare un registro contenitori diverso e usare Ignora moduli nelle **impostazioni** avanzate per ignorare le immagini che non sono per questo registro specifico. 

9. Selezionare **l'attività Copia** file per modificarla. Usare questa attività per copiare i file nella directory di gestione temporanea dell'artefatto.

    | Parametro | Descrizione |
    | --- | --- |
    | Nome visualizzato | Usare il nome predefinito o personalizzarlo |
    | Cartella di origine | Cartella con i file da copiare. |
    | Contenuto | Aggiungere due righe: `deployment.template.json` e `**/module.json` . Questi due file fungono da input per generare il manifesto IoT Edge distribuzione. |
    | Cartella di destinazione | Specificare la variabile `$(Build.ArtifactStagingDirectory)` . Per [informazioni sulla descrizione,](/azure/devops/pipelines/build/variables#build-variables) vedere Compilare variabili. |

10. Selezionare l'attività **Pubblica artefatti di compilazione** per modificarla. Specificare il percorso della directory di staging dell'artefatto per l'attività in modo che il percorso possa essere pubblicato nella pipeline di rilascio.

    | Parametro | Descrizione |
    | --- | --- |
    | Nome visualizzato | Usare il nome predefinito o personalizzarlo. |
    | Percorso da pubblicare | Specificare la variabile `$(Build.ArtifactStagingDirectory)` . Per [altre informazioni, vedere Compilare](/azure/devops/pipelines/build/variables#build-variables) variabili. |
    | Nome dell'artefatto | Usare il nome predefinito: **drop** |
    | Percorso di pubblicazione dell'artefatto | Usare il percorso predefinito: **Azure Pipelines** |

11. Aprire la scheda **Trigger** e selezionare la casella **Abilita l'integrazione continua**. Verificare che il ramo contenente il codice sia incluso.

    ![Attivare il trigger di integrazione continua](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Selezionare Salva **nell'elenco a discesa Salva &** coda. 

Questa pipeline è ora configurata per l'esecuzione automatica quando si esegue il push di nuovo codice al repository. L'ultima attività, quella di pubblicazione di artefatti della pipeline, attiva una pipeline di versione. Passare alla sezione successiva per creare la pipeline di versione.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Se si vogliono usare **distribuzioni** a più livelli nella pipeline, le distribuzioni a più livelli non sono ancora supportate nelle attività Azure IoT Edge in Azure DevOps.
>
>Tuttavia, è possibile usare un'attività dell'interfaccia della riga di comando di [Azure Azure DevOps](/azure/devops/pipelines/tasks/deploy/azure-cli) creare la distribuzione come distribuzione a più livelli. Per il **valore Script inline** è possibile usare il [comando az iot edge deployment create:](/cli/azure/iot/edge/deployment)
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Passaggi successivi

* IoT Edge di procedure consigliate devOps in [Azure DevOps Starter per IoT Edge](how-to-devops-starter.md)
* Per altre informazioni sulla distribuzione IoT Edge, vedere [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala)
* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-at-scale.md).