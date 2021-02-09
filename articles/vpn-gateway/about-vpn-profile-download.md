---
title: 'Gateway VPN di Azure: Informazioni sui profili client VPN da punto a sito'
description: Usare questo articolo per trovare le informazioni necessarie per un profilo client VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb66363d525648df08f32451842402ad1d0d93b
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979072"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>Uso dei file di profilo client VPN P2S

I file del profilo contengono informazioni necessarie per configurare una connessione VPN. Questo articolo consente di ottenere e comprendere le informazioni necessarie per un profilo client VPN.

## <a name="generate-and-download-profile"></a>Generare e scaricare il profilo

È possibile generare i file di configurazione client usando PowerShell oppure il portale di Azure. Entrambi i metodi restituiscono lo stesso file con estensione zip.

### <a name="portal"></a>Portale

1. Nel portale di Azure passare al gateway di rete virtuale per la rete virtuale a cui ci si vuole connettere.
1. Nella pagina gateway di rete virtuale selezionare **configurazione da punto a sito**.
1. Nella parte superiore della pagina configurazione da punto a sito selezionare **Scarica client VPN**. La generazione del pacchetto di configurazione client richiede qualche minuto.
1. Il browser indica che è disponibile un file con estensione zip per la configurazione client, che ha lo stesso nome del gateway. Decomprimere il file per visualizzare le cartelle.

### <a name="powershell"></a>PowerShell

Per generare usando PowerShell, è possibile usare l'esempio seguente:

1. Quando si generano file di configurazione del client VPN, il valore di '-AuthenticationMethod' è 'EapTls'. Generare i file di configurazione del client VPN con il comando seguente:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Copiare l'URL nel browser per scaricare il file con estensione zip, quindi decomprimere il file per visualizzare le cartelle.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* La **cartella OpenVPN** contiene il profilo *ovpn* che deve essere modificato perché includa la chiave e il certificato. Per altre informazioni, vedere [Configurare i client OpenVPN per il gateway VPN di Azure](vpn-gateway-howto-openvpn-clients.md#windows). Se nel gateway VPN è selezionata l'autenticazione di Azure AD, questa cartella non è presente nel file ZIP. Passare invece alla cartella AzureVPN e individuare azurevpnconfig.xml.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle connessioni da punto a sito, vedere [Informazioni sulla VPN da punto a sito](point-to-site-about.md).
