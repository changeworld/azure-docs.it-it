---
title: "Esercitazione: Migliorare l'accesso alle applicazioni Web - gateway applicazione di Azure"
description: In questa esercitazione vengono fornite informazioni su come creare un gateway applicazione con ridondanza della zona e scalabilità automatica con un indirizzo IP riservato tramite Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8196267ff7a71fb3910848fd0fef11a40a3c1c32
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869942"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Esercitazione: Creare un gateway applicazione che migliora l'accesso alle applicazioni Web

Gli amministratori IT che desiderano migliorare l'accesso alle applicazioni Web possono ottimizzare il gateway applicazione in modo da ridimensionarsi in base alla richiesta dei clienti ed estendersi a più zone di disponibilità. Questa esercitazione aiuta a configurare funzioni del gateway applicazione di Azure per eseguire operazioni di scalabilità automatica, ridondanza della zona e indirizzi VIP riservati (indirizzi IP statici). Per risolvere il problema si useranno cmdlet di Azure PowerShell e il modello di distribuzione Azure Resource Manager.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un certificato autofirmato
> * Creare una rete virtuale con scalabilità automatica
> * Creare un indirizzo IP pubblico riservato
> * Configurare l'infrastruttura del gateway applicazione
> * Specificare la scalabilità automatica
> * Creare il gateway applicazione
> * Testare il gateway applicazione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per questa esercitazione è necessario eseguire una sessione di amministrazione Azure PowerShell locale. Deve essere installato il modulo Azure PowerShell 1.0.0 o versioni successive. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Dopo avere verificato la versione di PowerShell, eseguire `Connect-AzAccount` per creare una connessione ad Azure.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un gruppo di risorse in una delle località disponibili.

```azurepowershell
$location = "East US 2"
$rg = "AppGW-rg"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Per la produzione è necessario importare un certificato valido firmato da un provider attendibile. Per questa esercitazione viene creato un certificato autofirmato usando il comando [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate). È possibile usare [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate) con l'identificazione personale restituita per esportare un file pfx dal certificato.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

L'output sarà simile al risultato seguente:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Usare l'identificazione personale per creare il file pfx. Sostituire *\<password>* con una password di propria scelta:

```powershell
$pwd = ConvertTo-SecureString -String "<password>" -Force -AsPlainText

Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```


## <a name="create-a-virtual-network"></a>Creare una rete virtuale

Creare una rete virtuale con una subnet dedicata per un gateway applicazione con scalabilità automatica. Attualmente è possibile distribuire un solo gateway applicazione con scalabilità automatica in ogni subnet dedicata.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Creare un indirizzo IP pubblico riservato

Specificare il metodo di allocazione dell'indirizzo IP pubblico come **Statico**. L'indirizzo VIP di un gateway applicazione con scalabilità automatica può essere solo statico. Gli indirizzi IP dinamici non sono supportati. È supportata solo la SKU PublicIpAddress Standard.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard -Zone 1,2,3
```

## <a name="retrieve-details"></a>Recuperare i dettagli

Recuperare i dettagli del gruppo di risorse, della subnet e dell'indirizzo IP in un oggetto locale per creare i dettagli di configurazione IP del gateway applicazione.

```azurepowershell
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="create-web-apps"></a>Creare app Web

Configurare due app Web per il pool back-end. Sostituire *\<site1-name>* e con nomi *\<site-2-name>* univoci nel `azurewebsites.net` dominio.

```azurepowershell
New-AzAppServicePlan -ResourceGroupName $rg -Name "ASP-01"  -Location $location -Tier Basic `
   -NumberofWorkers 2 -WorkerSize Small
New-AzWebApp -ResourceGroupName $rg -Name <site1-name> -Location $location -AppServicePlan ASP-01
New-AzWebApp -ResourceGroupName $rg -Name <site2-name> -Location $location -AppServicePlan ASP-01
```

## <a name="configure-the-infrastructure"></a>Configurare l'infrastruttura

Impostare la configurazione IP, la configurazione IP front-end, il pool back-end, le impostazioni HTTP, il certificato, la porta, il listener e la regola in un formato identico a quello del gateway applicazione Standard esistente. La nuova SKU segue lo stesso modello a oggetti della SKU Standard.

Sostituire i due FQDN dell'app Web (ad esempio: ) nella `mywebapp.azurewebsites.net` definizione $pool variabile.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses <your first web app FQDN>, <your second web app FQDN>
$fp01 = New-AzApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "Azure123456!" -AsPlainText -Force
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "c:\appgwcert.pfx"
$listener01 = New-AzApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled -PickHostNameFromBackendAddress
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Specificare la scalabilità automatica

È ora possibile specificare la configurazione della scalabilità automatica per il gateway applicazione. 

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```
In questa modalità il gateway applicazione si ridimensiona automaticamente in base al modello di traffico dell'applicazione.

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Creare il gateway applicazione e includere le zone di ridondanza e la configurazione di scalabilità automatica.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Usare Get-AzPublicIPAddress per ottenere l'indirizzo IP pubblico del gateway applicazione. Copiare l'indirizzo IP pubblico o il nome DNS e quindi incollarlo nella barra degli indirizzi del browser.

```azurepowershell
$pip = Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
$pip.IpAddress
```


## <a name="clean-up-resources"></a>Pulire le risorse

Innanzitutto, esplorare le risorse che sono state create con il gateway applicazione. Successivamente, quando non servono più, è possibile usare il comando `Remove-AzResourceGroup` per rimuovere il gruppo di risorse, il gateway applicazione e tutte le risorse correlate.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un gateway applicazione con regole di routing basate su percorsi URL](./tutorial-url-route-powershell.md)
