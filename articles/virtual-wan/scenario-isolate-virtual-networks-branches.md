---
title: 'Scenario: isolamento personalizzato per reti virtuali e Branch'
titleSuffix: Azure Virtual WAN
description: 'Scenari per il routing: impedire che i reti virtuali e i rami selezionati siano in grado di raggiungere gli altri.'
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: wellee
ms.openlocfilehash: e8e5a5a1b9325f40fdd51133155a0daffaa55a7b
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408495"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>Scenario: isolamento personalizzato per reti virtuali e Branch

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In uno scenario di isolamento personalizzato per le reti virtuali (reti virtuali) e i rami, l'obiettivo è impedire a un set specifico di reti virtuali di raggiungere un altro set di reti virtuali. Analogamente, i rami (VPN/ER/VPN utente) possono raggiungere solo determinati set di reti virtuali.

Viene anche introdotto il requisito aggiuntivo che il firewall di Azure deve ispezionare Branch per VNET e creare branch per il traffico VNET, ma **non**  VNET al traffico vnet.  

Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Progettazione

Per determinare il numero di tabelle di route necessarie, è possibile creare una matrice di connettività. Per questo scenario l'aspetto sarà simile al seguente, in cui ogni cella indica se un'origine (riga) può comunicare con una destinazione (colonna):

| Da | Con:| *Reti virtuali blu* | *Reti virtuali rosso* | *Rami rossi*| *Rami blu*| 
|---|---|---|---|---|---|
| **Reti virtuali blu** |   &#8594;|   Connessione diretta     |           |   |  AzFW|
| **Reti virtuali rosso**  |   &#8594;|              |   Connessione diretta  |  AzFW  | 
| **Rami rossi**   |   &#8594;|   |   AzFW  |  Connessione diretta | Connessione diretta
| **Rami blu**| &#8594;| AzFW  |   |Connessione diretta   | Connessione diretta

Ognuna delle celle della tabella precedente descrive se una connessione WAN virtuale (il lato "da" del flusso, le intestazioni di riga) comunica con una destinazione (il lato "a" del flusso, le intestazioni di colonna in corsivo). **Direct** implica che il traffico fluisce direttamente attraverso la rete WAN virtuale, mentre **AzFW** implica che il traffico viene controllato dal firewall di Azure prima di essere trasmesso alla destinazione. Una voce vuota indica che il flusso è bloccato durante l'installazione.

In questo caso, le due tabelle di route per reti virtuali sono necessarie per raggiungere l'obiettivo dell'isolamento VNet senza il firewall di Azure nel percorso. Queste tabelle route vengono chiamate **RT_BLUE** e **RT_RED**.

Inoltre, i rami devono essere sempre associati alla tabella di route  **predefinita** . Per assicurarsi che il traffico da e verso i rami venga controllato dal firewall di Azure, vengono aggiunte route statiche nelle tabelle predefinite **RT_RED** e **RT_BLUE** route che puntano il traffico al firewall di Azure e configurano le regole di rete per consentire il traffico desiderato. Si garantisce inoltre che i rami **non** vengano propagati a **RT_BLUE** e **RT_RED**.

Di conseguenza, si tratta della progettazione finale:

* Reti virtuali blu:
  * Tabella di route associata: **RT_BLUE**
  * Propagazione alle tabelle di route: **RT_BLUE**
* Reti virtuali rosse:
  * Tabella di route associata: **RT_RED**
  * Propagazione alle tabelle di route: **RT_RED** 
* Rami
  * Tabella di route associata: **predefinita**
  * Propagazione alle tabelle di route: **predefinita**
* Route statiche:
    * **Tabella di route predefinita**: spazi di indirizzi della rete virtuale con il firewall di Azure hop successivo
    * **RT_RED**: 0.0.0.0/0 con il firewall di Azure hop successivo
    * **RT_BLUE**: 0.0.0.0/0 con il firewall di Azure hop successivo
* Regole di rete del firewall:
    * **Consenti** **prefisso origine** regola: **prefisso di destinazione** dei prefissi di indirizzo blu: prefissi VNet blu 
    * **Consenti**  **prefisso origine** regola: **prefisso di destinazione** prefissi di indirizzo Red Branch: prefissi VNET rossi

> [!NOTE]
> Poiché è necessario associare tutti i rami alla tabella di route predefinita, nonché propagarsi allo stesso set di tabelle di routing, tutti i rami avranno lo stesso profilo di connettività. In altre parole, non è possibile applicare il concetto rosso/blu per reti virtuali ai rami. Tuttavia, per ottenere il routing personalizzato per i rami, è possibile trasmettere il traffico dai rami al firewall di Azure.

> [!NOTE]
> Per impostazione predefinita, il firewall di Azure nega il traffico in un modello con attendibilità zero. Se non è presente alcuna regola **Consenti** esplicita corrispondente al pacchetto ispezionato, il pacchetto verrà eliminato da Azure firewall.

Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).



## <a name="workflow"></a><a name="architecture"></a>Flusso di lavoro

Nella **Figura 1** sono presenti reti virtuali blu e rosso, oltre a rami che possono accedere a reti virtuali blu o rosso.

* I reti virtuali con connessione blu possono raggiungere reciprocamente e possono raggiungere tutte le connessioni Blue Branch (VPN/ER/P2S). Nel diagramma il ramo blu è il sito VPN da sito a sito.
* I reti virtuali con connessione rossa possono raggiungere reciprocamente e possono raggiungere tutte le connessioni a rami rossi (VPN/ER/P2S). Nel diagramma il ramo rosso è gli utenti VPN da punto a sito.

Quando si configura il routing, tenere presente i passaggi seguenti.

1. Creare due tabelle di route personalizzate nell'portale di Azure, **RT_BLUE** e **RT_RED** per personalizzare il traffico a questi reti virtuali.
2. Per **RT_BLUE** della tabella di route, applicare le impostazioni seguenti per assicurarsi che reti virtuali blu apprenda i prefissi degli indirizzi di tutti gli altri reti virtuali blu.:
   * **Associazione**: selezionare tutti reti virtuali blu.
   * **Propagazione**: selezionare tutti reti virtuali blu.
3. Ripetere gli stessi passaggi per **RT_RED** tabella di route per reti virtuali rosse.
4. Eseguire il provisioning di un firewall di Azure in WAN virtuale. Per altre informazioni sul firewall di Azure nell'hub WAN virtuale, vedere [configurazione del firewall di Azure nell'hub WAN virtuale](howto-firewall.md).
5. Aggiungere una route statica alla tabella di route **predefinita** dell'hub virtuale che indirizza tutto il traffico destinato agli spazi di indirizzi VNET (sia blu che rosso) al firewall di Azure. Questo passaggio garantisce che tutti i pacchetti dai rami vengano inviati al firewall di Azure per l'ispezione.
    * Esempio: **prefisso di destinazione**: 10.0.0.0/24 **hop successivo**: firewall di Azure
    >[!NOTE]
    > Questo passaggio può essere eseguito anche tramite gestione firewall selezionando l'opzione "traffico privato protetto". Verrà aggiunta una route per tutti gli indirizzi IP privati RFC1918 applicabili a reti virtuali e a rami. È necessario aggiungere manualmente i rami o le reti virtuali che non sono conformi a RFC1918. 

6. Aggiungere una route statica per **RT_RED** e **RT_BLUE** indirizzare tutto il traffico al firewall di Azure. Questo passaggio garantisce che reti virtuali non sarà in grado di accedere direttamente ai rami. Questo passaggio non può essere eseguito tramite gestione firewall perché queste reti virtuali non sono associate alla tabella di route predefinita.
    * Esempio: **prefisso di destinazione**: 0.0.0.0/0 **hop successivo**: firewall di Azure

    > [!NOTE]
    > Il routing viene eseguito usando la corrispondenza del prefisso più lungo (LPM). Di conseguenza, le route statiche 0.0.0.0/0 **non** verranno preferite rispetto ai prefissi esatti presenti in **BLUE_RT** e **RED_RT**. Di conseguenza, il traffico intra-VNET non verrà controllato dal firewall di Azure.

Questo comporterà la modifica della configurazione del routing come illustrato nella figura seguente.

**Figura 1** 
 [ ![ Figura 1 ](./media/routing-scenarios/custom-branch-vnet/custom-branch.png)](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
