---
title: Reimposta il gateway VPN o la connessione per ristabilire il tunnel IPsec
titleSuffix: Azure VPN Gateway
description: Reimpostare una connessione o un gateway VPN per ristabilire i tunnel IPsec.
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: adc2ffd63d73baaddce00324787df61061ea69dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726641"
---
# <a name="reset-a-vpn-gateway-or-a-connection"></a>Reimpostare un gateway VPN o una connessione

La reimpostazione di una connessione gateway o gateway VPN di Azure è utile se si perde la connettività VPN cross-premise in uno o più tunnel VPN da sito a sito. In questa situazione tutti i dispositivi VPN funzionano correttamente, ma non sono in grado di stabilire tunnel IPsec con i gateway VPN di Azure. Questo articolo consente di reimpostare un gateway VPN o una connessione gateway.

## <a name="what-happens-during-a-reset"></a>Cosa accade durante una reimpostazione

### <a name="gateway-reset"></a>Reimpostazione del gateway

Un gateway VPN di Azure è costituito da due istanze di macchina virtuale in esecuzione in una configurazione di standby attivo. Quando si reimposta il gateway, quest'ultimo viene riavviato e gli vengono riapplicate le configurazioni cross-premise. Il gateway mantiene l'indirizzo IP pubblico già disponibile. Non sarà quindi necessario aggiornare la configurazione del router VPN con un nuovo indirizzo IP pubblico per il gateway VPN di Azure.

Dopo l'emissione del comando per ripristinare il gateway, l'istanza attualmente attiva del gateway VPN di Azure viene riavviata immediatamente. Si verificherà un breve intervallo durante il failover dall'istanza attiva (in fase di riavvio) all'istanza di standby. L'intervallo dovrebbe essere inferiore a un minuto.

Se la connessione non viene ripristinata dopo il primo riavvio, emettere di nuovo lo stesso comando per riavviare la seconda istanza della VM, ovvero il nuovo gateway attivo. Se vengono richiesti due riavvii uno dopo l'altro, il periodo necessario per il riavvio di entrambe le istanze della VM (attiva e di standby) sarà leggermente più lungo. Ciò causerà un gap più lungo sulla connettività VPN, fino a un massimo di 30-45 minuti affinché le VM completino il riavvio.

Dopo due riavvii, se si verificano ancora problemi di connettività cross-premise, aprire una richiesta di supporto dal portale di Azure.

### <a name="connection-reset"></a>Connection reset (Connessione ripristinata)

Quando si sceglie di reimpostare una connessione, il gateway non viene riavviato. Solo la connessione selezionata viene reimpostata e ripristinata.

## <a name="reset-a-connection"></a>Reimpostare una connessione

È possibile reimpostare facilmente una connessione utilizzando la portale di Azure.

1. Passare alla **connessione** che si vuole reimpostare. È possibile trovare la risorsa di connessione individuando l'oggetto in **tutte le risorse** oppure passando al **nome Gateway '-> connections->' Connection Name '**
1. Nella pagina **connessione** selezionare **Reimposta** dal menu a sinistra.
1. Nella pagina **Reimposta** fare clic su **Reimposta** per reimpostare la connessione.

   :::image type="content" source="./media/reset-gateway/reset-connection.png" alt-text="Screenshot che mostra la reimpostazione.":::

## <a name="reset-a-vpn-gateway"></a>Reimpostare un gateway VPN

Prima di reimpostare il gateway, verificare gli elementi chiave elencati di seguito per ogni tunnel VPN da sito a sito (S2S) IPsec. Eventuali mancate corrispondenze negli elementi provocherà la disconnessione dei tunnel VPN S2S. La verifica e la correzione delle configurazioni per i gateway locali e VPN di Azure evitano riavvii non necessari e interruzioni per le altre connessioni funzionanti nei gateway.

Verificare gli elementi seguenti prima di reimpostare il gateway:

* Gli indirizzi IP virtuali per il gateway VPN di Azure e il gateway VPN locale devono essere configurati correttamente nei criteri VPN di Azure e locali.
* La chiave precondivisa deve essere uguale nei gateway VPN di Azure e locali.
* Se si applica una configurazione IPsec/IKE specifica, ad esempio la crittografia, gli algoritmi hash e PFS (Perfect Forward Secrecy), assicurarsi che i gateway VPN di Azure e locali abbiano le stesse configurazioni.

### <a name="azure-portal"></a><a name="portal"></a>Portale di Azure

È possibile reimpostare un gateway VPN di Resource Manager tramite il portale di Azure. Se si vuole reimpostare un gateway classico, vedere la procedura di PowerShell per il [modello di distribuzione classica](#resetclassic).

[!INCLUDE [portal steps](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="powershell"></a><a name="ps"></a>PowerShell

#### <a name="resource-manager-deployment-model"></a>Modello di distribuzione di Gestione risorse

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il cmdlet per la reimpostazione di un gateway è **Reset-AzVirtualNetworkGateway**. Prima di eseguire una reimpostazione, verificare di avere la versione più recente dei [cmdlet di PowerShell AZ](/powershell/module/az.network). Nell'esempio seguente viene ripristinato un gateway di rete virtuale denominato VNet1GW nel gruppo di risorse TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Risultato:

Quando si riceve un valore restituito, si può presupporre che il ripristino del gateway abbia avuto esito positivo. Tuttavia, non c'è alcun elemento nel risultato restituito che indica in modo esplicito che il ripristino è riuscito. Se si desidera esaminare attentamente la cronologia per trovare il momento preciso in cui si è verificato il ripristino del gateway, è possibile visualizzare l'informazione nel [portale di Azure](https://portal.azure.com). Nel portale, passare a **"GatewayName" -> Integrità risorse**.

#### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Modello di distribuzione classica

Il cmdlet per la reimpostazione di un gateway è **Reset-AzureVNetGateway**. I cmdlet di Azure PowerShell per la gestione dei servizi devono essere installati localmente sul desktop. Non è possibile usare Azure Cloud Shell. Prima di eseguire un ripristino assicurarsi di avere la versione più recente dei [cmdlet di PowerShell per Gestione servizi](/powershell/azure/servicemanagement/install-azure-ps#azure-service-management-cmdlets). Quando si usa questo comando, assicurarsi di usare il nome completo della rete virtuale. I reti virtuali classici creati con il portale hanno un nome lungo che è necessario per PowerShell. È possibile visualizzare il nome lungo usando "Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml".

L'esempio seguente reimposta il gateway per una rete virtuale denominata "Group TestRG1 TestVNet1" (che mostra semplicemente "TestVNet1" nel portale):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

Risultato:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

### <a name="azure-cli"></a><a name="cli"></a>

Per reimpostare il gateway, usare il comando [az network vnet-gateway reset](/cli/azure/network/vnet-gateway). Nell'esempio seguente viene ripristinato un gateway di rete virtuale denominato VNet5GW nel gruppo di risorse TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Risultato:

Quando si riceve un valore restituito, si può presupporre che il ripristino del gateway abbia avuto esito positivo. Tuttavia, non c'è alcun elemento nel risultato restituito che indica in modo esplicito che il ripristino è riuscito. Se si desidera esaminare attentamente la cronologia per trovare il momento preciso in cui si è verificato il ripristino del gateway, è possibile visualizzare l'informazione nel [portale di Azure](https://portal.azure.com). Nel portale, passare a **"GatewayName" -> Integrità risorse**.
