---
title: 'Esercitazione: implementare CI/CD con GitOps usando cluster Kubernetes abilitati per Azure Arc'
description: Questa esercitazione illustra la configurazione di una soluzione CI/CD usando GitOps con i cluster Kubernetes abilitati per Azure Arc. Per un'operazione concettuale su questo flusso di lavoro, vedere l'articolo flusso di lavoro CI/CD con GitOps-Azure Arc Enabled Kubernetes.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial
ms.openlocfilehash: a94784f2f3fc622e0232033d63bc957279a7d34c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076310"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Esercitazione: implementare CI/CD con GitOps usando cluster Kubernetes abilitati per Azure Arc


In questa esercitazione verrà configurata una soluzione CI/CD usando GitOps con i cluster Kubernetes abilitati per Azure Arc. Usando l'app Azure vote di esempio, è possibile:

> [!div class="checklist"]
> * Creare un cluster Kubernetes abilitato per Azure Arc.
> * Connettere l'applicazione e i repository GitOps a Azure Repos.
> * Importare pipeline CI/CD.
> * Connettere il Container Registry di Azure (ACR) ad Azure DevOps e Kubernetes.
> * Creare gruppi di variabili di ambiente.
> * Distribuire gli `dev` `stage` ambienti e.
> * Testare gli ambienti dell'applicazione.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Questa esercitazione presuppone una certa familiarità con DevOps, Azure Repos e pipeline di Azure e l'interfaccia della riga di comando di Azure.

* Accedere [Azure DevOps Services](https://dev.azure.com/).
* Completare l' [esercitazione precedente](./tutorial-use-gitops-connected-cluster.md) per informazioni su come distribuire GitOps per l'ambiente ci/CD.
* Comprendere i [vantaggi e l'architettura](./conceptual-configurations.md) di questa funzionalità.
* Verificare di avere:
  * Un [cluster Kubernetes abilitato per Azure Arc](./quickstart-connect-cluster.md#connect-an-existing-kubernetes-cluster) denominato **Arc-CICD-cluster**.
  * Un Container Registry di Azure connesso (ACR) con l' [integrazione AKS](../../aks/cluster-container-registry-integration.md) o [l'autenticazione del cluster non AKS](../../container-registry/container-registry-auth-kubernetes.md).
  * Autorizzazioni "compilazione amministratore" e "amministratore progetto" per [Azure Repos](/azure/devops/repos/get-started/what-is-repos) e [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started).
* Installare le seguenti estensioni dell'interfaccia della riga di comando di Azure Arc abilitate per Kubernetes di versioni >= 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * Per aggiornare queste estensioni alla versione più recente, eseguire i comandi seguenti:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-configuration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Importazione di applicazioni e repository GitOps in Azure Repos

Importare un [repository dell'applicazione](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#application-repo) e un [repository GitOps](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#gitops-repo) in Azure Repos. Per questa esercitazione, usare il repository di esempio seguente:

* repository dell'applicazione **Arc-CICD-demo-src**
   * URL: https://github.com/Azure/arc-cicd-demo-src
   * Contiene l'app Azure vote di esempio che viene distribuita con GitOps.
* **Arc-CICD-demo-gitops** Repository GitOps
   * URL: https://github.com/Azure/arc-cicd-demo-gitops
   * Funziona come base per le risorse cluster che ospitano l'app Azure vote.

Altre informazioni sull' [importazione di repository git](/azure/devops/repos/git/import-git-repository).

>[!NOTE]
> L'importazione e l'utilizzo di due repository distinti per le applicazioni e i repository GitOps possono migliorare la sicurezza e la semplicità. Le autorizzazioni e la visibilità dei repository GitOps e delle applicazioni possono essere ottimizzate singolarmente.
> È ad esempio possibile che l'amministratore del cluster non trovi le modifiche nel codice dell'applicazione rilevanti per lo stato desiderato del cluster. Viceversa, uno sviluppatore di applicazioni non deve essere in grado di comprendere i parametri specifici per ogni ambiente. potrebbe essere sufficiente un set di valori di test che forniscono la copertura per i parametri.

## <a name="connect-the-gitops-repo"></a>Connettere il repository GitOps

Per distribuire continuamente l'app, connettere il repository dell'applicazione al cluster usando GitOps. Il repository **Arc-CICD-demo-gitops** gitops contiene le risorse di base per far funzionare l'app nel cluster **Arc-CICD-cluster** .

Il repository GitOps iniziale contiene solo un [manifesto](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) che crea gli spazi dei nomi dello **sviluppo** e della **fase** corrispondenti agli ambienti di distribuzione.

La connessione GitOps creata verrà automaticamente:
* Sincronizzare i manifesti nella directory del manifesto.
* Aggiornare lo stato del cluster.

Il flusso di lavoro CI/CD popola la directory del manifesto con manifesti aggiuntivi per distribuire l'app.


1. [Creare una nuova connessione GitOps](./tutorial-use-gitops-connected-cluster.md) al repository **Arc-CICD-demo-GitOps** appena importato in Azure Repos.

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

1. Assicurarsi che Flux usi *solo* la `arc-cicd-cluster/manifests` Directory come percorso di base. Definire il percorso usando il parametro operator seguente:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Se si usa una stringa di connessione HTTPS e si verificano problemi di connessione, assicurarsi di omettere il prefisso del nome utente nell'URL. Ad esempio, `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` deve essere stato `alice@` rimosso. `--https-user`Specifica invece l'utente, ad esempio `--https-user alice` .

1. Controllare lo stato della distribuzione in portale di Azure.
   * In caso di esito positivo, verranno visualizzati sia `dev` `stage` gli spazi dei nomi che quelli creati nel cluster.

## <a name="import-the-cicd-pipelines"></a>Importare le pipeline CI/CD

Ora che è stata sincronizzata una connessione GitOps, è necessario importare le pipeline di integrazione continua/recapito continuo che creano i manifesti.

Il repository dell'applicazione contiene una `.pipeline` cartella con le pipeline da usare per le richieste pull, ci e CD. Importare e rinominare le tre pipeline fornite nel repository di esempio:

| Nome file Pipeline | Descrizione |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | Pipeline dell'applicazione PR, denominata **Arc-CICD-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | La pipeline dell'applicazione CI, denominata **Arc-CICD-demo-src ci** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | La pipeline CD dell'applicazione, denominata **Arc-CICD-demo-src CD** |



## <a name="connect-your-acr"></a>Connettere il record di registro di sistema
Sia le pipeline che il cluster utilizzeranno ACR per archiviare e recuperare le immagini docker.

### <a name="connect-acr-to-azure-devops"></a>Connettere ACR ad Azure DevOps
Durante il processo CI, i contenitori di applicazioni verranno distribuiti in un registro di sistema. Per iniziare, creare una connessione al servizio di Azure:

1. In Azure DevOps aprire la pagina **connessioni al servizio** dalla pagina Impostazioni progetto. In TFS aprire la pagina **Servizi** dall'icona **Impostazioni** nella barra dei menu superiore.
2. Scegliere **+ nuova connessione al servizio** e selezionare il tipo di connessione al servizio necessario.
3. Immettere i parametri per la connessione al servizio. Per questa esercitazione:
   * Denominare il servizio Connection **Arc-Demo-ACR**. 
   * Selezionare **myResourceGroup** come gruppo di risorse.
4. Selezionare l' **autorizzazione Concedi accesso a tutte le pipeline**. 
   * Questa opzione autorizza i file della pipeline YAML per le connessioni al servizio. 
5. Scegliere **OK** per creare la connessione.

### <a name="connect-acr-to-kubernetes"></a>Connetti ACR a Kubernetes
Consentire al cluster Kubernetes di eseguire il pull delle immagini dal record di registro di sistema. Se è privato, sarà necessaria l'autenticazione.

#### <a name="connect-acr-to-existing-aks-clusters"></a>Connettere ACR ai cluster AKS esistenti

Integrare un record di controllo di registro esistente con i cluster AKS esistenti usando il comando seguente:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Creare un segreto di pull immagine

Per connettere i cluster locali e non AKS al registro contenitori di sistema, creare un segreto di pull immagine. Kubernetes usa i segreti di pull delle immagini per archiviare le informazioni necessarie per l'autenticazione del registro.

Creare un segreto di pull immagine con il `kubectl` comando seguente. Ripetere per gli `dev` `stage` spazi dei nomi e.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

> [!TIP]
> Per evitare di dover impostare un imagePullSecret per ogni pod, è consigliabile aggiungere imagePullSecret all'account del servizio negli `dev` `stage` spazi dei nomi e. Per ulteriori informazioni, vedere l' [esercitazione su Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) .

## <a name="create-environment-variable-groups"></a>Creare gruppi di variabili di ambiente

### <a name="app-repo-variable-group"></a>Gruppo di variabili del repository app
[Creare un gruppo di variabili](/azure/devops/pipelines/library/variable-groups) denominato **AZ-vote-app-dev**. Impostare i valori seguenti:

| Variabile | valore |
| -------- | ----- |
| AZ_ACR_NAME | (ad esempio, l'istanza di ACR). azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (la connessione al servizio di Azure, che dovrebbe essere **Arc-Demo-ACR** precedente nell'esercitazione) |
| AZURE_VOTE_IMAGE_REPO | Percorso completo del repository dell'app Azure vote, ad esempio azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Sviluppo |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | Stringa di connessione git per il repository GitOps |
| Token di accesso personali | [Token Pat creato](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate#create-a-pat) con autorizzazioni di lettura/scrittura per l'origine. Salvarlo per usarlo in seguito durante la creazione del `stage` gruppo di variabili. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Contrassegnare PAT come tipo di segreto. Nelle applicazioni, prendere in considerazione il collegamento di segreti da un insieme di credenziali delle chiavi di [Azure](/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault).
>
### <a name="stage-environment-variable-group"></a>Gruppo di variabili di ambiente della fase

1. Clonare il gruppo di variabili **AZ-vote-app-dev** .
1. Modificare il nome in **AZ-vote-app-stage**.
1. Verificare i valori seguenti per le variabili corrispondenti:

| Variabile | valore |
| -------- | ----- |
| ENVIRONMENT_NAME | Fase |
| TARGET_NAMESPACE | `stage` |

A questo punto si è pronti per la distribuzione negli `dev` `stage` ambienti e.

## <a name="deploy-the-dev-environment-for-the-first-time"></a>Distribuire l'ambiente di sviluppo per la prima volta
Con le pipeline CI e CD create, eseguire la pipeline CI per distribuire l'app per la prima volta.

### <a name="ci-pipeline"></a>Pipeline CI

Durante l'esecuzione iniziale della pipeline CI, è possibile che si verifichi un errore di autorizzazione delle risorse durante la lettura del nome della connessione del servizio.
1. Verificare che la variabile a cui si accede sia AZURE_SUBSCRIPTION.
1. Autorizzare l'utilizzo.
1. Eseguire di nuovo la pipeline.

Pipeline CI:
* Garantisce che la modifica dell'applicazione superi tutti i controlli di qualità automatici per la distribuzione.
* Esegue una convalida aggiuntiva che non è stato possibile completare nella pipeline di richiesta pull.
    * Specifico per GitOps, la pipeline pubblica anche gli elementi per il commit che verranno distribuiti dalla pipeline CD.
* Verifica che l'immagine Docker sia cambiata e che venga effettuato il push della nuova immagine.

### <a name="cd-pipeline"></a>Pipeline CD
L'esecuzione della pipeline CI riuscita attiva la pipeline CD per completare il processo di distribuzione. Si distribuirà in ogni ambiente in modo incrementale.

> [!TIP]
> Se la pipeline CD non viene attivata automaticamente:
> 1. Verificare che il nome corrisponda al trigger Branch in [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * Il valore dovrebbe essere `arc-cicd-demo-src CI`.
> 1. Eseguire di nuovo la pipeline CI.

Dopo la generazione del modello e del manifesto nel repository GitOps, la pipeline CD creerà un commit, ne effettuerà il push e creerà una richiesta di conferma.
1. Aprire il collegamento PR specificato nell' `Create PR` output dell'attività.
1. Verificare le modifiche apportate al repository GitOps. Dovrebbe essere visualizzato:
   * Modifiche al modello Helm di alto livello.
   * Manifesti Kubernetes di basso livello che mostrano le modifiche sottostanti allo stato desiderato. Flux distribuisce questi manifesti.
1. Se tutto sembra corretto, approvare e completare la richiesta pull.

1. Dopo alcuni minuti, Flux preleva la modifica e avvia la distribuzione.
1. Inviare la porta localmente usando `kubectl` e assicurarsi che l'app funzioni correttamente usando:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Visualizzare l'app Azure vote nel browser all'indirizzo `http://localhost:8080/` .

1. Votare i Preferiti e prepararsi a apportare alcune modifiche all'app.

## <a name="set-up-environment-approvals"></a>Configurare le approvazioni dell'ambiente
Al momento della distribuzione dell'app, non è possibile apportare modifiche solo al codice o ai modelli, ma è anche possibile involontariamente impostare il cluster in uno stato non valido.

Se l'ambiente di sviluppo rivela un'interruzioni dopo la distribuzione, mantenerla negli ambienti successivi usando le approvazioni dell'ambiente.

1. Nel progetto Azure DevOps passare all'ambiente che deve essere protetto.
1. Passare ad **approvazioni e verificare** la risorsa.
1. Selezionare **Crea**.
1. Fornire i responsabili approvazione e un messaggio facoltativo.
1. Selezionare di nuovo **Crea** per completare l'aggiunta del controllo di approvazione manuale.

Per altri dettagli, vedere l'esercitazione [definire l'approvazione e i controlli](/azure/devops/pipelines/process/approvals) .

Alla successiva esecuzione della pipeline CD, la pipeline verrà sospesa dopo la creazione della richiesta pull GitOps. Verificare che la modifica sia stata sincronizzata correttamente e che la funzionalità di base sia passata. Approvare il controllo dalla pipeline per consentire al flusso di modifiche di passare all'ambiente successivo.

## <a name="make-an-application-change"></a>Apportare una modifica all'applicazione

Con questo set di modelli e manifesti di base che rappresenta lo stato del cluster, verrà apportata una piccola modifica all'app.

1. Nel repository **Arc-CICD-demo-src** modificare [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) file.

2. Dal momento che "Gatti vs cani" non ricevono voti sufficienti, impostarlo su "tabulazioni e spazi" per aumentare il numero di voti.

3. Eseguire il commit della modifica in un nuovo ramo, eseguirne il push e creare una richiesta pull.
   * Questo è il flusso di sviluppo tipico che avvierà il ciclo di vita CI/CD.

## <a name="pr-validation-pipeline"></a>Pipeline di convalida PR

La pipeline della richiesta pull è la prima linea di difesa contro una modifica difettosa. I normali controlli della qualità del codice dell'applicazione includono l'analisi statica e la lanugine. Dal punto di vista di GitOps, è anche necessario garantire la stessa qualità per la distribuzione dell'infrastruttura risultante.

I grafici Dockerfile e Helm dell'applicazione possono usare un metodo simile per l'applicazione.

Errori rilevati durante l'intervallo di pelucchi da:
* Formattazione non corretta dei file YAML,
* Suggerimenti sulle procedure consigliate, ad esempio l'impostazione dei limiti di CPU e memoria per l'applicazione.

> [!NOTE]
> Per ottenere la migliore copertura da parte di Helm in un'applicazione reale, è necessario sostituire i valori ragionevolmente simili a quelli usati in un ambiente reale.

Gli errori rilevati durante l'esecuzione della pipeline vengono visualizzati nella sezione risultati test dell'esecuzione. Da qui, è possibile:
* Tenere traccia delle statistiche utili sui tipi di errore.
* Trovare il primo commit su cui sono stati rilevati.
* Stile di traccia dello stack collegamenti alle sezioni di codice che hanno causato l'errore.

Al termine dell'esecuzione della pipeline, sarà garantita la qualità del codice dell'applicazione e del modello che lo distribuirà. È ora possibile approvare e completare la richiesta pull. L'CI verrà nuovamente eseguito, rigenerando i modelli e i manifesti prima di attivare la pipeline CD.

> [!TIP]
> In un ambiente reale, non dimenticare di impostare i criteri per i rami per assicurarsi che la richiesta pull superi i controlli di qualità. Per altre informazioni, vedere l'articolo [su come impostare i criteri](/azure/devops/repos/git/branch-policies) per i rami.

## <a name="cd-process-approvals"></a>Approvazioni del processo CD

Un'esecuzione di pipeline CI riuscita attiva la pipeline CD per completare il processo di distribuzione. Come per la prima volta, è possibile distribuire la pipeline di CD in ogni ambiente in modo incrementale. Questa volta, per la pipeline è necessario approvare ogni ambiente di distribuzione.

1. Approva la distribuzione nell' `dev` ambiente.
1. Dopo la generazione del modello e del manifesto nel repository GitOps, la pipeline CD creerà un commit, ne effettuerà il push e creerà una richiesta di conferma.
1. Aprire il collegamento PR specificato nell'attività.
1. Verificare le modifiche apportate al repository GitOps. Dovrebbe essere visualizzato:
   * Modifiche al modello Helm di alto livello.
   * Manifesti Kubernetes di basso livello che mostrano le modifiche sottostanti allo stato desiderato.
1. Se tutto sembra corretto, approvare e completare la richiesta pull.
1. Attendere il completamento della distribuzione.
1. Come smoke test di base, passare alla pagina dell'applicazione e verificare che l'app di voto ora visualizzi le schede e gli spazi.
   * Inviare la porta localmente usando `kubectl` e assicurarsi che l'app funzioni correttamente usando: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Visualizzare l'app Azure vote nel browser all'indirizzo http://localhost:8080/ e verificare che le opzioni di voto siano state modificate in tabulazioni e spazi. 
1. Ripetere i passaggi 1-7 per l' `stage` ambiente.

La distribuzione è stata completata. Il flusso di lavoro CI/CD viene terminato.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare l'applicazione, eliminare tutte le risorse con i passaggi seguenti:

1. Eliminare la connessione di configurazione di Azure Arc GitOps:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Rimuovere lo `dev` spazio dei nomi:
   * `kubectl delete namespace dev`

3. Rimuovere lo `stage` spazio dei nomi:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un flusso di lavoro CI/CD completo che implementa DevOps dallo sviluppo di applicazioni tramite la distribuzione. Le modifiche apportate all'app attivano automaticamente la convalida e la distribuzione, gestite da approvazioni manuali.

Passare al nostro articolo concettuale per ottenere altre informazioni su GitOps e sulle configurazioni con Azure Arc Enabled Kubernetes.

> [!div class="nextstepaction"]
> [Flusso di lavoro CI/CD con GitOps-Azure Arc Enabled Kubernetes](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd)
