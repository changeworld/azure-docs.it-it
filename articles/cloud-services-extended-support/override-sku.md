---
title: Eseguire l'override delle informazioni dello SKU su CSCFG/CSDEF per i servizi cloud di Azure (supporto esteso)
description: Eseguire l'override delle informazioni dello SKU su CSCFG/CSDEF per i servizi cloud di Azure (supporto esteso)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: 17e47b562c52ffce631a01cf03004d77053ea647
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387333"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>Eseguire l'override delle informazioni dello SKU su CSCFG/CSDEF in servizi cloud (supporto esteso) 

Questa funzionalità consentirà all'utente di aggiornare le dimensioni del ruolo e il numero di istanze nel servizio cloud usando la proprietà **allowModelOverride** senza dover aggiornare i file di configurazione del servizio e di definizione del servizio, consentendo così al servizio cloud di aumentare/ridurre/entrare/uscire senza eseguire un riassemblaggio e ridistribuire.

## <a name="set-allowmodeloverride-property"></a>Imposta proprietà allowModelOverride
È possibile impostare la proprietà allowModelOverride nei modi seguenti:
* Quando allowModelOverride = true, la chiamata API aggiornerà le dimensioni del ruolo e il numero di istanze per il servizio cloud senza convalidare i valori con i file csdef e cscfg. 
> [!Note]
> Il cscfg verrà aggiornato in modo da riflettere il numero di istanze del ruolo, ma csdef (all'interno di cspkg) manterrà i valori precedenti
* Quando allowModelOverride = false, la chiamata API genera un errore quando i valori delle dimensioni del ruolo e del numero di istanze non corrispondono rispettivamente ai file csdef e cscfg

Il valore predefinito è impostato su false. Se la proprietà viene reimpostata su false da true, i file csdef e cscfg verrebbero nuovamente controllati per la convalida.

Per applicare la proprietà in PowerShell, nel modello e nell'SDK, vedere gli esempi seguenti.

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
Se si imposta la proprietà "allowModelOverride" = true, il servizio cloud viene aggiornato con le proprietà del ruolo definite nella sezione roleProfile
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “**allowModelOverride**” : true,
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
Se si imposta l'opzione "AllowModelOverride" sul nuovo cmdlet di New-AzCloudService, il servizio cloud viene aggiornato con le proprietà dello SKU definite in RoleProfile
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
Se si imposta la variabile AllowModelOverride = true, il servizio cloud viene aggiornato con le proprietà SKU definite in RoleProfile

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
Il portale non consente alla proprietà precedente di eseguire l'override della dimensione del ruolo e del numero di istanze in csdef e cscfg. 


## <a name="next-steps"></a>Passaggi successivi 
- Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso).
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
