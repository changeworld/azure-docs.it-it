---
title: Eseguire l'override delle informazioni sullo SKU su CSCFG/CSDEF per Servizi cloud di Azure (supporto esteso)
description: Eseguire l'override delle informazioni sullo SKU su CSCFG/CSDEF per Servizi cloud di Azure (supporto esteso)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: d5dfae4b5cfee8f61e11e418a05e86017d119410
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739261"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>Eseguire l'override delle informazioni sullo SKU su CSCFG/CSDEF in Servizi cloud (supporto esteso) 

Questa funzionalità consentirà all'utente di aggiornare le dimensioni del ruolo e il numero di istanze nel servizio cloud usando la proprietà **allowModelOverride** senza dover aggiornare la configurazione del servizio e i file di definizione del servizio, consentendo così al servizio cloud di aumentare/ridurre/ridurre/ridurre il numero di istanze senza dover eseguire un nuovo pacchetto e ridistribuire.

## <a name="set-allowmodeloverride-property"></a>Impostare la proprietà allowModelOverride
La proprietà allowModelOverride può essere impostata nei modi seguenti:
* Quando allowModelOverride = true, la chiamata API aggiornerà le dimensioni del ruolo e il numero di istanze per il servizio cloud senza convalidare i valori con i file csdef e cscfg. 
> [!Note]
> Il file cscfg verrà aggiornato in modo da riflettere il numero di istanze del ruolo, ma il csdef (all'interno di cspkg) manterrà i valori vecchi
* Quando allowModelOverride = false, la chiamata API genera un errore quando i valori relativi alle dimensioni del ruolo e al numero di istanze non corrispondono rispettivamente ai file csdef e cscfg

Il valore predefinito è impostato su false. Se la proprietà viene reimpostata su false da true, i file csdef e cscfg verranno nuovamente verificati per la convalida.

Vedere gli esempi seguenti per applicare la proprietà in PowerShell, nel modello e nell'SDK

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
L'impostazione della proprietà "allowModelOverride" = true in questo caso aggiornerà il servizio cloud con le proprietà del ruolo definite nella sezione roleProfile
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
L'impostazione dell'opzione "AllowModelOverride" nel nuovo cmdlet New-AzCloudService aggiornerà il servizio cloud con le proprietà SKU definite in RoleProfile
```powershell
New-AzCloudService ` 
-Name “ContosoCS” ` 
-ResourceGroupName “ContosOrg” ` 
-Location “East US” `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK
L'impostazione della variabile AllowModelOverride= true aggiornerà il servizio cloud con le proprietà SKU definite in RoleProfile

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
```
### <a name="azure-portal"></a>Portale di Azure
Il portale non consente alla proprietà precedente di eseguire l'override delle dimensioni del ruolo e del numero di istanze in csdef e cscfg. 


## <a name="next-steps"></a>Passaggi successivi 
- Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per Servizi cloud (supporto esteso).
- Esaminare [le domande frequenti su](faq.md) Servizi cloud (supporto esteso).
