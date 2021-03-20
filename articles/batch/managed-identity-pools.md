---
title: Configurare le identità gestite nei pool di batch
description: Informazioni su come abilitare le identità gestite assegnate dall'utente nei pool di batch e su come usare le identità gestite nei nodi.
ms.topic: conceptual
ms.date: 02/10/2021
ms.custom: references_regions
ms.openlocfilehash: 4a59383d119f88bb3ee180f629ba0a6ea6ac2f44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417619"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Configurare le identità gestite nei pool di batch

Le [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) eliminano la necessità per gli sviluppatori di gestire le credenziali fornendo un'identità per la risorsa di azure in Azure Active Directory (Azure ad) e usandola per ottenere token Azure Active Directory (Azure ad).

Questo argomento illustra come abilitare le identità gestite assegnate dall'utente nei pool di batch e come usare le identità gestite nei nodi.

> [!IMPORTANT]
> Il supporto per i pool di Azure Batch con identità gestite assegnate dall'utente è attualmente disponibile in anteprima pubblica per le aree seguenti: Stati Uniti occidentali 2, Stati Uniti centro-meridionali, Stati Uniti orientali, US Gov Arizona e US Gov Virginia.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Creare un'identità assegnata dall'utente

Per prima cosa, [creare l'identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) nello stesso tenant dell'account batch. Questa identità gestita non deve trovarsi nello stesso gruppo di risorse o anche nella stessa sottoscrizione.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>Creare un pool di batch con identità gestite assegnate dall'utente

Dopo aver creato una o più identità gestite assegnate dall'utente, è possibile creare un pool di batch con tale identità gestita usando la libreria di [gestione batch .NET](/dotnet/api/overview/azure/batch#management-library).

> [!IMPORTANT]
> Per usare le identità gestite, i pool devono essere configurati usando la [configurazione della macchina virtuale](nodes-and-pools.md#virtual-machine-configuration) .

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        };
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

> [!NOTE]
> La creazione di pool con identità gestite non è attualmente supportata con la [libreria client batch .NET](/dotnet/api/overview/azure/batch#client-library).

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Usare identità gestite assegnate dall'utente nei nodi di batch

Dopo aver creato i pool, le identità gestite assegnate dall'utente possono accedere ai nodi del pool tramite Secure Shell (SSH) o Desktop remoto (RDP). È anche possibile configurare le attività in modo che le identità gestite possano accedere direttamente alle [risorse di Azure che supportano le identità gestite](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

All'interno dei nodi batch, è possibile ottenere i token di identità gestiti e usarli per eseguire l'autenticazione tramite l'autenticazione Azure AD tramite il [servizio metadati dell'istanza di Azure](../virtual-machines/windows/instance-metadata-service.md).

Per Windows, lo script di PowerShell per ottenere un token di accesso per l'autenticazione è:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Per Linux, lo script bash è:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

Per altre informazioni, vedere [come usare le identità gestite per le risorse di Azure in una macchina virtuale di Azure per acquisire un token di accesso](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).
- Informazioni su come usare [chiavi gestite dal cliente con identità gestite dall'utente](batch-customer-managed-key.md).
