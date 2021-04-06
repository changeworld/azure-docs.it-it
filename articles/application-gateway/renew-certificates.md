---
title: Rinnovare un certificato del gateway applicazione di Azure
description: Informazioni su come rinnovare un certificato associato a un listener del gateway dell'applicazione.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/20/2021
ms.author: victorh
ms.openlocfilehash: f0c06a94498f4d2481a6e953b959d766c60415fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98622181"
---
# <a name="renew-application-gateway-certificates"></a>Rinnovare i certificati del gateway applicazione

A un certo punto, sarà necessario rinnovare i certificati se è stato configurato il gateway applicazione per la crittografia TLS/SSL.

È possibile rinnovare un certificato associato a un listener che usa il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure:

## <a name="azure-portal"></a>Portale di Azure

Per rinnovare un certificato di listener dal portale, passare ai listener del gateway dell'applicazione. Selezionare il listener che dispone di un certificato che deve essere rinnovato, quindi selezionare **rinnova o modifica certificato selezionato**.

:::image type="content" source="media/renew-certificate/ssl-cert.png" alt-text="Rinnovare il certificato":::

Caricare il nuovo certificato PFX, assegnargli un nome, digitare la password e quindi selezionare **Save (Salva**).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per rinnovare il certificato tramite Azure PowerShell, usare lo script seguente:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare l'offload TLS con applicazione Azure gateway, vedere [configurare l'OFFLOAD TLS](./create-ssl-portal.md)