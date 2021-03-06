---
title: Convertire un peering di Exchange legacy in una risorsa di Azure tramite PowerShell
titleSuffix: Azure
description: Convertire un peering di Exchange legacy in una risorsa di Azure tramite PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: acc32f4916f5f7f8fe22eebdd1e72db297cac94c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590206"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Convertire un peering di Exchange legacy in una risorsa di Azure tramite PowerShell

Questo articolo descrive come convertire un peering di Exchange legacy esistente in una risorsa di Azure usando i cmdlet di PowerShell.

Se si preferisce, è possibile completare questa guida usando il [portale](howto-legacy-exchange-portal.md)di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
* Prima di iniziare la configurazione, esaminare i [prerequisiti](prerequisites.md) e la [procedura dettagliata per il peering di Exchange](walkthrough-exchange-all.md).

### <a name="work-with-azure-powershell"></a>Usare Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Convertire un peering di Exchange legacy in una risorsa di Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Ottenere peering Exchange legacy per la conversione
Questo esempio illustra come ottenere il peering di Exchange legacy nella località di peering di Seattle:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

La risposta sarà simile all'esempio di seguito:
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Converti peering legacy
Questo comando può essere usato per convertire il peering di Exchange legacy in una risorsa di Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Quando si converte il peering legacy in una risorsa di Azure, le modifiche non sono supportate.
&nbsp;

Questa risposta di esempio mostra quando il provisioning end-to-end è stato completato correttamente:

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>Risorse aggiuntive
È possibile ottenere descrizioni dettagliate di tutti i parametri eseguendo il comando seguente:

```powershell
Get-Help Get-AzPeering -detailed
```
Per altre informazioni, vedere [Domande frequenti sul peering Internet](faqs.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare o modificare un peering di Exchange tramite PowerShell](howto-exchange-powershell.md)
