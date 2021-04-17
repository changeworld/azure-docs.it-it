---
title: 'Esercitazione: Implementare CI/CD con GitOps usando Azure Arc cluster Kubernetes abilitati'
description: Questa esercitazione illustra la configurazione di una soluzione CI/CD usando GitOps Azure Arc cluster Kubernetes abilitati. Per un'analisi concettuale di questo flusso di lavoro, vedere l'articolo Flusso di lavoro ci/CD con GitOps - Azure Arc kubernetes abilitato.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: 9a228ce6f8b18afb77b656765abbad0bb4ae877f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589143"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Esercitazione: Implementare CI/CD con GitOps usando Azure Arc cluster Kubernetes abilitati


In questa esercitazione si imposterà una soluzione CI/CD usando GitOps con Azure Arc cluster Kubernetes abilitati. Usando l'app Di voto di Azure di esempio, si:

> [!div class="checklist"]
> * Creare un Azure Arc cluster Kubernetes abilitato.
> * Connettere l'applicazione e i repository GitOps Azure Repos.
> * Importare pipeline CI/CD.
> * Connettere il Registro Azure Container (ACR) a Azure DevOps e Kubernetes.
> * Creare gruppi di variabili di ambiente.
> * Distribuire gli `dev` ambienti `stage` e .
> * Testare gli ambienti dell'applicazione.

Se non si ™ una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Questa esercitazione presuppone familiarità con Azure DevOps, Azure Repos pipeline e l'interfaccia della riga di comando di Azure.

* Accedere a [Azure DevOps Services](https://dev.azure.com/).
* Completare [l'esercitazione precedente](./tutorial-use-gitops-connected-cluster.md) per informazioni su come distribuire GitOps per l'ambiente CI/CD.
* Comprendere i [vantaggi e l'architettura](./conceptual-configurations.md) di questa funzionalità.
* Verificare di disporre di:
  * Un [cluster Kubernetes Azure Arc connesso](./quickstart-connect-cluster.md#connect-an-existing-kubernetes-cluster) denominato **arc-cicd-cluster**.
  * Un'istanza Registro Azure Container (ACR) con l'integrazione del servizio [AKS](../../aks/cluster-container-registry-integration.md) o [con l'autenticazione del cluster non AKS.](../../container-registry/container-registry-auth-kubernetes.md)
  * Autorizzazioni "Build Admin" e "Project Admin" per [Azure Repos](/azure/devops/repos/get-started/what-is-repos) e [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started).
* Installare le estensioni dell'interfaccia Azure Arc kubernetes abilitate per le versioni >= 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8sconfiguration
  ```
  * Per aggiornare queste estensioni alla versione più recente, eseguire i comandi seguenti:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8sconfiguration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Importare i repository dell'applicazione e GitOps in Azure Repos

Importare [un repository dell'applicazione](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#application-repo) [e un repository GitOps](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#gitops-repo) in Azure Repos. Per questa esercitazione, usare i repository di esempio seguenti:

* **Arc-cicd-demo-src** application repo
   * URL: https://github.com/Azure/arc-cicd-demo-src
   * Contiene l'app Azure Vote di esempio che verrà distribuita con GitOps.
* **arc-cicd-demo-gitops** Repository GitOps
   * URL: https://github.com/Azure/arc-cicd-demo-gitops
   * Funziona come base per le risorse del cluster che ospitano l'app Azure Vote.

Altre informazioni [sull'importazione di repository Git.](/azure/devops/repos/git/import-git-repository)

>[!NOTE]
> L'importazione e l'uso di due repository separati per l'applicazione e i repository GitOps possono migliorare la sicurezza e la semplicità. Le autorizzazioni e la visibilità dell'applicazione e dei repository GitOps possono essere ottimizzate singolarmente.
> Ad esempio, l'amministratore del cluster potrebbe non trovare le modifiche nel codice dell'applicazione rilevanti per lo stato desiderato del cluster. Al contrario, uno sviluppatore di applicazioni non deve conoscere i parametri specifici per ogni ambiente. Un set di valori di test che forniscono copertura per i parametri può essere sufficiente.

## <a name="connect-the-gitops-repo"></a>Connettere il repository GitOps

Per distribuire continuamente l'app, connettere il repository dell'applicazione al cluster usando GitOps. Il repository GitOps **arc-cicd-demo-gitops** contiene le risorse di base per l'esecuzione dell'app nel cluster **arc-cicd-cluster.**

Il repository GitOps iniziale contiene solo un [manifesto che](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) crea gli spazi dei nomi **di** sviluppo e **fase** corrispondenti agli ambienti di distribuzione.

La connessione GitOps creata verrà eseguita automaticamente:
* Sincronizzare i manifesti nella directory del manifesto.
* Aggiornare lo stato del cluster.

Il flusso di lavoro CI/CD popola la directory manifesto con manifesti aggiuntivi per distribuire l'app.


1. [Creare una nuova connessione GitOps](./tutorial-use-gitops-connected-cluster.md) al repository **arc-cicd-demo-gitops** appena importato in Azure Repos.

   ```azurecli
   az k8sconfiguration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Assicurarsi che Flux *usi* solo la `arc-cicd-cluster/manifests` directory come percorso di base. Definire il percorso usando il parametro dell'operatore seguente:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Se si usa una stringa di connessione HTTPS e si verificano problemi di connessione, assicurarsi di omettere il prefisso del nome utente nell'URL. Ad esempio, `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` deve essere `alice@` stato rimosso. specifica `--https-user` invece l'utente, ad esempio `--https-user alice` .

1. Controllare lo stato della distribuzione in portale di Azure.
   * In caso di esito positivo, verranno visualizzati sia gli spazi dei nomi che gli `dev` spazi dei nomi creati nel `stage` cluster.

## <a name="import-the-cicd-pipelines"></a>Importare le pipeline CI/CD

Dopo aver sincronizzato una connessione GitOps, è necessario importare le pipeline CI/CD che creano i manifesti.

Il repo dell'applicazione contiene una cartella con le pipeline che verranno usate per le richieste PR, l'infrastruttura a esecuzione temporanea `.pipeline` e il CD. Importare e rinominare le tre pipeline fornite nel repo di esempio:

| Nome file della pipeline | Descrizione |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | Pipeline di richiesta pull dell'applicazione denominata **arc-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | Pipeline CI dell'applicazione denominata **arc-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | Pipeline del CD dell'applicazione denominata **arc-cicd-demo-src CD** |



## <a name="connect-your-acr"></a>Connettere il servizio ACR
Sia le pipeline che il cluster utilizzeranno Registro Controllo di accesso per archiviare e recuperare le immagini Docker.

### <a name="connect-acr-to-azure-devops"></a>Connettere ACR a Azure DevOps
Durante il processo ci si distribuiranno i contenitori dell'applicazione in un registro. Per iniziare, creare una connessione al servizio di Azure:

1. In Azure DevOps aprire la pagina **Connessioni al** servizio dalla pagina delle impostazioni del progetto. In TFS aprire la pagina **Servizi** dall'icona **delle** impostazioni nella barra dei menu superiore.
2. Scegliere **+ Nuova connessione al servizio** e selezionare il tipo di connessione al servizio necessario.
3. Specificare i parametri per la connessione al servizio. Per questa esercitazione:
   * Assegnare alla connessione del **servizio il nome arc-demo-acr**. 
   * Selezionare **myResourceGroup** come gruppo di risorse.
4. Selezionare **Concedi l'autorizzazione di accesso a tutte le pipeline.** 
   * Questa opzione autorizza i file della pipeline YAML per le connessioni al servizio. 
5. Scegliere **OK** per creare la connessione.

### <a name="connect-acr-to-kubernetes"></a>Connettere ACR a Kubernetes
Abilitare il cluster Kubernetes per eseguire il pull delle immagini da Registro Controllo di accesso. Se è privata, sarà necessaria l'autenticazione.

#### <a name="connect-acr-to-existing-aks-clusters"></a>Connettere Registro Controllo di accesso ai cluster del servizio Web DinK esistenti

Integrare un record di controllo di account esistente con i cluster del servizio Servizio Web DinK esistenti usando il comando seguente:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Creare un segreto di pull dell'immagine

Per connettere cluster locali e non del servizio AKS al registro di controllo di accesso, creare un segreto di pull dell'immagine. Kubernetes usa i segreti di pull delle immagini per archiviare le informazioni necessarie per autenticare il registro.

Creare un segreto di pull dell'immagine con il comando `kubectl` seguente. Ripetere l'operazione per gli `dev` spazi dei nomi e `stage` .
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

Per evitare di dover impostare imagePullSecret per ogni pod, è consigliabile aggiungere imagePullSecret all'account del servizio negli spazi `dev` dei nomi e `stage` . Per altre informazioni, vedere l'esercitazione su [Kubernetes.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account)

## <a name="create-environment-variable-groups"></a>Creare gruppi di variabili di ambiente

### <a name="app-repo-variable-group"></a>Gruppo di variabili del repo dell'app
[Creare un gruppo di variabili](/azure/devops/pipelines/library/variable-groups) denominato **az-vote-app-dev**. Impostare i valori seguenti:

| Variabile | valore |
| -------- | ----- |
| AZ_ACR_NAME | ad esempio l'istanza di ACR. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (connessione al servizio di Azure, che dovrebbe **essere arc-demo-acr** della versione precedente dell'esercitazione) |
| AZURE_VOTE_IMAGE_REPO | Percorso completo del repo dell'app di voto di Azure, ad esempio azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Sviluppo |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | Stringa di connessione Git per il repository GitOps |
| Token di accesso personali | Token [PAT creato con](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate#create-a-pat) autorizzazioni di origine di lettura/scrittura. Salvarlo per usarlo in un secondo momento durante la creazione `stage` del gruppo di variabili. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Contrassegnare pat come tipo di segreto. Nelle applicazioni prendere in considerazione il collegamento di segreti da [un azure keyvault.](/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault)
>
### <a name="stage-environment-variable-group"></a>Gruppo di variabili di ambiente di gestione temporanea

1. Clonare **il gruppo di variabili az-vote-app-dev.**
1. Modificare il nome in **az-vote-app-stage**.
1. Verificare i valori seguenti per le variabili corrispondenti:

| Variabile | valore |
| -------- | ----- |
| ENVIRONMENT_NAME | Fase |
| TARGET_NAMESPACE | `stage` |

A questo punto è possibile eseguire la distribuzione negli `dev` ambienti `stage` e .

## <a name="deploy-the-dev-environment-for-the-first-time"></a>Distribuire l'ambiente di sviluppo per la prima volta
Dopo aver creato le pipeline CI e CD, eseguire la pipeline DI PER distribuire l'app per la prima volta.

### <a name="ci-pipeline"></a>Pipeline CI

Durante l'esecuzione iniziale della pipeline DI, è possibile che venga visualizzato un errore di autorizzazione della risorsa durante la lettura del nome della connessione al servizio.
1. Verificare che la variabile a cui si accede sia AZURE_SUBSCRIPTION.
1. Autorizzare l'uso.
1. Eseguire nuovamente la pipeline.

La pipeline CI:
* Garantisce che la modifica dell'applicazione passi tutti i controlli di qualità automatizzati per la distribuzione.
* Esegue qualsiasi convalida aggiuntiva che non è stato possibile completare nella pipeline di richiesta pull.
    * Specifica per GitOps, la pipeline pubblica anche gli elementi per il commit che verranno distribuiti dalla pipeline CD.
* Verifica che l'immagine Docker sia stata modificata e che sia stato inserito il push della nuova immagine.

### <a name="cd-pipeline"></a>Pipeline CD
L'esecuzione corretta della pipeline CI attiva la pipeline CD per completare il processo di distribuzione. La distribuzione verrà distribuita in ogni ambiente in modo incrementale.

> [!TIP]
> Se la pipeline CD non viene attivata automaticamente:
> 1. Verificare che il nome corrisponda al trigger del ramo in [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * Il valore dovrebbe essere `arc-cicd-demo-src CI`.
> 1. Eseguire di nuovo la pipeline ci.

Dopo aver generato le modifiche al modello e al manifesto nel repository GitOps, la pipeline CD creerà un commit, eseguirà il push e creerà una richiesta pull per l'approvazione.
1. Aprire il collegamento di richiesta pull specificato `Create PR` nell'output dell'attività.
1. Verificare le modifiche al repository GitOps. Dovrebbe essere visualizzato quanto segue:
   * Modifiche al modello Helm di alto livello.
   * Manifesti Kubernetes di basso livello che mostrano le modifiche sottostanti allo stato desiderato. Flux distribuisce questi manifesti.
1. Se tutto sembra positivo, approvare e completare la richiesta pull.

1. Dopo alcuni minuti, Flux preleva la modifica e avvia la distribuzione.
1. Inoltrare la porta in locale usando `kubectl` e verificare che l'app funzioni correttamente usando:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Visualizzare l'app Azure Vote nel browser all'indirizzo `http://localhost:8080/` .

1. Votare per i preferiti e prepararsi ad apportare alcune modifiche all'app.

## <a name="set-up-environment-approvals"></a>Configurare le approvazioni dell'ambiente
Durante la distribuzione dell'app, non solo è possibile apportare modifiche al codice o ai modelli, ma anche inserire involontariamente il cluster in uno stato non valido.

Se l'ambiente di sviluppo rivela un'interruzione dopo la distribuzione, evitare di andare in ambienti successivi usando le approvazioni dell'ambiente.

1. Nel progetto Azure DevOps passare all'ambiente che deve essere protetto.
1. Passare ad **Approvazioni e controlli** per la risorsa.
1. Selezionare **Crea**.
1. Specificare i responsabili approvazione e un messaggio facoltativo.
1. Selezionare **di nuovo** Crea per completare l'aggiunta del controllo dell'approvazione manuale.

Per altri dettagli, vedere [l'esercitazione Definire l'approvazione e i](/azure/devops/pipelines/process/approvals) controlli.

Alla successiva esecuzione della pipeline CD, la pipeline verrà sospesa dopo la creazione della richiesta pull GitOps. Verificare che la modifica sia stata sincronizzata correttamente e che passi le funzionalità di base. Approvare il controllo dalla pipeline per consentire il flusso della modifica all'ambiente successivo.

## <a name="make-an-application-change"></a>Apportare una modifica all'applicazione

Con questo set di base di modelli e manifesti che rappresentano lo stato nel cluster, verrà apportata una piccola modifica all'app.

1. Nel **repo arc-cicd-demo-src** modificare [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) il file.

2. Poiché "Cats vs Dog" non riceve abbastanza voti, impostarlo su "Tabs vs Spaces" (Tabulazioni e spazi) per ottenere il conteggio dei voti.

3. Eseguire il commit della modifica in un nuovo ramo, eseguirne il push e creare una richiesta pull.
   * Questo è il flusso di sviluppo tipico che avvia il ciclo di vita di CI/CD.

## <a name="pr-validation-pipeline"></a>Pipeline di convalida della richiesta pull

La pipeline delle richieste pull è la prima linea di difesa contro una modifica non valida. I normali controlli di qualità del codice dell'applicazione includono linting e analisi statica. Dal punto di vista di GitOps, è anche necessario garantire la stessa qualità per la distribuzione dell'infrastruttura risultante.

I grafici Dockerfile e Helm dell'applicazione possono usare linting in modo simile all'applicazione.

Errori rilevati durante l'intervallo di linting da:
* File YAML formattati in modo non corretto, in
* Suggerimenti per le procedure consigliate, ad esempio l'impostazione dei limiti di CPU e memoria per l'applicazione.

> [!NOTE]
> Per ottenere la migliore copertura da Helm linting in un'applicazione reale, è necessario sostituire valori ragionevolmente simili a quelli usati in un ambiente reale.

Gli errori rilevati durante l'esecuzione della pipeline vengono visualizzati nella sezione dei risultati del test dell'esecuzione. Da qui, è possibile:
* Tenere traccia delle statistiche utili sui tipi di errore.
* Trovare il primo commit in cui sono stati rilevati.
* Lo stile di traccia dello stack è collegata alle sezioni di codice che hanno causato l'errore.

Al termine dell'esecuzione della pipeline, si avrà la certezza della qualità del codice dell'applicazione e del modello che la distribuirà. È ora possibile approvare e completare la richiesta pull. L'cia verrà eseguito di nuovo, rigenerando i modelli e i manifesti, prima di attivare la pipeline CD.

> [!TIP]
> In un ambiente reale, non dimenticare di impostare i criteri del ramo per assicurarsi che la richiesta pull passi i controlli di qualità. Per altre informazioni, vedere [l'articolo Impostare i criteri del](/azure/devops/repos/git/branch-policies) ramo.

## <a name="cd-process-approvals"></a>Approvazioni dei processi CD

Un'esecuzione corretta della pipeline CI attiva la pipeline CD per completare il processo di distribuzione. Analogamente alla prima volta che è possibile usare la pipeline CD, si distribuirà in ogni ambiente in modo incrementale. Questa volta, la pipeline richiede di approvare ogni ambiente di distribuzione.

1. Approvare la distribuzione `dev` nell'ambiente.
1. Dopo aver generato le modifiche al modello e al manifesto nel repository GitOps, la pipeline cd creerà un commit, ne eseguirà il push e creerà una richiesta pull per l'approvazione.
1. Aprire il collegamento della richiesta pull specificato nell'attività.
1. Verificare le modifiche apportate al repository GitOps. Dovrebbe essere visualizzato quanto segue:
   * Modifiche al modello Helm di alto livello.
   * Manifesti Kubernetes di basso livello che mostrano le modifiche sottostanti allo stato desiderato.
1. Se tutto sembra positivo, approvare e completare la richiesta pull.
1. Attendere il completamento della distribuzione.
1. Come esempio di smoke test, passare alla pagina dell'applicazione e verificare che l'app di voto ora visualizzi Tabulazioni e Spazi.
   * Inoltrare la porta in locale usando `kubectl` e verificare che l'app funzioni correttamente usando: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Visualizzare l'app Azure Vote nel browser all'indirizzo e verificare che le http://localhost:8080/ scelte di voto siano cambiate in Tabulazioni e Spazi. 
1. Ripetere i passaggi da 1 a 7 per `stage` l'ambiente.

La distribuzione è stata completata. Termina il flusso di lavoro CI/CD.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si continuerà a usare questa applicazione, eliminare tutte le risorse seguendo questa procedura:

1. Eliminare la Azure Arc configurazione gitOps:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Rimuovere lo spazio `dev` dei nomi:
   * `kubectl delete namespace dev`

3. Rimuovere lo spazio `stage` dei nomi:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un flusso di lavoro CI/CD completo che implementa DevOps dallo sviluppo di applicazioni alla distribuzione. Le modifiche all'app attivano automaticamente la convalida e la distribuzione, regolate da approvazioni manuali.

Passare all'articolo concettuale per altre informazioni su GitOps e le configurazioni Azure Arc Kubernetes abilitato.

> [!div class="nextstepaction"]
> [Flusso di lavoro CI/CD con GitOps - Azure Arc Kubernetes abilitato](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd)
