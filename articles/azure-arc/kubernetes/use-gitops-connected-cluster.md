---
title: Distribuire le configurazioni usando GitOps nei cluster Kubernetes con abilitazione di Azure Arc (anteprima)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Usare GitOps per configurare un cluster Kubernetes abilitato per Azure Arc (anteprima)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes Service, AKS, container
ms.openlocfilehash: 72dc42fffb3653de81477fa504c11b9b0328d2eb
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988707"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Distribuire le configurazioni usando GitOps nei cluster Kubernetes con abilitazione di Azure Arc (anteprima)

GitOps, in relazione a Kubernetes, è la pratica di dichiarare lo stato desiderato della configurazione Kubernetes (distribuzioni, spazi dei nomi e così via) in un repository git seguito da una distribuzione di polling e basata su pull di queste configurazioni nel cluster tramite un operatore. Questo documento illustra la configurazione di tali flussi di lavoro nei cluster Kubernetes abilitati per Azure Arc.

La connessione tra il cluster e un repository Git viene creata in Azure Resource Manager come `Microsoft.KubernetesConfiguration/sourceControlConfigurations` risorsa di estensione. Le proprietà della risorsa `sourceControlConfiguration` rappresentano la posizione e la modalità di flusso delle risorse Kubernetes da Git al cluster. I `sourceControlConfiguration` dati vengono archiviati crittografati inattivi in un database di Azure Cosmos DB per garantire la riservatezza dei dati.

L'oggetto `config-agent` in esecuzione nel cluster è responsabile dell'osservazione delle risorse di estensione nuove o aggiornate `sourceControlConfiguration` nella risorsa Kubernetes abilitata per Azure Arc, per la distribuzione di un operatore Flux per il controllo dell'archivio git `sourceControlConfiguration` e l'applicazione di tutti gli aggiornamenti apportati `sourceControlConfiguration` . È possibile creare più risorse nello `sourceControlConfiguration` stesso cluster Kubernetes abilitato per Azure Arc per ottenere il multi-tenant. È possibile creare ognuno `sourceControlConfiguration` con un `namespace` ambito diverso per limitare le distribuzioni a all'interno dei rispettivi spazi dei nomi.

Il repository Git può contenere manifesti in formato YAML che descrivono tutte le risorse Kubernetes valide, tra cui spazi dei nomi, ConfigMaps, distribuzioni, gli elementi daemonset e così via.  Potrebbe inoltre contenere grafici Helm per la distribuzione di applicazioni. Un set di scenari comune prevede la definizione di una configurazione di base per l'organizzazione, che può includere i ruoli e le associazioni comuni di Azure, gli agenti di monitoraggio o registrazione o i servizi a livello di cluster.

Lo stesso modello può essere usato per gestire una raccolta di cluster più ampia, che può essere distribuita in ambienti eterogenei. Ad esempio, è possibile disporre di un unico repository che definisce la configurazione di base per l'organizzazione e applicarlo a decine di cluster Kubernetes contemporaneamente. [Criteri di Azure consente di automatizzare](use-azure-policy.md) la creazione di un oggetto `sourceControlConfiguration` con un set specifico di parametri in tutte le risorse Kubernetes abilitate per Azure Arc in un ambito (sottoscrizione o gruppo di risorse).

Questa guida introduttiva illustra come applicare un set di configurazioni con ambito amministrazione del cluster.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che sia già disponibile un cluster connesso Kubernetes con abilitazione di Azure Arc. Se è necessario un cluster connesso, vedere l'[avvio rapido di connessione di un cluster](./connect-cluster.md).

## <a name="create-a-configuration"></a>Creare una configurazione

Il [repository di esempio](https://github.com/Azure/arc-k8s-demo) usato in questo documento è strutturato intorno all'utente di un operatore cluster che desidera effettuare il provisioning di alcuni spazi dei nomi, distribuire un carico di lavoro comune e fornire una configurazione specifica del team. L'uso di questo repository crea nel cluster le risorse seguenti:

**Spazi dei nomi:** `cluster-config`, `team-a`, `team-b`
**Distribuzione:** `cluster-config/azure-vote`
**ConfigMap:** `team-a/endpoints`

Il esegue il `config-agent` polling di Azure per la nuova o l'aggiornamento `sourceControlConfiguration` ogni 30 secondi, ovvero il tempo massimo impiegato da `config-agent` per selezionare una configurazione nuova o aggiornata.
Se si associa un repository privato con `sourceControlConfiguration` , assicurarsi di completare anche i passaggi in [applicare la configurazione da un repository git privato](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Usare l'estensione dell'interfaccia della riga di comando di Azure per per `k8sconfiguration` collegare un cluster connesso al [repository git di esempio](https://github.com/Azure/arc-k8s-demo). A questa configurazione verrà assegnato un nome `cluster-config`; indicare all'agente di distribuire l'operatore nello spazio dei nomi `cluster-config` e concedere all'operatore autorizzazioni `cluster-admin`.

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

#### <a name="use-a-public-git-repo"></a>Usare un repository git pubblico

| Parametro | Format |
| ------------- | ------------- |
| --repository-URL | http [s]://server/repo [. git] o git://server/repo [. git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Usare un repository git privato con chiavi SSH e create da Flux

| Parametro | Format | Note
| ------------- | ------------- | ------------- |
| --repository-URL | ssh://user@server/repo[. git] o user@server:repo [. git] | `git@` può sostituire `user@`

> [!NOTE]
> La chiave pubblica generata da Flux deve essere aggiunta all'account utente nel provider di servizi git. Se la chiave viene aggiunta al repository anziché > account utente, usare al `git@` posto di `user@` nell'URL. [Visualizza altri dettagli](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Usare un repository git privato con SSH e chiavi fornite dall'utente

| Parametro | Format | Note |
| ------------- | ------------- | ------------- |
| --repository-URL  | ssh://user@server/repo[. git] o user@server:repo [. git] | `git@` può sostituire `user@` |
| --SSH-chiave privata | chiave con codifica Base64 in [formato PEM](https://aka.ms/PEMformat) | Fornire direttamente la chiave |
| --SSH-private-key-file | percorso completo del file locale | Specificare il percorso completo del file locale che contiene la chiave del formato PEM

> [!NOTE]
> Fornire la propria chiave privata direttamente o in un file. La chiave deve essere in [formato PEM](https://aka.ms/PEMformat) e terminare con una nuova riga (\n).  È necessario aggiungere la chiave pubblica associata all'account utente nel provider di servizi git. Se la chiave viene aggiunta al repository anziché all'account utente, usare `git@` al posto di `user@` . [Visualizza altri dettagli](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Usare un host git privato con SSH e host noti forniti dall'utente

| Parametro | Format | Note |
| ------------- | ------------- | ------------- |
| --repository-URL  | ssh://user@server/repo[. git] o user@server:repo [. git] | `git@` può sostituire `user@` |
| --host di SSH-noti | con codifica Base64 | contenuto host noto fornito direttamente |
| --SSH-known-hosts-file | percorso completo del file locale | contenuto host noto fornito in un file locale

> [!NOTE]
> L'operatore Flux gestisce un elenco di host git comuni nel file hosts noto per autenticare il repository git prima di stabilire la connessione SSH. Se si usa un repository Git non comune o un host git, potrebbe essere necessario specificare la chiave host per assicurarsi che Flux possa identificare il repository. È possibile fornire il contenuto host noto direttamente o in un file. [Visualizza la specifica del formato di contenuto host noto](https://aka.ms/KnownHostsFormat).
> Questa operazione può essere usata insieme a uno degli scenari chiave SSH descritti in precedenza.

#### <a name="use-a-private-git-repo-with-https"></a>Usare un repository git privato con HTTPS

| Parametro | Format | Note |
| ------------- | ------------- | ------------- |
| --repository-URL | https://server/repo[. git] | HTTPS con autenticazione di base |
| --https-utente | con codifica RAW o Base64 | Nome utente HTTPS |
| --https-chiave | con codifica RAW o Base64 | Token di accesso personale HTTPS o password

> [!NOTE]
> Il protocollo di autorizzazione privata della versione Helm HTTPS è supportato solo con la versione del grafico Helm operator >= 1.2.0.  Per impostazione predefinita, viene usata la versione 1.2.0.
> La versione Helm HTTPS privata non è attualmente supportata per i cluster gestiti di servizi Kubernetes di Azure.
> Se è necessario Flux per accedere al repository git tramite il proxy, sarà necessario aggiornare gli agenti di Azure Arc con le impostazioni del proxy. [Altre informazioni](./connect-cluster.md#connect-using-an-outbound-proxy-server)

#### <a name="additional-parameters"></a>Parametri aggiuntivi

Per personalizzare la configurazione, di seguito sono riportati altri parametri che è possibile usare:

`--enable-helm-operator` : *Facoltativo*: attivarlo per abilitare il supporto per le distribuzioni del grafico Helm.

`--helm-operator-params` : *Facoltativo*: valori del grafico per l'operatore Helm (se abilitato).  Ad esempio, '--set helm.versions=v3'.

`--helm-operator-version` : *Facoltativo*: versione del grafico per l'operatore Helm (se abilitato). Usare "1.2.0" o versione successiva. Impostazione predefinita: "1.2.0".

`--operator-namespace` : *Facoltativo*: nome per lo spazio dei nomi dell'operatore. Impostazione predefinita:' default '. Massimo 23 caratteri.

`--operator-params` : *Facoltativo*: parametri per l'operatore. Deve essere specificato tra virgolette singole. Ad esempio, usare ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ```

Opzioni supportate in --operator-params

| Opzione | Descrizione |
| ------------- | ------------- |
| --git-branch  | Ramo del repository git da usare per i manifesti Kubernetes. Il valore predefinito è "master". I repository più recenti hanno un ramo radice denominato ' Main ', nel qual caso è necessario impostare--git-branch = Main. |
| --git-path  | Percorso relativo all'interno del repository Git per permettere a Flux di individuare i manifesti Kubernetes. |
| --git-readonly | Il repository Git verrà considerato di sola lettura. Flux non tenterà di scrivervi. |
| --manifest-generation  | Se abilitato, Flux cercherà solo .flux.yaml ed eseguirà Kustomize o altri generatori di manifesti. |
| --git-poll-interval  | Periodo in cui eseguire il polling del repository Git per i nuovi commit. Il valore predefinito è "5m" (5 minuti). |
| --sync-garbage-collection  | Se abilitato, Flux eliminerà le risorse create, che tuttavia non saranno più presenti in Git. |
| --git-label  | Etichetta per tenere traccia dello stato di avanzamento della sincronizzazione, usata per contrassegnare il ramo Git.  Il valore predefinito è "flux-sync". |
| --git-user  | Nome utente per il commit Git. |
| --git-email  | Messaggio di posta elettronica da usare per il commit Git. |

* Se "--git-user" o "--git-email" non sono impostati (il che significa che non si vuole che Flux scriva nel repository), --git-readonly verrà impostato automaticamente (se non è già stato impostato).

Per ulteriori informazioni, vedere la [documentazione di Flux](https://aka.ms/FluxcdReadme).

> [!TIP]
> È possibile creare un sourceControlConfiguration nel portale di Azure nella scheda **GitOps** della risorsa Kubernetes di Azure Arc abilitata.

## <a name="validate-the-sourcecontrolconfiguration"></a>Convalidare sourceControlConfiguration

Usando l'interfaccia della riga di comando di Azure, verificare che `sourceControlConfiguration` sia stato creato correttamente.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Si noti che la risorsa `sourceControlConfiguration` viene aggiornata con lo stato di conformità, i messaggi e le informazioni di debug.

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

Quando `sourceControlConfiguration` si crea o si aggiorna un oggetto, si verificano alcuni aspetti dietro le quinte:

1. Azure Arc `config-agent` sta monitorando Azure Resource Manager per le configurazioni nuove o aggiornate ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) e rileva la nuova `Pending` configurazione.
1. `config-agent`Legge le proprietà di configurazione e crea lo spazio dei nomi di destinazione.
1. Azure Arc `controller-manager` prepara un account del servizio Kubernetes con l'autorizzazione `cluster` o l'ambito appropriato `namespace` , quindi distribuisce un'istanza di `flux` .
1. Se si usa l'opzione SSH con chiavi generate da Flux, `flux` genera una chiave SSH e registra la chiave pubblica.
1. `config-agent`Segnala lo stato di nuovo alla `sourceControlConfiguration` risorsa in Azure.

Durante il processo di provisioning, `sourceControlConfiguration` scorrerà alcune modifiche di stato. Monitorare lo stato di avanzamento con il comando `az k8sconfiguration show ...` precedente:

1. `complianceStatus` -> `Pending`: rappresenta gli stati iniziali e in corso
1. `complianceStatus` -> `Installed`: `config-agent` è stato in grado di configurare correttamente il cluster e distribuire `flux` senza errori
1. `complianceStatus` -> `Failed`: `config-agent` ha riscontrato un errore durante la distribuzione di `flux`; i dettagli sono disponibili nel corpo della risposta `complianceStatus.message`

## <a name="apply-configuration-from-a-private-git-repository"></a>Applicare la configurazione da un repository git privato

Se si usa un repository git privato, è necessario configurare la chiave pubblica SSH nel repository. È possibile configurare la chiave pubblica nel repository git specifico o nell'utente git che ha accesso al repository. La chiave pubblica SSH sarà quella fornita dall'utente o quella generata da Flux.

**Ottenere la chiave pubblica**

Se sono state generate chiavi SSH personalizzate, si hanno già le chiavi pubbliche e private.

**Ottenere la chiave pubblica usando l'interfaccia della riga di comando di Azure (utile se Flux genera le chiavi)**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Ottenere la chiave pubblica dal portale di Azure (utile se Flux genera le chiavi)**

1. Nel portale di Azure, passare alla risorsa cluster connessa.
2. Nella pagina delle risorse selezionare "GitOps" e visualizzare l'elenco delle configurazioni per il cluster.
3. Selezionare la configurazione usata dal repository Git privato.
4. Nella parte inferiore della finestra di contesto visualizzata, copiare la **chiave pubblica del repository**.

Se si usa GitHub, usare una delle due opzioni seguenti:

**Opzione 1: aggiungere la chiave pubblica all'account utente (si applica a tutti i repository nell'account)**

1. Aprire GitHub, fare clic sull'icona del profilo nell'angolo superiore destro della pagina.
2. Fare clic su **Impostazioni**
3. Fare clic su **chiavi SSH e GPG**
4. Fare clic su **nuova chiave SSH**
5. Fornire un titolo
6. Incollare la chiave pubblica (escluse le virgolette circostanti)
7. Fare clic su **Aggiungi chiave SSH**

**Opzione 2: aggiungere la chiave pubblica come chiave di distribuzione al repository git (si applica solo a questo repository)**

1. Aprire GitHub, passare al repository, a **Settings** e quindi per distribuire le **chiavi**
2. Fare clic su **Aggiungi chiave di distribuzione**
3. Fornire un titolo
4. Spuntare **Allow write access** (Consenti accesso in scrittura)
5. Incollare la chiave pubblica (escluse le virgolette circostanti)
6. Fare clic su **Aggiungi chiave**

**Se si usa un repository Azure DevOps, aggiungere la chiave alle chiavi SSH**

1. In **Impostazioni utente** in alto a destra, accanto all'immagine del profilo, fare clic su **Chiavi pubbliche SSH**
1. Selezionare **+ New Key** (Aggiungi nuova chiave)
1. Specificare un nome
1. Incollare la chiave pubblica, escluse le virgolette circostanti
1. Fare clic su **Aggiungi**

## <a name="validate-the-kubernetes-configuration"></a>Convalidare la configurazione di Kubernetes

Dopo aver `config-agent` installato l' `flux` istanza, le risorse contenute nel repository git dovrebbero iniziare a fluire nel cluster. Verificare che gli spazi dei nomi, le distribuzioni e le risorse siano stati creati:

```console
kubectl get ns --show-labels
```

**Output:**

```console
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

L'operatore `flux` è stato distribuito nello spazio dei nomi `cluster-config`, come indicato da `sourceControlConfig`:

```console
kubectl -n cluster-config get deploy  -o wide
```

**Output:**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Esplorazione aggiuntiva

È possibile esplorare le altre risorse distribuite come parte del repository di configurazione:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Eliminare una configurazione

Eliminare un `sourceControlConfiguration` usando l'interfaccia della riga di comando di Azure o portale di Azure.  Dopo aver avviato il comando DELETE, la `sourceControlConfiguration` risorsa verrà eliminata immediatamente in Azure e l'eliminazione completa degli oggetti associati dal cluster dovrebbe essere eseguita entro 10 minuti.  Se `sourceControlConfiguration` è in uno stato di errore quando viene eliminato, l'eliminazione completa degli oggetti associati può richiedere fino a un'ora.

> [!NOTE]
> Dopo la creazione di un sourceControlConfiguration con ambito dello spazio dei nomi, è possibile che gli utenti con `edit` Binding Role nello spazio dei nomi distribuiscano i carichi di lavoro in questo spazio dei nomi. Quando `sourceControlConfiguration` viene eliminato questo oggetto con ambito dello spazio dei nomi, lo spazio dei nomi viene lasciato intatto e non verrà eliminato per evitare di suddividere gli altri carichi di lavoro.  Se necessario, è possibile eliminare questo spazio dei nomi manualmente con kubectl.
> Qualsiasi modifica apportata al cluster risultante dalle distribuzioni dal repository git rilevato non viene eliminata quando `sourceControlConfiguration` viene eliminato.

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare Helm con la configurazione del controllo del codice sorgente](./use-gitops-with-helm.md)
- [Usare Criteri di Azure per gestire la configurazione del cluster](./use-azure-policy.md)