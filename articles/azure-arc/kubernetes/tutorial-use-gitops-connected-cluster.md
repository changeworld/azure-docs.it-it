---
title: 'Esercitazione: distribuire le configurazioni con GitOps in un cluster Kubernetes abilitato per Azure Arc'
description: Questa esercitazione illustra l'applicazione delle configurazioni in un cluster Kubernetes abilitato per Azure Arc. Per un'operazione concettuale su questo processo, vedere l'articolo configurazioni e GitOps-Azure Arc Enabled Kubernetes.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 1459d4a0f15595c6f658d73fcb9cb533023f0eb8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025718"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Esercitazione: distribuire le configurazioni con GitOps in un cluster Kubernetes abilitato per Azure Arc 

In questa esercitazione verranno applicate le configurazioni usando GitOps in un cluster Kubernetes abilitato per Azure Arc. Si apprenderà come:

> [!div class="checklist"]
> * Creare una configurazione in un cluster Kubernetes abilitato per Azure ARC usando un repository git di esempio.
> * Verificare che la configurazione sia stata creata correttamente.
> * Applicare la configurazione creare un repository git privato.
> * Convalidare la configurazione Kubernetes.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un cluster con connessione Kubernetes abilitato per Azure ARC esistente.
    - Se non è ancora stato connesso un cluster, vedere la [Guida introduttiva alla connessione di un cluster Kubernetes abilitato per Azure Arc](quickstart-connect-cluster.md).
- Comprendere i vantaggi e l'architettura di questa funzionalità. Per altre informazioni [, vedere l'articolo configurazioni e GitOps-Azure Arc Enabled Kubernetes](conceptual-configurations.md).

## <a name="create-a-configuration"></a>Creare una configurazione

Il [repository di esempio](https://github.com/Azure/arc-k8s-demo) usato in questo articolo è strutturato intorno all'utente di un operatore cluster. I manifesti in questo repository eseguono il provisioning di alcuni spazi dei nomi, distribuiscono i carichi di lavoro e forniscono una configurazione specifica del team. L'uso di questo repository con GitOps crea le risorse seguenti nel cluster:

* Spazi dei nomi: `cluster-config` , `team-a` , `team-b`
* Distribuzione `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

Il esegue il `config-agent` polling di Azure per le configurazioni nuove o aggiornate. Questa attività può richiedere fino a 30 secondi.

Se si associa un repository privato alla configurazione, completare la procedura seguente in [applicare la configurazione da un repository git privato](#apply-configuration-from-a-private-git-repository).

## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Usare l'estensione dell'interfaccia della riga di comando di Azure per per `k8s-configuration` collegare un cluster connesso al [repository git di esempio](https://github.com/Azure/arc-k8s-demo). 
1. Assegnare un nome a questa configurazione `cluster-config` .
1. Indica all'agente di distribuire l'operatore nello `cluster-config` spazio dei nomi.
1. Assegnare all'operatore le `cluster-admin` autorizzazioni.

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
    {
      "complianceStatus": {
      "complianceState": "Pending",
      "lastConfigApplied": "0001-01-01T00:00:00",
      "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
      "messageLevel": "3"
      },
      "configurationProtectedSettings": {},
      "enableHelmOperator": false,
      "helmOperatorProperties": null,
      "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
      "name": "cluster-config",
      "operatorInstanceName": "cluster-config",
      "operatorNamespace": "cluster-config",
      "operatorParams": "--git-readonly",
      "operatorScope": "cluster",
      "operatorType": "Flux",
      "provisioningState": "Succeeded",
      "repositoryPublicKey": "",
      "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
      "resourceGroup": "MyRG",
      "sshKnownHostsContents": "",
      "systemData": {
        "createdAt": "2020-11-24T21:22:01.542801+00:00",
        "createdBy": null,
        "createdByType": null,
        "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
        "lastModifiedBy": null,
        "lastModifiedByType": null
      },
      "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
      ```

### <a name="use-a-public-git-repository"></a>Usare un repository git pubblico

| Parametro | Format |
| ------------- | ------------- |
| `--repository-url` | http [s]://server/repo [. git] o git://server/repo [. git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Usare un repository git privato con chiavi SSH e create da Flux

Aggiungere la chiave pubblica generata da Flux all'account utente nel provider di servizi git. Se la chiave viene aggiunta al repository anziché all'account utente, usare al `git@` posto di `user@` nell'URL. 

Per altri dettagli, passare alla sezione [applicare la configurazione da un repository git privato](#apply-configuration-from-a-private-git-repository) .


| Parametro | Format | Note
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. git] o user@server:repo [. git] | `git@` può sostituire `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Usare un repository git privato con SSH e chiavi fornite dall'utente

Fornire la propria chiave privata direttamente o in un file. La chiave deve essere in [formato PEM](https://aka.ms/PEMformat) e terminare con una nuova riga (\n). 

Aggiungere la chiave pubblica associata all'account utente nel provider di servizi git. Se la chiave viene aggiunta al repository anziché all'account utente, usare `git@` al posto di `user@` . 

Per altri dettagli, passare alla sezione [applicare la configurazione da un repository git privato](#apply-configuration-from-a-private-git-repository) .  

| Parametro | Format | Note |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] o user@server:repo [. git] | `git@` può sostituire `user@` |
| `--ssh-private-key` | chiave con codifica Base64 in [formato PEM](https://aka.ms/PEMformat) | Fornire direttamente la chiave |
| `--ssh-private-key-file` | percorso completo del file locale | Specificare il percorso completo del file locale che contiene la chiave del formato PEM

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Usare un host git privato con SSH e host noti forniti dall'utente

L'operatore Flux gestisce un elenco di host git comuni nel file hosts noto per autenticare il repository git prima di stabilire la connessione SSH. Se si usa un repository Git non *comune* o un host git personalizzato, è possibile fornire la chiave host in modo che Flux possa identificare il repository. 

Analogamente alle chiavi private, è possibile fornire il contenuto del known_hosts direttamente o in un file. Quando si fornisce contenuto personalizzato, usare le [specifiche del formato del contenuto known_hosts](https://aka.ms/KnownHostsFormat), insieme a uno degli scenari chiave SSH precedenti.

| Parametro | Format | Note |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] o user@server:repo [. git] | `git@` può sostituire `user@` |
| `--ssh-known-hosts` | con codifica Base64 | Fornire direttamente contenuto host noto |
| `--ssh-known-hosts-file` | percorso completo del file locale | Fornire contenuto host noto in un file locale |

### <a name="use-a-private-git-repository-with-https"></a>Usare un repository git privato con HTTPS

| Parametro | Format | Note |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. git] | HTTPS con autenticazione di base |
| `--https-user` | con codifica RAW o Base64 | Nome utente HTTPS |
| `--https-key` | con codifica RAW o Base64 | Token di accesso personale HTTPS o password

>[!NOTE]
>* Helm operator Chart versione 1.2.0 + supporta l'autenticazione privata HTTPS della versione Helm.
>* La versione Helm HTTPS non è supportata per i cluster gestiti da AKS.
>* Se è necessario Flux per accedere al repository git tramite il proxy, sarà necessario aggiornare gli agenti di Azure Arc con le impostazioni del proxy. Per ulteriori informazioni, vedere la pagina relativa alla [connessione tramite un server proxy in uscita](./quickstart-connect-cluster.md#connect-using-an-outbound-proxy-server).


## <a name="additional-parameters"></a>Parametri aggiuntivi

Personalizzare la configurazione con i parametri facoltativi seguenti:

| Parametro | Descrizione |
| ------------- | ------------- |
| `--enable-helm-operator`| Consente di abilitare il supporto per le distribuzioni del grafico Helm. |
| `--helm-operator-params` | Valori del grafico per l'operatore Helm (se abilitato). Ad esempio: `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Versione del grafico per l'operatore Helm (se abilitata). Usare la versione 1.2.0 +. Impostazione predefinita: "1.2.0". |
| `--operator-namespace` | Nome per lo spazio dei nomi dell'operatore. Impostazione predefinita:' default '. Massimo: 23 caratteri. |
| `--operator-params` | Parametri per l'operatore. Deve essere specificato tra virgolette singole. Ad esempio, usare ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>Opzioni supportate in  `--operator-params` :

| Opzione | Descrizione |
| ------------- | ------------- |
| `--git-branch`  | Ramo del repository git da usare per i manifesti Kubernetes. Il valore predefinito è "master". I repository più recenti hanno un ramo radice denominato `main` , nel qual caso è necessario impostare `--git-branch=main` . |
| `--git-path`  | Percorso relativo all'interno del repository Git per il flux per individuare i manifesti Kubernetes. |
| `--git-readonly` | Il repository git verrà considerato di sola lettura. Il flusso non tenterà di scrivervi. |
| `--manifest-generation`  | Se abilitato, Flux cercherà solo .flux.yaml ed eseguirà Kustomize o altri generatori di manifesti. |
| `--git-poll-interval`  | Periodo in cui eseguire il polling del repository Git per i nuovi commit. Il valore predefinito è `5m` (5 minuti). |
| `--sync-garbage-collection`  | Se abilitato, Flux eliminerà le risorse create, che tuttavia non saranno più presenti in Git. |
| `--git-label`  | Etichetta per tenere traccia dello stato di avanzamento della sincronizzazione. Usato per contrassegnare il ramo git.  Il valore predefinito è `flux-sync`. |
| `--git-user`  | Nome utente per il commit Git. |
| `--git-email`  | Messaggio di posta elettronica da usare per il commit Git. 

Se non si vuole che Flux scriva nel repository e `--git-user` o `--git-email` non sia impostato, `--git-readonly` verrà impostato automaticamente.

Per ulteriori informazioni, vedere la [documentazione di Flux](https://aka.ms/FluxcdReadme).

> [!TIP]
> È possibile creare una configurazione nel portale di Azure nella scheda **GitOps** della risorsa Kubernetes di Azure Arc abilitata.

## <a name="validate-the-configuration"></a>Convalidare la configurazione

Usare l'interfaccia della riga di comando di Azure per verificare che la configurazione sia stata creata correttamente.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

La risorsa di configurazione verrà aggiornata con lo stato di conformità, i messaggi e le informazioni di debug.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Quando si crea o si aggiorna una configurazione, si verificano alcuni aspetti:

1. Azure Arc `config-agent` monitora Azure Resource Manager per le configurazioni nuove o aggiornate ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) e rileva la nuova `Pending` configurazione.
1. `config-agent`Legge le proprietà di configurazione e crea lo spazio dei nomi di destinazione.
1. Azure Arc `controller-manager` Crea un account del servizio Kubernetes e ne esegue il mapping a [ClusterRoleBinding o Roley](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) per le autorizzazioni appropriate `cluster` o l' `namespace` ambito. Viene quindi distribuita un'istanza di `flux` .
1. Se si usa l'opzione SSH con chiavi generate da Flux, `flux` genera una chiave SSH e registra la chiave pubblica.
1. `config-agent`Segnala lo stato di nuovo alla risorsa di configurazione in Azure.

Durante il processo di provisioning, la risorsa di configurazione passa attraverso alcune modifiche di stato. Monitorare lo stato di avanzamento con il comando `az k8s-configuration show ...` precedente:

| Modifica della fase | Descrizione |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Rappresenta gli stati iniziali e in corso. |
| `complianceStatus` -> `Installed`  | `config-agent` il cluster è stato configurato e distribuito `flux` senza errori. |
| `complianceStatus` -> `Failed` | `config-agent` errore durante la distribuzione di `flux` . I dettagli sono disponibili nel `complianceStatus.message` corpo della risposta. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Applicare la configurazione da un repository git privato

Se si usa un repository git privato, è necessario configurare la chiave pubblica SSH nel repository. Si specifica o Flux genera la chiave pubblica SSH. È possibile configurare la chiave pubblica nel repository git specifico o nell'utente git che ha accesso al repository. 

### <a name="get-your-own-public-key"></a>Ottenere la chiave pubblica

Se sono state generate chiavi SSH personalizzate, si hanno già le chiavi pubbliche e private.

#### <a name="get-the-public-key-using-azure-cli"></a>Ottenere la chiave pubblica usando l'interfaccia della riga di comando di Azure 

Usare il comando seguente nell'interfaccia della riga di comando di Azure se Flux genera le chiavi.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Ottenere la chiave pubblica dal portale di Azure

Esaminare quanto segue in portale di Azure se Flux genera le chiavi.

1. Nel portale di Azure, passare alla risorsa cluster connessa.
2. Nella pagina delle risorse selezionare "GitOps" e visualizzare l'elenco delle configurazioni per il cluster.
3. Selezionare la configurazione usata dal repository Git privato.
4. Nella parte inferiore della finestra di contesto visualizzata, copiare la **chiave pubblica del repository**.

#### <a name="add-public-key-using-github"></a>Aggiungere la chiave pubblica usando GitHub

Usare una delle seguenti opzioni:

* Opzione 1: aggiungere la chiave pubblica all'account utente (si applica a tutti i repository nell'account):  
    1. Aprire GitHub e fare clic sull'icona del profilo nell'angolo superiore destro della pagina.
    2. Fare clic su **Settings** (Impostazioni).
    3. Fare clic su **chiavi SSH e GPG**.
    4. Fare clic su **nuova chiave SSH**.
    5. Fornire un titolo.
    6. Incollare la chiave pubblica senza le virgolette circostanti.
    7. Fare clic su **Aggiungi chiave SSH**.

* Opzione 2: aggiungere la chiave pubblica come chiave di distribuzione al repository git (si applica solo a questo repository):  
    1. Aprire GitHub e passare al repository.
    1. Fare clic su **Settings** (Impostazioni).
    1. Fare clic su **Distribuisci chiavi**.
    1. Fare clic su **Aggiungi chiave deploy**.
    1. Fornire un titolo.
    1. Selezionare **Consenti accesso in scrittura**.
    1. Incollare la chiave pubblica senza le virgolette circostanti.
    1. Fare clic su **Aggiungi chiave**.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Aggiungere una chiave pubblica usando un repository Azure DevOps

Usare la procedura seguente per aggiungere la chiave alle chiavi SSH:

1. In **impostazioni utente** in alto a destra, accanto all'immagine del profilo, fare clic su **chiavi pubbliche SSH**.
1. Selezionare  **+ nuova chiave**.
1. Specificare un nome.
1. Incollare la chiave pubblica senza le virgolette circostanti.
1. Fare clic su **Aggiungi**.

## <a name="validate-the-kubernetes-configuration"></a>Convalidare la configurazione di Kubernetes

Dopo aver `config-agent` installato l' `flux` istanza, le risorse contenute nel repository git dovrebbero iniziare a fluire nel cluster. Verificare che gli spazi dei nomi, le distribuzioni e le risorse siano stati creati con il comando seguente:

```console
kubectl get ns --show-labels
```

```output
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

È possibile osservare che spazi dei nomi `team-a`, `team-b`, `itops`e `cluster-config` sono stati creati.

L' `flux` operatore è stato distribuito `cluster-config` nello spazio dei nomi, come indicato dalla risorsa di configurazione:

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Ulteriore approfondimento

È possibile esplorare le altre risorse distribuite come parte del repository di configurazione usando:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare una configurazione usando l'interfaccia della riga di comando di Azure o portale di Azure. Dopo aver eseguito il comando DELETE, la risorsa di configurazione verrà eliminata immediatamente in Azure. L'eliminazione completa degli oggetti associati dal cluster dovrebbe essere eseguita entro 10 minuti. Se la configurazione si trova in uno stato di errore quando viene rimossa, l'eliminazione completa degli oggetti associati può richiedere fino a un'ora.

Quando `namespace` viene eliminata una configurazione con ambito, lo spazio dei nomi non viene eliminato da Azure Arc per evitare di suddividere i carichi di lavoro esistenti. Se necessario, è possibile eliminare questo spazio dei nomi manualmente usando `kubectl` .

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Eventuali modifiche apportate al cluster che erano il risultato delle distribuzioni dal repository git rilevato non vengono eliminate al momento dell'eliminazione della configurazione.

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva per informazioni su come implementare CI/CD con GitOps.
> [!div class="nextstepaction"]
> [Implementare CI/CD con GitOps](./tutorial-gitops-ci-cd.md)