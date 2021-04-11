---
title: Eseguire la migrazione ai servizi cloud di Azure (supporto esteso) con PowerShell
description: Come eseguire la migrazione da servizi cloud di Azure (versione classica) a servizi cloud di Azure (supporto esteso) con PowerShell
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: eea49a41e81e7e580becce815ff91aff6aa430d6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286918"
---
# <a name="migrate-to-azure-cloud-services-extended-support-using-powershell"></a>Eseguire la migrazione ai servizi cloud di Azure (supporto esteso) con PowerShell

Questi passaggi illustrano come usare i comandi di Azure PowerShell per eseguire la migrazione da [servizi cloud (versione classica)](../cloud-services/cloud-services-choose-me.md) a [servizi cloud (supporto esteso)](overview.md).

> [!IMPORTANT]
> La migrazione da servizi cloud (versione classica) a servizi cloud (supporto esteso) con lo strumento di migrazione è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="1-plan-for-migration"></a>1) pianificare la migrazione
La pianificazione è il passaggio più importante per una corretta esperienza di migrazione. Vedere [Panoramica dei servizi cloud (supporto esteso)](overview.md) e [pianificazione della migrazione delle risorse IaaS dal modello classico al Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-plan.md) prima di iniziare qualsiasi procedura di migrazione. 

## <a name="2-install-the-latest-version-of-powershell"></a>2) installare la versione più recente di PowerShell
Per l'installazione di Azure PowerShell sono previste due opzioni principali: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) e [Installazione guidata piattaforma Web (WebPI)](https://aka.ms/webpi-azps). WebPI riceve aggiornamenti mensili. PowerShell Gallery riceve aggiornamenti su base continua. Questo articolo si basa su Azure PowerShell versione 2.1.0.

Per le istruzioni di installazione, vedere [Come installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0&preserve-view=true).

## <a name="3-ensure-admin-permissions"></a>3) verificare le autorizzazioni di amministratore
Per eseguire questa migrazione, è necessario essere aggiunti come coamministratore per la sottoscrizione nel [portale di Azure](https://portal.azure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu **Hub** selezionare **sottoscrizione**. Se questa voce non viene visualizzata, selezionare **Tutti i servizi**.
3. Individuare la voce della sottoscrizione appropriata e quindi esaminare il campo **ruolo personale** . Per un coamministratore, il valore deve essere *amministratore account*.

Se non si è in grado di aggiungere un coamministratore, contattare un amministratore del servizio o un coamministratore per la sottoscrizione per essere aggiunti.

## <a name="4-register-the-classic-provider-and-cloudservice-feature"></a>4) registrare il provider classico e la funzionalità CloudService
Avviare prima un prompt di PowerShell. Per la migrazione, configurare l'ambiente sia per la versione classica che per la Gestione risorse.

Accedere con l'account per il modello di Resource Manager.

```powershell
Connect-AzAccount
```

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente:

```powershell
Get-AzSubscription | Sort Name | Select Name
```

Impostare la sottoscrizione di Azure per la sessione corrente. In questo esempio viene impostato il nome **My Azure Subscription** per la sottoscrizione predefinita. Sostituire il nome della sottoscrizione di esempio con il nome della propria sottoscrizione.

```powershell
Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

Registrarsi con il provider di risorse di migrazione usando il comando seguente:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```
> [!NOTE]
> La registrazione è un passaggio singolo, ma è necessario eseguire una volta prima di tentare la migrazione. Senza la registrazione, verrà visualizzato il seguente messaggio di errore:
>
> *BadRequest: Subscription is not registered for migration* (Richiesta non valida: la sottoscrizione non è registrata per la migrazione)

Registrare la funzionalità CloudServices per la sottoscrizione. Il completamento delle registrazioni potrebbe richiedere alcuni minuti. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Attendere cinque minuti per il completamento della registrazione. 

Verificare lo stato dell'approvazione del provider classico usando il comando seguente:

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Verificare lo stato della registrazione usando quanto segue:  
```powershell
Get-AzProviderFeature -FeatureName CloudServices
```

Assicurarsi che RegistrationState sia `Registered` per entrambi prima di procedere.

Prima di passare al modello di distribuzione classica, assicurarsi di disporre di un numero sufficiente Azure Resource Manager quota vCPU nell'area di Azure della distribuzione corrente o della rete virtuale. È possibile usare il comando PowerShell seguente per controllare il numero corrente di CPU virtuali in Azure Resource Manager. Per altre informazioni sulle quote di CPU virtuali, vedere [Limiti e Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

In questo esempio viene verificata la disponibilità nell'area **Stati Uniti occidentali**. Sostituire il nome dell'area di esempio con il nome della propria area.

```powershell
Get-AzVMUsage -Location "West US"
```

A questo punto, accedere al proprio account per il modello di distribuzione classica.

```powershell
Add-AzureAccount
```

È possibile ottenere le sottoscrizioni disponibili usando il comando seguente:

```powershell
Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Impostare la sottoscrizione di Azure per la sessione corrente. In questo esempio viene impostato **My Azure Subscription** come sottoscrizione predefinita. Sostituire il nome della sottoscrizione di esempio con il nome della propria sottoscrizione.

```powershell
Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="5-migrate-your-cloud-services"></a>5) eseguire la migrazione dei servizi cloud 
* [Eseguire la migrazione di un servizio cloud non in una rete virtuale](#51-option-1---migrate-a-cloud-service-not-in-a-virtual-network)
* [Eseguire la migrazione di un servizio cloud in una rete virtuale](#51-option-2---migrate-a-cloud-service-in-a-virtual-network)

> [!NOTE]
> Tutte le operazioni descritte di seguito sono idempotenti. Se vengono rilevati errori diversi da una funzionalità non supportata o un errore di configurazione, è consigliabile provare a ripetere l'operazione di preparazione, interruzione o commit. La piattaforma tenterà di ripetere l'azione.


### <a name="51-option-1---migrate-a-cloud-service-not-in-a-virtual-network"></a>5,1) opzione 1: eseguire la migrazione di un servizio cloud non in una rete virtuale
Ottenere l'elenco dei servizi cloud usando il comando seguente. Quindi selezionare il servizio cloud di cui si vuole eseguire la migrazione.

```powershell
Get-AzureService | ft Servicename
```

Ottenere il nome della distribuzione per il servizio cloud. In questo esempio il nome del servizio è **My Service**. Sostituire il nome del servizio di esempio con il nome del proprio servizio.

```powershell
$serviceName = "My Service"
$deployment = Get-AzureDeployment -ServiceName $serviceName
$deploymentName = $deployment.DeploymentName
```

Prima di tutto, verificare che sia possibile eseguire la migrazione del servizio cloud usando i comandi seguenti:

```powershell
$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
$validate.ValidationMessages
 ```

Il comando seguente visualizza gli eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile passare al passaggio di preparazione.

```powershell
Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

### <a name="51-option-2---migrate-a-cloud-service-in-a-virtual-network"></a>5,1) opzione 2: eseguire la migrazione di un servizio cloud in una rete virtuale

Per eseguire la migrazione di un servizio cloud in una rete virtuale, eseguire la migrazione della rete virtuale. Il servizio cloud viene automaticamente migrato con la rete virtuale.

> [!NOTE]
> Il nome della rete virtuale potrebbe essere diverso da quello visualizzato nel nuovo portale. Il nuovo portale di Azure Visualizza il nome come `[vnet-name]` , ma il nome effettivo della rete virtuale è di tipo `Group [resource-group-name] [vnet-name]` . Prima di iniziare la migrazione, cercare il nome effettivo della rete virtuale usando il comando `Get-AzureVnetSite | Select -Property Name` o visualizzarlo nella portale di Azure precedente. 

In questo esempio viene impostato il nome **myVnet** per la rete virtuale. Sostituire il nome della rete virtuale di esempio con il nome della propria rete virtuale.

```powershell
$vnetName = "myVnet"
```

Prima di tutto, verificare che sia possibile eseguire la migrazione della rete virtuale usando il comando seguente:

```powershell
Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Il comando seguente visualizza gli eventuali avvisi ed errori che bloccano la migrazione. Se la convalida ha esito positivo, è possibile procedere con il passaggio di preparazione seguente:

```powershell
Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Controllare la configurazione del servizio cloud preparato usando Azure PowerShell o il portale di Azure. Se non si è pronti per la migrazione e si vuole tornare allo stato precedente, usare il comando seguente:

```powershell
Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se la configurazione preparata appare corretta, è possibile procedere ed eseguire il commit delle risorse usando il comando seguente:

```powershell
Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```


## <a name="next-steps"></a>Passaggi successivi
Esaminare la sezione [post-migrazione modifiche](in-place-migration-overview.md#post-migration-changes) per visualizzare le modifiche apportate ai file di distribuzione, all'automazione e ad altri attributi della distribuzione del nuovo servizio cloud (supporto esteso). 
