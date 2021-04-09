---
title: Applicare l'estensione diagnostica di Microsoft Azure in servizi cloud (supporto esteso)
description: Applicare l'estensione diagnostica di Microsoft Azure per i servizi cloud (supporto esteso)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 14b1661792ca5276bd6ebfa4cee1c4b46f94764d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780447"
---
# <a name="apply-the-windows-azure-diagnostics-extension-in-cloud-services-extended-support"></a>Applicare l'estensione diagnostica di Microsoft Azure in servizi cloud (supporto esteso) 
È possibile monitorare le metriche di prestazioni chiave per qualsiasi servizio cloud. Ogni ruolo del servizio cloud raccoglie dati minimi: utilizzo della CPU, utilizzo della rete e utilizzo del disco. Se il servizio cloud ha l'estensione Microsoft. Azure. Diagnostics applicata a un ruolo, tale ruolo può raccogliere ulteriori punti di dati. Per altre informazioni, vedere [Cenni preliminari sulle estensioni](extensions.md)

È possibile abilitare l'estensione Windows Diagnostica di Azure per i servizi cloud (supporto esteso) tramite [PowerShell](deploy-powershell.md) o il [modello ARM](deploy-template.md)

## <a name="apply-windows-azure-diagnostics-extension-using-powershell"></a>Applicare Windows Diagnostica di Azure Extension usando PowerShell

```powershell
# Create WAD extension object
$storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
$configFilePath = "<Insert WAD public configuration file path>"
$wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFilePath -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 

# Add <privateConfig> settings
$wadExtension.ProtectedSetting = "<Insert WAD Private Configuration as raw string here>"

# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Add WAD extension to existing Cloud Service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $wadExtension

# Update Cloud Service
$cloudService | Update-AzCloudService
```
Scaricare la definizione dello schema del file di configurazione pubblico eseguendo il comando PowerShell seguente:

```powershell
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'PublicWadConfig.xsd'
```
Di seguito è riportato un esempio del file XML di configurazione pubblica
```
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```
Scaricare la definizione dello schema del file di configurazione privato eseguendo il comando PowerShell seguente:

```powershell
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PrivateConfigurationSchema | Out-File -Encoding utf8 -FilePath 'PrivateWadConfig.xsd'
```
Di seguito è riportato un esempio del file XML di configurazione privata

```
<?xml version="1.0" encoding="utf-8"?>
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="string" key="string" />
  <AzureMonitorAccount>
    <ServicePrincipalMeta>
      <PrincipalId>string</PrincipalId>
      <Secret>string</Secret>
    </ServicePrincipalMeta>
  </AzureMonitorAccount>
  <SecondaryStorageAccounts>
    <StorageAccount name="string" />
  </SecondaryStorageAccounts>
  <SecondaryEventHubs>
    <EventHub Url="string" SharedAccessKeyName="string" SharedAccessKey="string" />
  </SecondaryEventHubs>
</PrivateConfig>
```

## <a name="apply-windows-azure-diagnostics-extension-using-arm-template"></a>Applicare l'estensione Windows Diagnostica di Azure usando il modello ARM
```json
"extensionProfile": { 
          "extensions": [ 
            { 
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1", 
              "properties": { 
                "autoUpgradeMinorVersion": true, 
                "publisher": "Microsoft.Azure.Diagnostics", 
                "type": "PaaSDiagnostics", 
                "typeHandlerVersion": "1.5", 
                "settings": "Include PublicConfig XML as a raw string", 
                "protectedSettings": "Include PrivateConfig XML as a raw string”", 
                "rolesAppliedTo": [ 
                  "WebRole1" 
                ] 
              } 
            }
          ]
        },

```


## <a name="next-steps"></a>Passaggi successivi 
- Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso).
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
