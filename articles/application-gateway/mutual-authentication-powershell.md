---
title: Configurare l'autenticazione reciproca sul gateway applicazione Azure tramite PowerShell
description: Informazioni su come configurare un gateway applicazione per l'autenticazione reciproca tramite PowerShell
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 95534760c09ca9e1f7f09d6079886216127c7eb0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231513"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-powershell-preview"></a>Configurare l'autenticazione reciproca con il gateway applicazione tramite PowerShell (anteprima)
Questo articolo descrive come usare PowerShell per configurare l'autenticazione reciproca nel gateway applicazione. L'autenticazione reciproca significa che il gateway applicazione autentica il client inviando la richiesta usando il certificato client caricato nel gateway applicazione. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Questo articolo richiede il modulo Azure PowerShell versione 1.0.0 o successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per creare una connessione con Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Per configurare l'autenticazione reciproca con un gateway applicazione, è necessario un certificato client da caricare nel gateway. Il certificato client verrà usato per convalidare il certificato che il client presenterà al gateway applicazione. A scopo di test, è possibile usare un certificato autofirmato. Tuttavia, questa operazione non è consigliata per i carichi di lavoro di produzione, perché sono più difficili da gestire e non sono completamente protetti.

Per altre informazioni, soprattutto sul tipo di certificati client che è possibile caricare, vedere [Panoramica dell'autenticazione reciproca con il gateway applicazione](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare innanzitutto un nuovo gruppo di risorse nella sottoscrizione. 

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location -Tags @{ testtag = "APPGw tag"}
```
## <a name="create-a-virtual-network"></a>Crea rete virtuale

Distribuire una rete virtuale per il gateway applicazione da distribuire in.

```azurepowershell
$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet
$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -VirtualNetwork $vnet
```

## <a name="create-a-public-ip"></a>Creare un indirizzo IP pubblico.

Creare un indirizzo IP pubblico da usare con il gateway applicazione. 

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -sku Standard
```

## <a name="create-the-application-gateway-ip-configuration"></a>Creare la configurazione IP del gateway applicazione

Creare le configurazioni IP e la porta front-end. 

```azurepowershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip
$port = New-AzApplicationGatewayFrontendPort -Name $frontendPortName  -Port 443
```

## <a name="configure-frontend-ssl"></a>Configurare SSL front-end 

Configurare i certificati SSL per il gateway applicazione.

```azurepowershell
$password = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
$sslCertPath = $basedir + "/ScenarioTests/Data/ApplicationGatewaySslCert1.pfx"
$sslCert = New-AzApplicationGatewaySslCertificate -Name $sslCertName -CertificateFile $sslCertPath -Password $password
```

## <a name="configure-client-authentication"></a>Configurare l'autenticazione dei client 

Configurare l'autenticazione client nel gateway applicazione. Per altre informazioni su come estrarre le catene di certificati CA client attendibili da usare qui, vedere [come estrarre le catene di certificati CA client attendibili](./mutual-authentication-certificate-management.md).

> [!IMPORTANT]
> Assicurarsi di caricare l'intera catena di certificati della CA client in un file e solo una catena per ogni file.  

> [!NOTE]
> Si consiglia di usare TLS 1,2 con l'autenticazione reciproca perché TLS 1,2 verrà richiesto in futuro. 

```azurepowershell
$clientCertFilePath = $basedir + "/ScenarioTests/Data/TrustedClientCertificate.cer"
$trustedClient01 = New-AzApplicationGatewayTrustedClientCertificate -Name $trustedClientCert01Name -CertificateFile $clientCertFilePath
$sslPolicy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S"
$clientAuthConfig = New-AzApplicationGatewayClientAuthConfiguration -VerifyClientCertIssuerDN
$sslProfile01 = New-AzApplicationGatewaySslProfile -Name $sslProfile01Name -SslPolicy $sslPolicy -ClientAuthConfiguration $clientAuthConfig -TrustedClientCertificates $trustedClient01
$listener = New-AzApplicationGatewayHttpListener -Name $listenerName -Protocol Https -SslCertificate $sslCert -FrontendIPConfiguration $fipconfig -FrontendPort $port -SslProfile $sslProfile01
```

## <a name="configure-the-backend-pool-and-settings"></a>Configurare il pool back-end e le impostazioni

Configurare il pool back-end e le impostazioni per il gateway applicazione. Facoltativamente, configurare il certificato radice attendibile back-end per la crittografia SSL end-to-end.  

```azurepowershell
$certFilePath = $basedir + "/ScenarioTests/Data/ApplicationGatewayAuthCert.cer"
$trustedRoot = New-AzApplicationGatewayTrustedRootCertificate -Name $trustedRootCertName -CertificateFile $certFilePath
$pool = New-AzApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses www.microsoft.com, www.bing.com
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name $poolSettingName -Port 443 -Protocol Https -CookieBasedAffinity Enabled -PickHostNameFromBackendAddress -TrustedRootCertificate $trustedRoot
```

## <a name="configure-the-rule"></a>Configurare la regola

Configurare una regola nel gateway applicazione.

```azurepowershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

## <a name="set-up-default-ssl-policy-for-future-listeners"></a>Configurare i criteri SSL predefiniti per i listener futuri

Sono stati configurati criteri SSL specifici del listener durante la configurazione dell'autenticazione reciproca. In questo passaggio è possibile impostare facoltativamente i criteri SSL predefiniti per i listener futuri creati. 

```azurepowershell
$sslPolicyGlobal = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401"
```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Usando tutti gli elementi creati in precedenza, distribuire il gateway applicazione.

```azurepowershell
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Zone 1,2 -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $port -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicyGlobal -TrustedRootCertificate $trustedRoot -AutoscaleConfiguration $autoscaleConfig -TrustedClientCertificates $trustedClient01 -SslProfiles $sslProfile01 -SslCertificates $sslCert
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell
Remove-AzResourceGroup -Name $rgname
```

## <a name="renew-expired-client-ca-certificates"></a>Rinnova i certificati CA client scaduti

Nel caso in cui il certificato della CA client sia scaduto, è possibile aggiornare il certificato nel gateway attenendosi alla procedura seguente: 

1. Accedere ad Azure
    ```azurepowershell
    Connect-AzAccount
    Select-AzSubscription -Subscription "<sub name>"
    ```
2. Ottenere la configurazione del gateway applicazione
    ```azurepowershell
    $gateway = Get-AzApplicationGateway -Name "<gateway-name>" -ResourceGroupName "<resource-group-name>"
    ```
3. Rimuovere il certificato client attendibile dal gateway 
    ```azurepowershell
    Remove-AzApplicationGatewayTrustedClientCertificate -Name "<name-of-client-certificate>" -ApplicationGateway $gateway
    ``` 
4. Aggiungere il nuovo certificato nel gateway 
    ```azurepowershell
    Add-AzApplicationGatewayTrustedClientCertificate -ApplicationGateway $gateway -Name "<name-of-new-cert>" -CertificateFile "<path-to-certificate-file>"
    ```
5. Aggiornare il gateway con il nuovo certificato 
    ```azurepowershell
    Set-AzApplicationGateway -ApplicationGateway $gateway
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Gestire il traffico Web con un gateway applicazione mediante l'interfaccia della riga di comando di Azure](./tutorial-manage-web-traffic-cli.md)