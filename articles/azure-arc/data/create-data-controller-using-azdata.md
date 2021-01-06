---
title: Creare un controller dati usando l'interfaccia della riga di comando di Azure Data (azdata)
description: Creare un controller di dati di Azure Arc in un cluster Kubernetes multinodo tipico che è già stato creato con l'interfaccia della riga di comando di Azure Data CLI (azdata).
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 19451fb09919238a04ac953c9c38fc70b4744d16
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955298"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Creare un controller di dati di Azure ARC usando il [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

Vedere l'argomento [creare il controller dati di Azure Arc](create-data-controller.md) per informazioni generali.

Per creare il controller di dati di Azure ARC usando il [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] è necessario che sia [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] installato.

   [Installare il [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Indipendentemente dalla piattaforma di destinazione scelta, sarà necessario impostare le variabili di ambiente seguenti prima della creazione dell'utente amministratore del controller di dati. È possibile fornire queste credenziali ad altre persone che devono disporre dell'accesso come amministratore al controller dati in base alle esigenze.

**AZDATA_USERNAME** : nome utente desiderato per l'utente amministratore del controller di dati. Esempio: `arcadmin`

**AZDATA_PASSWORD** : una password di propria scelta per l'utente amministratore del controller di dati. La password deve avere una lunghezza di almeno otto caratteri e contenere caratteri di tre dei quattro set seguenti: lettere maiuscole, lettere minuscole, numeri e simboli.

### <a name="linux-or-macos"></a>Linux o macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Sarà necessario connettersi ed eseguire l'autenticazione a un cluster Kubernetes e selezionare un contesto Kubernetes esistente prima di iniziare la creazione del controller di dati di Azure Arc. La modalità di connessione a un cluster o un servizio Kubernetes varia. Vedere la documentazione per la distribuzione o il servizio Kubernetes usato per la connessione al server dell'API Kubernetes.

È possibile verificare che sia presente una connessione Kubernetes corrente e verificare il contesto corrente con i comandi seguenti.

```console
kubectl get namespace
kubectl config current-context
```

### <a name="connectivity-modes"></a>Modalità di connettività

Come descritto in [modalità e requisiti di connettività](https://docs.microsoft.com/azure/azure-arc/data/connectivity), è possibile distribuire il controller dati di Azure Arc con la `direct` modalità di `indirect` connettività o. Con la `direct` modalità di connettività, i dati di utilizzo vengono inviati automaticamente e continuamente ad Azure. In questo articolo gli esempi specificano la `direct` modalità di connettività, come indicato di seguito:

   ```console
   --connectivity-mode direct
   ```

   Per creare il controller con la `indirect` modalità di connettività, aggiornare gli script nell'esempio come specificato di seguito:

   ```console
   --connectivity-mode indirect
   ```

#### <a name="create-service-principal"></a>Creare un'entità servizio

Se si distribuisce il controller di dati di Azure Arc con la `direct` modalità di connettività, le credenziali dell'entità servizio sono necessarie per la connettività di Azure. L'entità servizio viene usata per caricare i dati di utilizzo e di metrica. 

Seguire questi comandi per creare le metriche di caricamento dell'entità servizio:

> [!NOTE]
> Per la creazione di un'entità servizio sono necessarie [determinate autorizzazioni in Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Per creare un'entità servizio, aggiornare l'esempio seguente. Sostituire `<ServicePrincipalName>` con il nome dell'entità servizio ed eseguire il comando:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

Se l'entità servizio è stata creata in precedenza ed è sufficiente ottenere le credenziali correnti, eseguire il comando seguente per reimpostare le credenziali.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Ad esempio, per creare un'entità servizio denominata `azure-arc-metrics` , eseguire il comando seguente

```console
az ad sp create-for-rbac --name azure-arc-metrics
```

Output di esempio:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Salvare i `appId` `password` valori, e `tenant` in una variabile di ambiente per usarli in seguito. 

#### <a name="save-environment-variables-in-windows"></a>Salva variabili di ambiente in Windows

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

#### <a name="save-environment-variables-in-linux-or-macos"></a>Salvare le variabili di ambiente in Linux o macOS

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

#### <a name="save-environment-variables-in-powershell"></a>Salvare le variabili di ambiente in PowerShell

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
$Env:SPN_AUTHORITY="https://login.microsoftonline.com"
```

Dopo aver creato l'entità servizio, assegnare l'entità servizio al ruolo appropriato. 

### <a name="assign-roles-to-the-service-principal"></a>Assegnare i ruoli all'entità servizio

Eseguire questo comando per assegnare l'entità servizio al `Monitoring Metrics Publisher` ruolo nella sottoscrizione in cui si trovano le risorse dell'istanza del database:

#### <a name="run-the-command-on-windows"></a>Eseguire il comando in Windows

> [!NOTE]
> Quando si esegue da un ambiente Windows, è necessario utilizzare le virgolette doppie per i nomi dei ruoli.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-on-linux-or-macos"></a>Eseguire il comando in Linux o macOS

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-in-powershell"></a>Eseguire il comando in PowerShell

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Con l'entità servizio assegnata al ruolo appropriato e le variabili di ambiente impostate, è possibile procedere con la creazione del controller dei dati 

## <a name="create-the-azure-arc-data-controller"></a>Creare il controller di dati di Azure Arc

> [!NOTE]
> È possibile usare un valore diverso per il `--namespace` parametro del comando azdata Arc DC create negli esempi seguenti, ma assicurarsi di usare il nome dello spazio dei nomi per `--namespace parameter` tutti gli altri comandi seguenti.

- [Crea in Azure Kubernetes Service (AKS)](#create-on-azure-kubernetes-service-aks)
- [Creazione nel motore AKS nell'hub Azure Stack](#create-on-aks-engine-on-azure-stack-hub)
- [Creazione su AKS in Azure Stack HCI](#create-on-aks-on-azure-stack-hci)
- [Crea in Azure Red Hat OpenShift (ARO)](#create-on-azure-red-hat-openshift-aro)
- [Creazione su Red Hat OpenShift container Platform (OCP)](#create-on-red-hat-openshift-container-platform-ocp)
- [Crea su Open Source, upstream Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)
- [Creare nel servizio Kubernetes di AWS elastico (EKS)](#create-on-aws-elastic-kubernetes-service-eks)
- [Creazione sul servizio motore di Google Cloud Kubernetes (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Crea in Azure Kubernetes Service (AKS)

Per impostazione predefinita, il profilo di distribuzione AKS usa la `managed-premium` classe di archiviazione. La `managed-premium` classe di archiviazione funzionerà solo se sono presenti macchine virtuali distribuite usando immagini di VM con dischi Premium.

Se si intende usare `managed-premium` come classe di archiviazione, è possibile eseguire il comando seguente per creare il controller di dati. Sostituire i segnaposto nel comando con il nome del gruppo di risorse, l'ID sottoscrizione e la località di Azure.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Se non si è certi della classe di archiviazione da usare, è necessario usare la `default` classe di archiviazione supportata indipendentemente dal tipo di macchina virtuale in uso. Ma non fornirà le prestazioni più veloci.

Se si vuole usare la `default` classe di archiviazione, è possibile eseguire questo comando:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Creazione nel motore AKS nell'hub Azure Stack

Per impostazione predefinita, il profilo di distribuzione usa la `managed-premium` classe di archiviazione. La `managed-premium` classe di archiviazione funzionerà solo se sono presenti VM di lavoro distribuite usando immagini di VM con dischi Premium nell'Hub Azure stack.

È possibile eseguire il comando seguente per creare il controller dati usando la classe di archiviazione gestita-Premium:

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Se non si è certi della classe di archiviazione da usare, è necessario usare la `default` classe di archiviazione supportata indipendentemente dal tipo di macchina virtuale in uso. Nell'hub Azure Stack i dischi Premium e i dischi standard sono supportati dalla stessa infrastruttura di archiviazione. Si prevede pertanto che forniscano le stesse prestazioni generali, ma con limiti di IOPS diversi.

Se si vuole usare la `default` classe di archiviazione, è possibile eseguire questo comando.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Creazione su AKS in Azure Stack HCI

Per impostazione predefinita, il profilo di distribuzione usa una classe di archiviazione denominata `default` e il tipo di servizio `LoadBalancer` .

È possibile eseguire il comando seguente per creare il controller dati usando la `default` classe di archiviazione e il tipo di servizio `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Crea in Azure Red Hat OpenShift (ARO)

#### <a name="apply-the-scc"></a>Applicare il SCC

Prima di creare il controller dati in Azure Red Hat OpenShift, è necessario applicare specifici vincoli del contesto di sicurezza (SCC). Per la versione di anteprima, questi vincoli di sicurezza sono attenuati. Le versioni future forniranno il SCC aggiornato.

1. Scaricare il vincolo del contesto di sicurezza personalizzato (SCC). Usare uno dei seguenti: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - (Non[elaborato](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Il comando seguente Scarica Arc-data-SCC. YAML:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Crea SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Applicare l'oggetto SCC all'account del servizio.

   > [!NOTE]
   > Usare lo stesso spazio dei nomi qui e nel `azdata arc dc create` comando riportato di seguito. L'esempio è `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```


#### <a name="create-custom-deployment-profile"></a>Creare un profilo di distribuzione personalizzato

Usare il profilo `azure-arc-azure-openshift` per Azure RedHat Open Shift.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>Creare un controller dei dati

È possibile eseguire il comando seguente per creare il controller dati:

> [!NOTE]
> Usare lo stesso spazio dei nomi qui e nei `oc adm policy add-scc-to-user` comandi precedenti. L'esempio è `arc` .

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Creazione su Red Hat OpenShift container Platform (OCP)

> [!NOTE]
> Se si usa Red Hat OpenShift container Platform in Azure, è consigliabile usare la versione più recente disponibile.

#### <a name="apply-the-scc"></a>Applicare il SCC

Prima di creare il controller dati in Red Hat OCP, sarà necessario applicare specifici vincoli di contesto di sicurezza (SCC). Per la versione di anteprima, questi vincoli di sicurezza sono attenuati. Le versioni future forniranno il SCC aggiornato.

1. Scaricare il vincolo del contesto di sicurezza personalizzato (SCC). Usare uno dei seguenti: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - (Non[elaborato](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Il comando seguente Scarica Arc-data-SCC. YAML:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Crea SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Applicare l'oggetto SCC all'account del servizio.

   > [!NOTE]
   > Usare lo stesso spazio dei nomi qui e nel `azdata arc dc create` comando riportato di seguito. L'esempio è `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```

#### <a name="determine-storage-class"></a>Determinare la classe di archiviazione

Sarà inoltre necessario determinare la classe di archiviazione da utilizzare eseguendo il comando seguente.

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>Creare un profilo di distribuzione personalizzato

Creare un nuovo file del profilo di distribuzione personalizzato in base al `azure-arc-openshift` profilo di distribuzione eseguendo il comando seguente. Questo comando crea una directory `custom` nella directory di lavoro corrente e un file del profilo di distribuzione personalizzato `control.json` in tale directory.

Usare il profilo `azure-arc-openshift` per OpenShift container Platform.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>Imposta classe di archiviazione 

A questo punto, impostare la classe di archiviazione desiderata sostituendo `<storageclassname>` nel comando riportato di seguito con il nome della classe di archiviazione che si vuole usare, che è stata determinata eseguendo il `kubectl get storageclass` comando precedente.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>Set LoadBalancer (facoltativo)

Per impostazione predefinita, il `azure-arc-openshift` profilo di distribuzione USA `NodePort` come tipo di servizio. Se si usa un cluster OpenShift integrato con un servizio di bilanciamento del carico, è possibile modificare la configurazione per usare il `LoadBalancer` tipo di servizio usando il comando seguente:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="verify-security-policies"></a>Verificare i criteri di sicurezza

Quando si usa OpenShift, è possibile eseguire con i criteri di sicurezza predefiniti in OpenShift o si vuole bloccare in genere l'ambiente in modo più che tipico. Facoltativamente, è possibile scegliere di disabilitare alcune funzionalità per ridurre al minimo le autorizzazioni necessarie in fase di distribuzione e in fase di esecuzione eseguendo i comandi seguenti.

Questo comando Disabilita le raccolte di metriche relative ai pod. Se questa funzionalità viene disabilitata, non sarà possibile visualizzare le metriche sui Pod nei dashboard di Grafana. Il valore predefinito è true.

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

Questo comando Disabilita le raccolte di metriche sui nodi. Se questa funzionalità viene disabilitata, non sarà possibile visualizzare le metriche sui nodi nei dashboard Grafana. Il valore predefinito è true.

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

Questo comando Disabilita la possibilità di eseguire i dump della memoria per la risoluzione dei problemi.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

#### <a name="create-data-controller"></a>Creare un controller dei dati

A questo punto è possibile creare il controller dati usando il comando seguente.

> [!NOTE]
>   Usare lo stesso spazio dei nomi qui e nei `oc adm policy add-scc-to-user` comandi precedenti. L'esempio è `arc` .

> [!NOTE]
>   Il `--path` parametro deve puntare alla _directory_ che contiene il control.jsnel file non al control.jsnel file stesso.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Crea su Open Source, upstream Kubernetes (kubeadm)

Per impostazione predefinita, il profilo di distribuzione kubeadm usa una classe di archiviazione denominata `local-storage` e il tipo di servizio `NodePort` . Se questo è accettabile, è possibile ignorare le istruzioni riportate di seguito per impostare la classe di archiviazione e il tipo di servizio desiderati ed eseguire immediatamente il `azdata arc dc create` comando riportato di seguito.

Se si desidera personalizzare il profilo di distribuzione per specificare una classe di archiviazione e/o un tipo di servizio specifici, iniziare creando un nuovo file del profilo di distribuzione personalizzato in base al profilo di distribuzione kubeadm eseguendo il comando seguente. Questo comando creerà una directory `custom` nella directory di lavoro corrente e un file del profilo di distribuzione personalizzato `control.json` in tale directory.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

È possibile cercare le classi di archiviazione disponibili eseguendo il comando seguente.

```console
kubectl get storageclass
```

A questo punto, impostare la classe di archiviazione desiderata sostituendo `<storageclassname>` nel comando riportato di seguito con il nome della classe di archiviazione che si vuole usare, che è stata determinata eseguendo il `kubectl get storageclass` comando precedente.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Per impostazione predefinita, il profilo di distribuzione kubeadm utilizza `NodePort` come tipo di servizio. Se si usa un cluster Kubernetes integrato con un servizio di bilanciamento del carico, è possibile modificare la configurazione usando il comando seguente.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

A questo punto è possibile creare il controller dati usando il comando seguente.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Creare nel servizio Kubernetes di AWS elastico (EKS)

Per impostazione predefinita, la classe di archiviazione EKS è `gp2` e il tipo di servizio è `LoadBalancer` .

Eseguire il comando seguente per creare il controller dati usando il profilo di distribuzione EKS fornito.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Creazione sul servizio motore di Google Cloud Kubernetes (GKE)

Per impostazione predefinita, la classe di archiviazione GKE è `standard` e il tipo di servizio è `LoadBalancer` .

Eseguire il comando seguente per creare il controller dati usando il profilo di distribuzione GKE fornito.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Dopo aver eseguito il comando, continuare con il [monitoraggio dello stato di creazione](#monitoring-the-creation-status).

## <a name="monitoring-the-creation-status"></a>Monitoraggio dello stato di creazione

Il completamento della creazione del controller potrebbe richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento in un'altra finestra del terminale con i comandi seguenti:

> [!NOTE]
>  I comandi di esempio seguenti presuppongono che siano stati creati un controller dati e uno spazio dei nomi Kubernetes con il nome `arc` . Se è stato utilizzato un nome diverso per lo spazio dei nomi/controller dati, è possibile sostituire `arc` con il nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

È anche possibile controllare lo stato di creazione di un particolare pod eseguendo un comando come riportato di seguito. Questa operazione è particolarmente utile per la risoluzione di eventuali problemi.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Risoluzione dei problemi di creazione

Se si verificano problemi durante la creazione, vedere la [Guida alla risoluzione dei problemi](troubleshoot-guide.md).
