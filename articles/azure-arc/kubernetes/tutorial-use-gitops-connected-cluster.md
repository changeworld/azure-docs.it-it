---
title: 'Esercitazione: Distribuire configurazioni con GitOps in un cluster Kubernetes Azure Arc abilitato'
description: Questa esercitazione illustra l'applicazione di configurazioni in Azure Arc cluster Kubernetes abilitato. Per un'analisi concettuale di questo processo, vedere l'articolo Configurazioni e GitOps - Azure Arc Kubernetes abilitato.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial , devx-track-azurecli
ms.openlocfilehash: 0f1172ffa0d29734e7ec005bf2812eeca215aa0d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484167"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Esercitazione: Distribuire configurazioni con GitOps in un cluster Kubernetes Azure Arc abilitato 

In questa esercitazione si applicheranno le configurazioni usando GitOps in Azure Arc cluster Kubernetes abilitato. Si apprenderà come:

> [!div class="checklist"]
> * Creare una configurazione in un cluster Kubernetes Azure Arc abilitato usando un repository Git di esempio.
> * Verificare che la configurazione sia stata creata correttamente.
> * Applicare la configurazione da un repository Git privato.
> * Convalidare la configurazione di Kubernetes.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un cluster Azure Arc cluster connesso Kubernetes abilitato.
    - Se non è ancora stato connesso un cluster, vedere la guida introduttiva Connettere un [cluster Kubernetes](quickstart-connect-cluster.md)Azure Arc abilitato.
- Conoscenza dei vantaggi e dell'architettura di questa funzionalità. Per altre informazioni, [vedere l'articolo Configurazioni e GitOps - Azure Arc kubernetes abilitato.](conceptual-configurations.md)
- Installare l'estensione dell'interfaccia della riga di comando `k8s-configuration` di Azure >= 1.0.0:
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

    >[!TIP]
    > Se `k8s-configuration` l'estensione è già installata, è possibile aggiornarla alla versione più recente usando il comando seguente: `az extension update --name k8s-configuration`

## <a name="create-a-configuration"></a>Creare una configurazione

Il [repository di esempio](https://github.com/Azure/arc-k8s-demo) usato in questo articolo è strutturato in base alla persona di un operatore cluster. I manifesti in questo repository effettuano il provisioning di alcuni spazi dei nomi, distribuiscono i carichi di lavoro e forniscono alcune configurazioni specifiche del team. L'uso di questo repository con GitOps crea le risorse seguenti nel cluster:

* Spazi dei nomi: `cluster-config` , `team-a` , `team-b`
* Distribuzione: `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

Esegue `config-agent` il polling di Azure per le configurazioni nuove o aggiornate. Questa attività può richiedere fino a 30 secondi.

Se si associa un repository privato alla configurazione, completare la procedura seguente in [Applicare la configurazione da un repository Git privato.](#apply-configuration-from-a-private-git-repository)

## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Usare l'estensione dell'interfaccia della riga di comando di Azure `k8s-configuration` per per collegare un cluster connesso al repository Git di [esempio.](https://github.com/Azure/arc-k8s-demo) 
1. Assegnare a questa configurazione il nome `cluster-config` .
1. Indicare all'agente di distribuire l'operatore nello spazio `cluster-config` dei nomi .
1. Concedere all'operatore `cluster-admin` le autorizzazioni.

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

### <a name="use-a-public-git-repository"></a>Usare un repository Git pubblico

| Parametro | Format |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] o git://server/repo[.git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Usare un repository Git privato con chiavi SSH e create da Flux

Aggiungere la chiave pubblica generata da Flux all'account utente nel provider di servizi Git. Se la chiave viene aggiunta al repository anziché all'account utente, usare `git@` al posto di `user@` nell'URL. 

Passare alla [sezione Applica configurazione da un repository Git privato](#apply-configuration-from-a-private-git-repository) per altri dettagli.


| Parametro | Format | Note
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] o user@server:repo [.git] | `git@` può sostituire `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Usare un repository Git privato con SSH e chiavi fornite dall'utente

Fornire la propria chiave privata direttamente o in un file. La chiave deve essere in [formato PEM e](https://aka.ms/PEMformat) terminare con una nuova riga (\n). 

Aggiungere la chiave pubblica associata all'account utente nel provider di servizi Git. Se la chiave viene aggiunta al repository anziché all'account utente, usare `git@` al posto di `user@` . 

Per altri [dettagli, passare alla](#apply-configuration-from-a-private-git-repository) sezione Applicare la configurazione da un repository Git privato.  

| Parametro | Format | Note |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] o user@server:repo [.git] | `git@` può sostituire `user@` |
| `--ssh-private-key` | Chiave con codifica base64 in [formato PEM](https://aka.ms/PEMformat) | Fornire direttamente la chiave |
| `--ssh-private-key-file` | percorso completo del file locale | Specificare il percorso completo del file locale che contiene la chiave in formato PEM

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Usare un host Git privato con SSH e host noti forniti dall'utente

L'operatore Flux gestisce un elenco di host Git comuni nel file hosts noto per autenticare il repository Git prima di stabilire la connessione SSH. Se si usa un repository *Git* non comune o il proprio host Git, è possibile specificare la chiave host in modo che Flux possa identificare il repository. 

Proprio come le chiavi private, è possibile fornire il known_hosts contenuto direttamente o in un file. Quando si forniscono contenuti personalizzati, usare le specifiche [known_hosts formato](https://aka.ms/KnownHostsFormat)del contenuto, insieme a uno degli scenari chiave SSH precedenti.

| Parametro | Format | Note |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] o user@server:repo [.git] | `git@` può sostituire `user@` |
| `--ssh-known-hosts` | Codifica base64 | Fornire direttamente il contenuto degli host noti |
| `--ssh-known-hosts-file` | percorso completo del file locale | Fornire il contenuto degli host noti in un file locale |

### <a name="use-a-private-git-repository-with-https"></a>Usare un repository Git privato con HTTPS

| Parametro | Format | Note |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[.git] | HTTPS con autenticazione di base |
| `--https-user` | non elaborato o con codifica Base64 | Nome utente HTTPS |
| `--https-key` | non elaborato o con codifica Base64 | Password o token di accesso personale HTTPS

>[!NOTE]
>* Il grafico dell'operatore Helm versione 1.2.0+ supporta l'autenticazione privata della versione di Helm HTTPS.
>* La versione helm HTTPS non è supportata per i cluster gestiti del servizio Web Disatteso.
>* Se è necessario Flux per accedere al repository Git tramite il proxy, è necessario aggiornare gli agenti Azure Arc con le impostazioni del proxy. Per altre informazioni, vedere [Connettersi tramite un server proxy in uscita.](./quickstart-connect-cluster.md#connect-using-an-outbound-proxy-server)


## <a name="additional-parameters"></a>Parametri aggiuntivi

Personalizzare la configurazione con i parametri facoltativi seguenti:

| Parametro | Descrizione |
| ------------- | ------------- |
| `--enable-helm-operator`| Passare a per abilitare il supporto per le distribuzioni di grafici Helm. |
| `--helm-operator-params` | Valori del grafico per l'operatore Helm (se abilitato). Ad esempio: `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Versione del grafico per l'operatore Helm (se abilitata). Usare la versione 1.2.0+. Impostazione predefinita: '1.2.0'. |
| `--operator-namespace` | Nome dello spazio dei nomi dell'operatore. Impostazione predefinita: 'default'. Massimo: 23 caratteri. |
| `--operator-params` | Parametri per l'operatore . Deve essere specificato tra virgolette singole. Ad esempio, usare ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>Opzioni supportate in  `--operator-params` :

| Opzione | Descrizione |
| ------------- | ------------- |
| `--git-branch`  | Ramo del repository Git da usare per i manifesti Kubernetes. Il valore predefinito è "master". I repository più nuovi hanno un ramo radice denominato , nel qual `main` caso è necessario impostare `--git-branch=main` . |
| `--git-path`  | Percorso relativo all'interno del repository Git per Flux per individuare i manifesti Kubernetes. |
| `--git-readonly` | Il repository Git verrà considerato di sola lettura. Flux non tenterà di scrivere in esso. |
| `--manifest-generation`  | Se abilitato, Flux cercherà solo .flux.yaml ed eseguirà Kustomize o altri generatori di manifesti. |
| `--git-poll-interval`  | Periodo in cui eseguire il polling del repository Git per i nuovi commit. Il valore predefinito `5m` è (5 minuti). |
| `--sync-garbage-collection`  | Se abilitato, Flux eliminerà le risorse create, che tuttavia non saranno più presenti in Git. |
| `--git-label`  | Etichetta per tenere traccia dello stato di avanzamento della sincronizzazione. Usato per contrassegnare il ramo Git.  Il valore predefinito è `flux-sync`. |
| `--git-user`  | Nome utente per il commit Git. |
| `--git-email`  | Messaggio di posta elettronica da usare per il commit Git. 

Se non si vuole che Flux scrivo nel repository e o non sia impostato, verrà impostato `--git-user` `--git-email` `--git-readonly` automaticamente.

Per altre informazioni, vedere la [documentazione di Flux.](https://aka.ms/FluxcdReadme)

> [!TIP]
> È possibile creare una configurazione nel portale di Azure nella scheda **GitOps** della risorsa Kubernetes Azure Arc abilitata.

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

Quando viene creata o aggiornata una configurazione, si verificano alcune operazioni:

1. Il Azure Arc monitora Azure Resource Manager le configurazioni nuove o aggiornate ( ) e `config-agent` `Microsoft.KubernetesConfiguration/sourceControlConfigurations` nota la nuova `Pending` configurazione.
1. legge `config-agent` le proprietà di configurazione e crea lo spazio dei nomi di destinazione.
1. Il Azure Arc crea un account del servizio Kubernetes ed esegue il mapping a `controller-manager` [ClusterRoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) o RoleBinding per le autorizzazioni appropriate ( `cluster` o `namespace` ambito). Distribuisce quindi un'istanza di `flux` .
1. Se si usa l'opzione SSH con chiavi generate da Flux, genera una `flux` chiave SSH e registra la chiave pubblica.
1. Lo `config-agent` stato viene visualizzato di nuovo nella risorsa di configurazione in Azure.

Durante il processo di provisioning, la risorsa di configurazione passa attraverso alcune modifiche di stato. Monitorare lo stato di avanzamento con il comando `az k8s-configuration show ...` precedente:

| Modifica della fase | Descrizione |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Rappresenta gli stati iniziale e in corso. |
| `complianceStatus` -> `Installed`  | `config-agent` il cluster è stato configurato correttamente e distribuito `flux` senza errori. |
| `complianceStatus` -> `Failed` | `config-agent` si è verificato un errore durante la distribuzione di `flux` . I dettagli vengono forniti nel `complianceStatus.message` corpo della risposta. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Applicare la configurazione da un repository Git privato

Se si usa un repository Git privato, è necessario configurare la chiave pubblica SSH nel repository. Se si specifica o Flux genera la chiave pubblica SSH. È possibile configurare la chiave pubblica nel repository Git specifico o nell'utente Git che ha accesso al repository. 

### <a name="get-your-own-public-key"></a>Ottenere la propria chiave pubblica

Se sono state generate chiavi SSH, sono già presenti le chiavi private e pubbliche.

#### <a name="get-the-public-key-using-azure-cli"></a>Ottenere la chiave pubblica usando l'interfaccia della riga di comando di Azure 

Se Flux genera le chiavi, usare quanto segue nell'interfaccia della riga di comando di Azure.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Ottenere la chiave pubblica dal portale di Azure

Seguire questa procedura per portale di Azure se Flux sta generando le chiavi.

1. Nel portale di Azure, passare alla risorsa cluster connessa.
2. Nella pagina della risorsa selezionare "GitOps" e visualizzare l'elenco delle configurazioni per questo cluster.
3. Selezionare la configurazione usata dal repository Git privato.
4. Nella parte inferiore della finestra di contesto visualizzata, copiare la **chiave pubblica del repository**.

#### <a name="add-public-key-using-github"></a>Aggiungere una chiave pubblica con GitHub

Usare una delle seguenti opzioni:

* Opzione 1: aggiungere la chiave pubblica all'account utente (si applica a tutti i repository nell'account):  
    1. Aprire GitHub e fare clic sull'icona del profilo nell'angolo superiore destro della pagina.
    2. Fare clic su **Settings** (Impostazioni).
    3. Fare clic sulle **chiavi SSH e GPG**.
    4. Fare clic **su Nuova chiave SSH.**
    5. Specificare un titolo.
    6. Incollare la chiave pubblica senza virgolette racchiuse tra virgolette.
    7. Fare clic **su Add SSH key (Aggiungi chiave SSH).**

* Opzione 2: aggiungere la chiave pubblica come chiave di distribuzione al repository Git (si applica solo a questo repository):  
    1. Aprire GitHub e passare al repository.
    1. Fare clic su **Settings** (Impostazioni).
    1. Fare clic **su Distribuisci chiavi**.
    1. Fare clic su **Aggiungi chiave di distribuzione**.
    1. Specificare un titolo.
    1. Selezionare **Consenti accesso in scrittura**.
    1. Incollare la chiave pubblica senza virgolette racchiuse tra virgolette.
    1. Fare clic **su Aggiungi chiave.**

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Aggiungere una chiave pubblica usando un repository Azure DevOps

Usare la procedura seguente per aggiungere la chiave alle chiavi SSH:

1. In **User Settings (Impostazioni** utente) in alto a destra (accanto all'immagine del profilo) fare clic su **SSH public keys (Chiavi pubbliche SSH).**
1. Selezionare **+ Nuova chiave.**
1. Specificare un nome.
1. Incollare la chiave pubblica senza virgolette circostanti.
1. Fare clic su **Aggiungi**.

## <a name="validate-the-kubernetes-configuration"></a>Convalidare la configurazione di Kubernetes

Dopo `config-agent` aver installato `flux` l'istanza, le risorse presenti nel repository Git dovrebbero iniziare a fluire al cluster. Verificare che gli spazi dei nomi, le distribuzioni e le risorse siano stati creati con il comando seguente:

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

`flux`L'operatore è stato distribuito nello spazio `cluster-config` dei nomi, come indicato dalla risorsa di configurazione:

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

Eliminare una configurazione usando l'interfaccia della riga di comando di Azure o portale di Azure. Dopo aver eseguito il comando delete, la risorsa di configurazione verrà eliminata immediatamente in Azure. L'eliminazione completa degli oggetti associati dal cluster deve avvenire entro 10 minuti. Se la configurazione si trova in uno stato di errore quando viene rimossa, l'eliminazione completa degli oggetti associati può richiedere fino a un'ora.

Quando viene eliminata una configurazione con ambito, lo spazio dei nomi non viene eliminato Azure Arc per evitare `namespace` l'interruzione dei carichi di lavoro esistenti. Se necessario, è possibile eliminare manualmente questo spazio dei nomi usando `kubectl` .

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Le modifiche al cluster risultanti dalle distribuzioni dal repository Git monitorato non vengono eliminate quando la configurazione viene eliminata.

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva per informazioni su come implementare CI/CD con GitOps.
> [!div class="nextstepaction"]
> [Implementare CI/CD con GitOps](./tutorial-gitops-ci-cd.md)
