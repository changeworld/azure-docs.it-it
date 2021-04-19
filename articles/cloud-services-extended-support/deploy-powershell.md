---
title: Distribuire un servizio cloud (supporto esteso) - PowerShell
description: Distribuire un servizio cloud (supporto esteso) con PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a7e4e76aa0619d78a91d766a9a43c0b1a02a48d3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717388"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Distribuire un servizio cloud (supporto esteso) usando Azure PowerShell

Questo articolo illustra come usare il modulo PowerShell per distribuire Servizi cloud (supporto esteso) in Azure con più ruoli (WebRole e WorkerRole) ed estensione `Az.CloudService` desktop remoto. 

## <a name="before-you-begin"></a>Prima di iniziare

Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per Servizi cloud (supporto esteso) e creare le risorse associate. 

## <a name="deploy-a-cloud-services-extended-support"></a>Distribuire servizi cloud (supporto esteso)
1. Installare il modulo Az.CloudService di PowerShell  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Creare un nuovo gruppo di risorse. Questo passaggio è facoltativo se si usa un gruppo di risorse esistente.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Creare un account di archiviazione e un contenitore che verranno usati per archiviare i file del pacchetto del servizio cloud (con estensione cspkg) e di configurazione del servizio (con estensione cscfg). È necessario usare un nome univoco per il nome dell'account di archiviazione. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Caricare il pacchetto del servizio cloud (cspkg) nell'account di archiviazione.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Caricare la configurazione del servizio cloud (cscfg) nell'account di archiviazione. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Creare una rete virtuale e una subnet. Questo passaggio è facoltativo se si usano una rete e una subnet esistenti. Questo esempio usa una singola rete virtuale e una sola subnet per entrambi i ruoli del servizio cloud (WebRole e WorkerRole). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Creare un indirizzo IP pubblico e impostare la proprietà etichetta DNS dell'indirizzo IP pubblico. Servizi cloud (supporto esteso) supporta solo indirizzi IP pubblici dello SKU [Basic.](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) Gli IP pubblici dello SKU Standard non funzionano con i servizi cloud.
Se si usa un indirizzo IP statico, è necessario fare riferimento a esso come IP riservato nel file di configurazione del servizio (con estensione cscfg) 

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Creare un oggetto profilo di rete e associare l'indirizzo IP pubblico al front-end del servizio di bilanciamento del carico. La piattaforma Azure crea automaticamente una risorsa di bilanciamento del carico con SKU "classico" nella stessa sottoscrizione della risorsa del servizio cloud. La risorsa di bilanciamento del carico è una risorsa di sola lettura in Arm. Gli aggiornamenti alla risorsa sono supportati solo tramite i file di distribuzione del servizio cloud (con estensione cscfg & .csdef)

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Creare un insieme di credenziali delle chiavi. Questa Key Vault verrà usata per archiviare i certificati associati ai ruoli del servizio cloud (supporto esteso). Il Key Vault deve trovarsi nella stessa area e nella stessa sottoscrizione del servizio cloud e avere un nome univoco. Per altre informazioni, vedere [Usare i certificati con Servizi cloud di Azure (supporto esteso).](certificates-and-key-vault.md)

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Aggiornare i criteri Key Vault di accesso e concedere le autorizzazioni del certificato all'account utente. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    In alternativa, impostare i criteri di accesso tramite ObjectId (che può essere ottenuto eseguendo `Get-AzADUser` ) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. Ai fini di questo esempio si aggiungerà un certificato autofirmato a un Key Vault. L'identificazione personale del certificato deve essere aggiunta nel file di configurazione del servizio cloud (con estensione cscfg) per la distribuzione nei ruoli del servizio cloud. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Creare un oggetto profilo del sistema operativo in memoria. Profilo del sistema operativo specifica i certificati associati ai ruoli del servizio cloud. Si tratta dello stesso certificato creato nel passaggio precedente. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Creare un oggetto profilo ruolo in memoria. Il profilo del ruolo definisce le proprietà specifiche dello sku dei ruoli, ad esempio il nome, la capacità e il livello. Per questo esempio sono stati definiti due ruoli: frontendRole e backendRole. Le informazioni sul profilo del ruolo devono corrispondere alla configurazione del ruolo definita nel file di configurazione (cscfg) e nel file di definizione del servizio (csdef). 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. (Facoltativo) Creare un oggetto profilo di estensione in memoria da aggiungere al servizio cloud. Per questo esempio verrà aggiunta l'estensione RDP. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    Si noti che configFile deve avere solo tag PublicConfig e deve contenere uno spazio dei nomi come segue:
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. (Facoltativo) Definire tag come tabella hash di PowerShell da aggiungere al servizio cloud. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Creare la distribuzione del servizio cloud usando oggetti profilo & URL di firma di accesso condiviso.

    ```powershell
    $cloudService = New-AzCloudService ` 
    -Name “ContosoCS” ` 
    -ResourceGroupName “ContosOrg” ` 
    -Location “East US” ` 
    -PackageUrl $cspkgUrl ` 
    -ConfigurationUrl $cscfgUrl ` 
    -UpgradeMode 'Auto' ` 
    -RoleProfile $roleProfile ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile `
    -Tag $tag 
    ```

## <a name="next-steps"></a>Passaggi successivi 
- Esaminare [le domande frequenti su](faq.md) Servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), [Template](deploy-template.md) [o Visual Studio](deploy-visual-studio.md).
- Visitare il repository di esempi di Servizi [cloud (supporto esteso)](https://github.com/Azure-Samples/cloud-services-extended-support)
