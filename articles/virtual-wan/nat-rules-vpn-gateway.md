---
title: Configurare le regole NAT VPN per il gateway
titleSuffix: Azure Virtual WAN
description: Informazioni su come configurare le regole NAT per il gateway VPN VWAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cherylmc
ms.openlocfilehash: a31b3718eb1baa32aef39474383924efe8cf93b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745867"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Configurare le regole NAT per il gateway VPN WAN virtuale-anteprima

> [!IMPORTANT]
> Le regole NAT sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile configurare il gateway VPN WAN virtuale con regole NAT uno-a-uno statiche. Una regola NAT fornisce un meccanismo per configurare una conversione uno-a-uno degli indirizzi IP. È possibile utilizzare NAT per collegare due reti IP con indirizzi IP incompatibili o sovrapposti. Uno scenario tipico è il ramo con indirizzi IP sovrapposti che vogliono accedere alle risorse di Azure VNet.

Questa configurazione usa una tabella di flusso per instradare il traffico da un indirizzo IP esterno (host) a un indirizzo IP interno associato a un endpoint all'interno di una rete virtuale (macchina virtuale, computer, contenitore e così via).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagramma che mostra l'architettura.":::

## <a name="configure-and-view-rules"></a><a name="view"></a>Configurare e visualizzare le regole

È possibile configurare e visualizzare le regole NAT nelle impostazioni del gateway VPN in qualsiasi momento.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Screenshot che illustra le regole di modifica.":::

1. Passare all'hub virtuale.
1. Selezionare **VPN (da sito a sito)**.
1. Selezionare **regole NAT (modifica)**.
1. Nella pagina **Modifica regola NAT** è possibile aggiungere, **modificare o eliminare** una regola NAT usando i valori seguenti:

   * **Nome:** Nome univoco per la regola NAT.
   * **Tipo:** Statico. Un NAT statico uno-a-uno stabilisce una relazione uno-a-uno tra un indirizzo interno e un indirizzo esterno.
   * **Modalità:** IngressSnat o EgressSnat.  
      * La modalità IngressSnat (nota anche come origine in ingresso NAT) è applicabile al traffico che accede al gateway VPN da sito a sito dell'hub di Azure.
      * La modalità EgressSnat (nota anche come origine in uscita NAT) è applicabile al traffico che lascia il gateway VPN da sito a sito dell'hub di Azure.
   * **InternalMapping:** Un intervallo di prefisso dell'indirizzo di indirizzi IP di origine nella rete interna di cui verrà eseguito il mapping a un set di indirizzi IP esterni. In altre parole, l'intervallo di prefisso dell'indirizzo pre-NAT.
   * **ExternalMapping:** Un intervallo di prefisso dell'indirizzo degli indirizzi IP di destinazione nella rete esterna a cui verrà eseguito il mapping degli indirizzi IP di origine. In altre parole, l'intervallo di prefisso dell'indirizzo post-NAT.
   * **Connessione collegamento:** Risorsa di connessione che connette virtualmente un sito VPN al gateway VPN da sito a sito dell'hub di Azure.

### <a name="configuration-considerations"></a>Considerazioni sulla configurazione

* Le dimensioni della subnet per il mapping interno ed esterno devono essere le stesse per il NAT statico uno-a-uno.
* Assicurarsi di modificare il sito VPN nel portale di Azure per aggiungere i prefissi **ExternalMapping** nel campo "spazio indirizzi privato". Attualmente, i siti in cui è abilitato BGP devono assicurarsi che il annunciatore BGP locale (Impostazioni BGP del dispositivo) includa una voce per i prefissi di mapping esterno.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle configurazioni da sito a sito, vedere [configurare una connessione da sito a sito WAN virtuale](virtual-wan-site-to-site-portal.md).
