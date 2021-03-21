---
title: Creare un IP pubblico-portale di Azure
description: Informazioni su come creare un indirizzo IP pubblico nella portale di Azure
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: e6b7648188e2307da4ef40e0ab3daf6201f9d89d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694870"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Creare un indirizzo IP pubblico usando il portale di Azure

Questo articolo illustra come creare una risorsa indirizzo IP pubblico usando il portale di Azure. 

Per altre informazioni sulle risorse a cui è possibile associare l'indirizzo IP pubblico e la differenza tra gli SKU Basic e standard, vedere [indirizzi IP pubblici](./public-ip-addresses.md). 

Questo articolo è incentrato sugli indirizzi IPv4. Per altre informazioni sugli indirizzi IPv6, vedere [IPv6 per Azure VNet](./ipv6-overview.md).

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-create-public-ip-standard-zones)

Usare la procedura seguente per creare un indirizzo IP pubblico con ridondanza della zona standard denominato **myStandardZRPublicIP**.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Crea una risorsa**. 
3. Nella casella di ricerca immettere **indirizzo IP pubblico**. Selezionare **indirizzo IP pubblico** nei risultati della ricerca.
4. Nella pagina **indirizzo IP pubblico** selezionare **Crea**.
5. Nella pagina **Crea indirizzo IP pubblico** immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | valore                       |
    | ---                     | ---                         |
    | Versione indirizzo IP              | Selezionare IPv4                 |    
    | SKU                     | selezionare **Standard**         |
    | Livello                   | Selezionare la **regione**         |
    | Nome                    | Immettere **myStandardZRPublicIP**          |
    | Assegnazione indirizzi IP   | Nota Questa selezione è bloccata come "statica"                                        |
    | Preferenza di routing      | Lasciare l'impostazione predefinita **rete Microsoft**. </br> Per ulteriori informazioni sulle preferenze di routing, vedere informazioni sulle preferenze di [routing (anteprima)](./routing-preference-overview.md). |
    | Timeout di inattività (minuti)  | Lasciare il valore predefinito **4**.        |
    | Etichetta del nome DNS          | Lasciare vuoto il valore.    |
    | Subscription            | Selezionare la propria sottoscrizione.   |
    | Resource group          | Selezionare **Crea nuovo**, immettere **myResourceGroup**. </br> Selezionare **OK**. |
    | Location                | Selezionare **Stati Uniti orientali 2**      |
    | Zona di disponibilità       | Selezionare la zona con **ridondanza della zona**, nessuna zona o seleziona zona specifica (vedere la nota sotto) |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Crea indirizzo IP standard in portale di Azure" border="false":::

> [!NOTE]
> Queste selezioni sono valide nelle aree con [zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones). </br>
È possibile selezionare una zona specifica in queste aree, anche se non sarà resiliente agli errori di zona. </br> Per altre informazioni sulle zone di disponibilità, vedere [Cenni preliminari sulle zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview).

\* = Il livello è correlato alla funzionalità di [bilanciamento del carico tra aree](../load-balancer/cross-region-overview.md) , attualmente in anteprima.

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-create-public-ip-basic)

In questa sezione viene creato un indirizzo IP pubblico di base denominato **myBasicPublicIP**. 

> [!NOTE]
> Gli indirizzi IP pubblici di base non supportano le zone di disponibilità.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Crea una risorsa**. 
3. Nella casella di ricerca immettere **indirizzo IP pubblico**. Selezionare **indirizzo IP pubblico** nei risultati della ricerca.
4. Nella pagina **indirizzo IP pubblico** selezionare **Crea**.
5. Nella pagina **Crea indirizzo IP pubblico** immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | valore                       |
    | ---                     | ---                         |
    | Versione indirizzo IP              | Selezionare IPv4                 |    
    | SKU                     | Selezionare **Basic**         |
    | Nome                    | Immettere *myBasicPublicIP*          |
    | Assegnazione indirizzi IP   | Seleziona **statico** (vedere la nota sotto)                                     |
    | Timeout di inattività (minuti)  | Lasciare il valore predefinito **4**.       |
    | Etichetta del nome DNS          | Lasciare vuoto il valore    |
    | Subscription            | Selezionare la propria sottoscrizione.   |
    | Resource group          | Selezionare **Crea nuovo**, immettere **myResourceGroup**. </br> Selezionare **OK**. |
    | Location                | Selezionare **Stati Uniti orientali 2**      |

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Crea indirizzo IP standard in portale di Azure" border="false":::

Se è accettabile che l'indirizzo IP cambi nel tempo, è possibile selezionare Assegnazione IP **dinamica** .

---

## <a name="additional-information"></a>Informazioni aggiuntive 

Per altre informazioni sui singoli campi elencati sopra, vedere [gestire gli indirizzi IP pubblici](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Passaggi successivi
- Associare un [indirizzo IP pubblico a una macchina virtuale](./associate-public-ip-address-vm.md#azure-portal)
- Altre informazioni sugli [indirizzi IP pubblici](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Altre informazioni su tutte le [impostazioni relative agli indirizzi IP pubblici](virtual-network-public-ip-address.md#create-a-public-ip-address).