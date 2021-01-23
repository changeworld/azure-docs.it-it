---
title: Distribuire un servizio cloud (supporto esteso)-PowerShell
description: Distribuire un servizio cloud (supporto esteso) con PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 325e9123dd3f121b88df6d03518cfd9300fe3be9
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745183"
---
# <a name="create-a-cloud-service-extended-support-using-azure-powershell"></a>Creare un servizio cloud (supporto esteso) usando Azure PowerShell

Questo articolo illustra come usare il `Az.CloudService` modulo PowerShell per distribuire i servizi cloud (supporto esteso) in Azure con più ruoli (WebRole e WorkerRole) e l'estensione desktop remoto. 

> [!IMPORTANT]
> Servizi cloud (supporto esteso) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso) e creare le risorse associate. 

3. Installare il modulo di PowerShell AZ. CloudService  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

4. Creare un nuovo gruppo di risorse. Questo passaggio è facoltativo se si usa un gruppo di risorse esistente.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

5. Creare un account e un contenitore di archiviazione che verranno usati per archiviare il pacchetto del servizio cloud (. cspkg) e i file di configurazione del servizio (. cscfg). È necessario usare un nome univoco per il nome dell'account di archiviazione. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “ContosoContainer” -Context $storageAccount.Context -Permission Blob 
    ```

6. Caricare il pacchetto del servizio cloud (cspkg) nell'account di archiviazione.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “ContosoContainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “ContosoContainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

7.  Caricare la configurazione del servizio cloud (cscfg) nell'account di archiviazione. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container ContosoContainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “ContosoContainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

8. Creare una rete virtuale e una subnet. Questo passaggio è facoltativo se si usa una rete e una subnet esistenti. Questo esempio usa una singola rete virtuale e una subnet per entrambi i ruoli del servizio cloud (WebRole e WorkerRole). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
9. Creare un indirizzo IP pubblico e, facoltativamente, impostare la proprietà etichetta DNS dell'indirizzo IP pubblico. Se si usa un indirizzo IP statico, è necessario farvi riferimento come IP riservato nel file di configurazione del servizio.  

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

10. Creare un oggetto profilo di rete e associare l'indirizzo IP pubblico al front-end del servizio di bilanciamento del carico creato dalla piattaforma.  

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
11. Creare un insieme di credenziali delle chiavi. Questa Key Vault verrà usata per archiviare i certificati associati ai ruoli del servizio cloud (supporto esteso). Il Key Vault deve trovarsi nella stessa area e nella stessa sottoscrizione del servizio cloud e avere un nome univoco. Per altre informazioni, vedere [usare i certificati con servizi cloud di Azure (supporto esteso)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosoOrg” -Location “East US” 
    ```

13. Aggiornare i criteri di accesso Key Vault e concedere le autorizzazioni per il certificato all'account utente. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosoOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    In alternativa, impostare i criteri di accesso tramite ObjectId (che è possibile ottenere eseguendo `Get-AzADUser` ) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

14. Ai fini di questo esempio, verrà aggiunto un certificato autofirmato a un Key Vault. È necessario aggiungere l'identificazione personale del certificato nel file di configurazione del servizio cloud (. cscfg) per la distribuzione nei ruoli del servizio cloud. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
15. Creare un oggetto in memoria del profilo del sistema operativo. Profilo del sistema operativo specifica i certificati associati ai ruoli del servizio cloud. Si tratta dello stesso certificato creato nel passaggio precedente. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

16. Creazione di un oggetto in memoria del profilo del ruolo. Il profilo del ruolo definisce proprietà specifiche dello SKU dei ruoli, ad esempio nome, capacità e livello. Per questo esempio, sono stati definiti due ruoli: frontendRole e backendRole. Le informazioni sul profilo del ruolo devono corrispondere alla configurazione del ruolo definita in file di configurazione (cscfg) e file di definizione del servizio (csdef). 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

17. Opzionale Creare un oggetto in memoria del profilo di estensione che si desidera aggiungere al servizio cloud. Per questo esempio verrà aggiunta l'estensione RDP. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $extension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "ContosSA" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
18. Opzionale Definire i tag come tabella hash di PowerShell che si vuole aggiungere al servizio cloud. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

19. Creare una distribuzione del servizio cloud usando oggetti profilo & URL SAS.

    ```powershell
    $cloudService = New-AzCloudService                                                  ` 
    -Name “ContosoCS”                                           ` 
    -ResourceGroupName “ContosOrg”                     ` 
    -Location “East US”                                           ` 
    -PackageUrl $cspkgUrl                       ` 
    -ConfigurationUrl $cscfgUrl                                         ` 
    -UpgradeMode 'Auto'                                           ` 
    -RoleProfile $roleProfile                                       ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile  
    -Tag $tag 
    ```

## <a name="next-steps"></a>Passaggi successivi 
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).