---
title: Configurare le regole NAT VPN per il gateway
titleSuffix: Azure Virtual WAN
description: Informazioni su come configurare le regole NAT per il gateway VPN VWAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6fbee31f015953bd7e65648ea273e3ca84686115
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431217"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Configurare le regole NAT per il gateway VPN WAN virtuale-anteprima

> [!IMPORTANT]
> Le regole NAT sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile configurare il gateway VPN WAN virtuale con regole NAT uno-a-uno statiche. Una regola NAT fornisce un meccanismo per configurare una conversione uno-a-uno degli indirizzi IP. È possibile utilizzare NAT per collegare due reti IP con indirizzi IP incompatibili o sovrapposti. Uno scenario tipico è il ramo con indirizzi IP sovrapposti che vogliono accedere alle risorse di Azure VNet.

Questa configurazione usa una tabella di flusso per instradare il traffico da un indirizzo IP esterno (host) a un indirizzo IP interno associato a un endpoint all'interno di una rete virtuale (macchina virtuale, computer, contenitore e così via).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagramma che mostra l'architettura.":::

## <a name="configure-nat-rules"></a><a name="rules"></a>Configurare le regole NAT

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

### <a name="configuration-considerations"></a><a name="considerations"></a>Considerazioni sulla configurazione

* Le dimensioni della subnet per il mapping interno ed esterno devono essere le stesse per il NAT statico uno-a-uno.
* Assicurarsi di modificare il sito VPN nel portale di Azure per aggiungere i prefissi **ExternalMapping** nel campo "spazio indirizzi privato". Attualmente, i siti in cui è abilitato BGP devono assicurarsi che il annunciatore BGP locale (Impostazioni BGP del dispositivo) includa una voce per i prefissi di mapping esterno.

## <a name="examples-and-verification"></a><a name="examples"></a>Esempi e verifica

### <a name="ingress-mode-nat"></a>NAT in modalità ingresso

Le regole NAT in modalità ingresso vengono applicate ai pacchetti che accedono ad Azure tramite il gateway VPN da sito a sito WAN virtuale. In questo scenario si vuole connettere due branch VPN da sito a sito ad Azure. Il sito VPN 1 si connette tramite Link1 e il sito VPN 2 si connette tramite il collegamento 2. Ogni sito ha lo spazio degli indirizzi 192.169.1.0/24.

Il diagramma seguente mostra il risultato finale proiettato:

:::image type="content" source="./media/nat-rules-vpn-gateway/ingress.png" alt-text="Diagramma che mostra la modalità di ingresso NAT.":::

1. Specificare una regola NAT.

   Specificare una regola NAT per verificare che il gateway VPN da sito a sito sia in grado di distinguere tra i due rami con spazi di indirizzi sovrapposti, ad esempio 192.168.1.0/24. In questo esempio, l'attenzione è dedicata a Link1 per il sito VPN 1.

   La regola NAT seguente può essere configurata e associata al collegamento 1 di uno dei rami. Poiché si tratta di una regola NAT statica, gli spazi di indirizzi di InternalMapping e ExternalMapping contengono lo stesso numero di indirizzi IP.

   * **Nome:** IngressRule01
   * **Tipo:** Statico
   * **Modalità:** IngressSnat
   * **InternalMapping:** 192.168.1.0/24
   * **ExternalMapping:** 10.1.1.0/24
   * **Connessione collegamento:** Collegamento 1

1. Annunciare il ExternalMapping corretto.

   In questo passaggio, assicurarsi che il gateway VPN da sito a sito annunci lo spazio degli indirizzi ExternalMapping corretto al resto delle risorse di Azure. Sono disponibili diverse istruzioni, a seconda che BGP sia abilitato o meno.

   **Esempio 1: BGP è abilitato**

   * Verificare che l'altoparlante BGP locale che si trova nel sito VPN 1 sia configurato per annunciare lo spazio degli indirizzi 10.1.1.0/24.
   * Durante questa fase di anteprima, i siti in cui è abilitato BGP devono assicurarsi che il annunciatore BGP locale (Impostazioni BGP del dispositivo) includa una voce per i prefissi di mapping esterno.

   **Esempio 2: BGP non è abilitato**

   * Passare alla risorsa dell'hub virtuale che contiene il gateway VPN da sito a sito. Nella pagina hub virtuale in **connettività** selezionare **VPN (da sito a sito)**.
   * Selezionare il sito VPN connesso all'hub WAN virtuale tramite il collegamento 1. Selezionare **modifica sito** e input 10.1.1.0/24 come spazio di indirizzi privato per il sito VPN.

     :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site.png" alt-text="Screenshot che mostra la pagina Modifica sito VPN.":::

### <a name="packet-flow"></a>Flusso di pacchetti

In questo esempio, un dispositivo locale vuole raggiungere una rete virtuale spoke. Il flusso di pacchetti è il seguente, con le traduzioni NAT in grassetto.

1. Viene avviato il traffico dall'ambiente locale.
   * Indirizzo IP di origine: **192.168.1.1**
   * Indirizzo IP di destinazione: 30.0.0.1
1. Il traffico immette il gateway da sito a sito e viene convertito usando la regola NAT e quindi inviato al spoke.
   * Indirizzo IP di origine: **10.1.1.1**
   * Indirizzo IP di destinazione: 30.0.0.1
1. Risposta da spoke avviata.
   * Indirizzo IP di origine: 30.0.0.1
   * Indirizzo IP di destinazione: **10.1.1.1**
1. Il traffico entra nel gateway VPN da sito a sito e la traduzione viene annullata e inviata a locale.
   * Indirizzo IP di origine: 30.0.0.1
   * Indirizzo IP di destinazione: **192.168.1.1**

### <a name="verification-checks"></a>Controlli di verifica

In questa sezione vengono illustrati i controlli per verificare che la configurazione sia impostata correttamente.

#### <a name="validate-defaultroutetable-rules-and-routes"></a>Convalidare DefaultRouteTable, regole e Route

I rami nella rete WAN virtuale si associano a **DefaultRouteTable**, implicando che tutte le connessioni di rami apprendano le route popolate all'interno di DefaultRouteTable. Viene visualizzata la regola NAT con il prefisso di mapping esterno nelle route valide del DefaultRouteTable.

Esempio:

* **Prefisso:** 10.1.1.0/24  
* **Tipo hop successivo:** VPN_S2S_Gateway
* **Hop successivo:** Risorsa VPN_S2S_Gateway

#### <a name="validate-address-prefixes"></a>Convalidare i prefissi degli indirizzi

Questo esempio si applica alle risorse nelle reti virtuali associate a DefaultRouteTable.

Le **route valide** sulle schede di interfaccia di rete (NIC) di qualsiasi macchina virtuale che si trova in una rete virtuale spoke connessa all'hub WAN virtuale devono contenere anche i prefissi di indirizzo delle regole NAT **ExternalMapping**.

#### <a name="validate-bgp-advertisements"></a>Convalidare gli annunci BGP

Se si dispone di BGP configurato sulla connessione al sito VPN, controllare l'altoparlante BGP locale per assicurarsi che venga annunciata una voce per i prefissi di mapping esterno.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle configurazioni da sito a sito, vedere [configurare una connessione da sito a sito WAN virtuale](virtual-wan-site-to-site-portal.md).
