---
title: includere file
description: includere file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dbc4d73ab5faee89895f5bd06349dfe6bc8bfd5d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627711"
---
1. Nella pagina del portale per la rete WAN virtuale, nella sezione **Connettività** selezionare **Siti VPN** per aprire la pagina Siti VPN.
2. Nella pagina **Siti VPN** fare clic su **+Crea sito**.

   ![Screenshot mostra la finestra da sito a sito con il riquadro Crea sito VPN aperto.](./media/virtual-wan-tutorial-site-include/basics.png "Nozioni di base")
3. Nella pagina **Crea sito WAN**, nella scheda **Nozioni di base**, compilare i campi seguenti:

    * **Regione** - Precedentemente denominata posizione. Si tratta del percorso in cui si vuole creare la risorsa del sito.
    * **Nome** - Nome che si vuole usare per fare riferimento al sito locale.
    * **Fornitore del dispositivo** - Nome del fornitore del dispositivo VPN (ad esempio: Citrix, Cisco, Barracuda). L'aggiunta del fornitore del dispositivo può aiutare il team di Azure a comprendere meglio l'ambiente per aggiungere altre possibilità di ottimizzazione in futuro o per semplificare la risoluzione dei problemi.
    * **Border Gateway Protocol** : l'impostazione **Abilita** implica che tutte le connessioni dal sito saranno abilitate per BGP. Verranno infine configurate le informazioni BGP per ogni collegamento dal sito VPN nella sezione collegamenti. La configurazione di BGP in una rete WAN virtuale equivale alla configurazione di BGP in una VPN del gateway di rete virtuale di Azure. L'indirizzo del peer BGP locale non deve essere uguale all'indirizzo IP pubblico del dispositivo VPN né allo spazio di indirizzi della rete virtuale del sito VPN. Usare un indirizzo IP diverso nel dispositivo VPN per il peer BGP. Può trattarsi di un indirizzo assegnato all'interfaccia di loopback nel dispositivo. Specificare questo indirizzo nel sito VPN corrispondente che rappresenta la posizione. Per i prerequisiti di BGP, vedere [Informazioni su BGP con i gateway VPN di Azure](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). È sempre possibile modificare una connessione VPN per aggiornare i parametri BGP (indirizzo IP del peering sul collegamento e il #) dopo che l'impostazione BGP del sito VPN è abilitata.
    * **Spazio indirizzi privato** - Spazio di indirizzi IP nel sito locale. Il traffico destinato a questo spazio di indirizzi viene indirizzato al sito locale. Questa operazione è necessaria quando BGP non è abilitato per il sito.
    
      >[!NOTE]
      >Se si modifica lo spazio di indirizzi dopo aver creato il sito, ad esempio si aggiunge un altro spazio di indirizzi, l'aggiornamento delle route effettive può richiedere 8-10 minuti mentre i componenti vengono ricreati.
      >
    * **Hub** - Hub a cui si vuole connettere il sito. Un sito può essere connesso solo agli hub con gateway VPN. Se non viene visualizzato un hub, creare prima un gateway VPN in tale hub.
4. Selezionare **Collegamenti** per aggiungere informazioni sui collegamenti fisici al ramo. Se è presente un dispositivo CPE del partner WAN virtuale, verificarlo per verificare se queste informazioni vengono scambiate con Azure nell'ambito del caricamento delle informazioni sui Branch configurate dai rispettivi sistemi.

   ![Screenshot mostra il riquadro Crea sito VPN con la scheda collegamenti selezionata.](./media/virtual-wan-tutorial-site-include/links.png "Collegamenti")

    * **Nome collegamento** - Nome che si desidera fornire per il collegamento fisico al sito VPN. Esempio: mylink1.
    * **Nome provider** - Nome del collegamento fisico al sito VPN. Esempio: ATT, Verizon.
    * **Velocità** - Velocità del dispositivo VPN in corrispondenza della località del ramo. Esempio: 50, che indica 50 Mbps è la velocità del dispositivo VPN nella sede del ramo.
    * **Indirizzo IP/FQDN** - Indirizzo IP pubblico del dispositivo locale con questo collegamento. Facoltativamente, è possibile specificare l'indirizzo IP privato del dispositivo VPN locale che si trova dietro ExpressRoute. È anche possibile includere un nome di dominio completo. Ad esempio, *something.contoso.com*. Il nome FQDN deve essere risolvibile dal gateway VPN. Questo è possibile se il server DNS che ospita questo nome FQDN è raggiungibile tramite Internet. L'indirizzo IP ha la precedenza quando vengono specificati sia l'indirizzo IP che il nome FQDN.

      >[!NOTE]
      >
      >* È supportato un unico indirizzo IPv4 per ogni nome FQDN. Se il nome FQDN deve essere risolto in più indirizzo IP, il gateway VPN preleva il primo indirizzo IP4 dall'elenco. Gli indirizzi IPv6 non sono supportati al momento.
      >
      >* Il gateway VPN mantiene una cache DNS che viene aggiornata ogni 5 minuti. Il gateway tenta di risolvere i nomi FQDN solo per i tunnel disconnessi. Anche una reimpostazione del gateway o una modifica di configurazione può attivare la risoluzione del nome FQDN.
      >
5. Per eliminare o aggiungere altri collegamenti, è possibile utilizzare la casella di controllo. Sono supportati quattro collegamenti per sito VPN. Se, ad esempio, si dispone di quattro provider di servizi Internet (ISP) nella posizione del ramo, è possibile creare quattro collegamenti, uno per ogni ISP, e fornire le informazioni per ogni collegamento.
6. Al termine della compilazione dei campi, selezionare **Rivedi + crea** per verificare e creare il sito.
7. Visualizzare lo stato della pagina Siti VPN. Il sito passerà a **Connessione necessaria** perché il sito non è ancora stato collegato all'hub.
