---
title: Distribuire il controller dati di Azure Arc | Modalità di connessione diretta
description: Viene illustrato come distribuire il controller dati in modalità di connessione diretta.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: overview
ms.openlocfilehash: 220618f167237d5937766eb5e28b9b6569cef76a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031486"
---
#  <a name="deploy-azure-arc-data-controller--direct-connect-mode"></a>Distribuire il controller dati di Azure Arc | Modalità di connessione diretta

Questo articolo descrive come distribuire il controller dati di Azure Arc in modalità di connessione diretta durante l'anteprima corrente di questa funzionalità. 

Attualmente è possibile creare il controller dati di Azure Arc da portale di Azure. Altri strumenti per Azure Arc Enabled Data Services non supportano la creazione del controller dati in modalità di connessione diretta. Per informazioni dettagliate, vedere [problemi noti-Azure Arc Enabled Data Services (anteprima)](known-issues.md).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="complete-prerequisites"></a>Prerequisiti completi

Prima di iniziare, verificare di aver completato i prerequisiti in [Distribuisci controller dati-modalità di connessione diretta-prerequisiti](deploy-data-controller-direct-mode-prerequisites.md).

Da un livello elevato, questo articolo illustra come completare queste attività:

1. Creare un'estensione di Azure Arc Enabled Data Services. 
1. Creare un percorso personalizzato.
1. Distribuire il controller dati dal portale.

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Creare un'estensione di Azure Arc Enabled Data Services

Usare l'interfaccia della riga di comando K8S-Extension per creare un'estensione di servizi dati.

### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

Impostare le variabili di ambiente seguenti che verranno usate nel passaggio successivo.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>Creare l'estensione di Arc Data Services

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

> [!NOTE]
> Per completare l'installazione dell'estensione di Arc Data Services possono essere necessari alcuni minuti.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Verificare che l'estensione di Arc Data Services sia stata creata

È possibile verificare se l'estensione di Arc Enabled Data Services viene creata dal portale o connettendosi direttamente al cluster Kubernetes abilitato per l'arco. 

#### <a name="azure-portal"></a>Portale di Azure
1. Accedere al portale di Azure e passare al gruppo di risorse in cui si trova la risorsa cluster connessa a Kubernetes.
1. Selezionare il cluster kubernetes abilitato per Arc (Type = "Kubernetes-Azure Arc") in cui è stata distribuita l'estensione.
1. Nella finestra di spostamento sul lato sinistro, in **Impostazioni**, selezionare "estensioni (anteprima)".
1. Verrà visualizzata l'estensione appena creata in precedenza in uno stato "installato".

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Dashboard estensioni":::

#### <a name="kubectl-cli"></a>INTERFACCIA della riga di comando kubectl

1. Connettersi al cluster Kubernetes tramite una finestra del terminale.
1. Eseguire il comando seguente e verificare che lo spazio dei nomi (1) indicato in precedenza sia stato creato e (2) il `bootstrapper` Pod si trovi nello stato "Running" prima di procedere al passaggio successivo.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

Il codice seguente, ad esempio, ottiene i pod dallo `arc` spazio dei nomi.

```console
#Example:
kubectl get pods -n arc
```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>Creare un percorso personalizzato usando l'estensione CLI della posizione personalizzata

Un percorso personalizzato è una risorsa di Azure equivalente a uno spazio dei nomi in un cluster Kubernetes.  I percorsi personalizzati vengono usati come destinazione per la distribuzione di risorse da o verso Azure. Scopri di più sui percorsi personalizzati nei [percorsi personalizzati nella documentazione di Azure Arc abilitata per Kubernetes](../kubernetes/conceptual-custom-locations.md).

### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus2euap
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>Convalidare la creazione del percorso personalizzato

Dal terminale eseguire il comando seguente per elencare i percorsi personalizzati e verificare che lo stato di **provisioning** sia completato:

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Creare il controller di dati di Azure Arc

Dopo aver creato l'estensione e il percorso personalizzato, procedere con portale di Azure per distribuire il controller di dati di Azure Arc.

1. Accedere al portale di Azure.
1. Cercare "Azure Arc data controller" in Azure Marketplace e avviare il flusso di creazione.
1. Nella sezione **prerequisiti** verificare che sia selezionato il cluster Kubernetes abilitato per Azure Arc (modalità diretta) e procedere al passaggio successivo.
1. Nella sezione **Dettagli controller dati** scegliere una sottoscrizione e un gruppo di risorse.
1. Immettere un nome per il controller di dati.
1. Scegliere un profilo di configurazione basato sul provider di distribuzione Kubernetes in cui si sta distribuendo.
1. Scegliere il percorso personalizzato creato nel passaggio precedente.
1. Specificare i dettagli per l'account di accesso e la password dell'amministratore del controller dati.
1. Specificare i dettagli per ClientID, TenantId e Secret client per l'entità servizio da usare per creare gli oggetti di Azure. Vedere [caricare le metriche](upload-metrics-and-logs-to-azure-monitor.md) per istruzioni dettagliate sulla creazione di un account dell'entità servizio e sui ruoli che è necessario concedere per l'account.
1. Fare clic su **Avanti**, rivedere la pagina di riepilogo per tutti i dettagli e fare clic su **Crea**.

## <a name="monitor-the-creation"></a>Monitorare la creazione

Quando lo stato della distribuzione portale di Azure indica che la distribuzione è stata completata correttamente, è possibile controllare lo stato della distribuzione del controller di dati Arc nel cluster come indicato di seguito:

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>Passaggi successivi

[Creare un gruppo di server PostgreSQL Hyperscale abilitato per Azure Arc](create-postgresql-hyperscale-server-group.md)

[Creare un'istanza gestita di SQL di Azure in Azure Arc](create-sql-managed-instance.md)
