---
title: includere file
description: includere file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/11/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 708baa83ca919adcc374be36c229ce3ff30da384
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362943"
---
1. Nella pagina del portale per la rete WAN virtuale, nella sezione **Connettività** selezionare **Siti VPN** per aprire la pagina Siti VPN.
1. Nella pagina **Siti VPN** fare clic su **+Crea sito**.
1. Nella pagina **Crea sito WAN**, nella scheda **Nozioni di base**, compilare i campi seguenti:

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="Scheda Informazioni di base" lightbox="./media/virtual-wan-tutorial-site-include/site-basics.png":::

    * **Regione** - Precedentemente denominata posizione. Si tratta del percorso in cui si vuole creare la risorsa del sito.
    * **Nome** - Nome che si vuole usare per fare riferimento al sito locale.
    * **Fornitore del dispositivo** - Nome del fornitore del dispositivo VPN (ad esempio: Citrix, Cisco, Barracuda). L'aggiunta del fornitore del dispositivo può aiutare il team di Azure a comprendere meglio l'ambiente per aggiungere altre possibilità di ottimizzazione in futuro o per semplificare la risoluzione dei problemi.
    * **Spazio indirizzi privato** - Spazio di indirizzi IP nel sito locale. Il traffico destinato a questo spazio di indirizzi viene indirizzato al sito locale. Questa operazione è necessaria quando BGP non è abilitato per il sito.
    
      >[!NOTE]
      >Se si modifica lo spazio di indirizzi dopo aver creato il sito, ad esempio si aggiunge un altro spazio di indirizzi, l'aggiornamento delle route effettive può richiedere 8-10 minuti mentre i componenti vengono ricreati.
      >
1. Selezionare **Collegamenti** per aggiungere informazioni sui collegamenti fisici al ramo. Se è presente un dispositivo CPE del partner WAN virtuale, verificarlo per verificare se queste informazioni vengono scambiate con Azure nell'ambito del caricamento delle informazioni sui Branch configurate dai rispettivi sistemi.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-links.png" alt-text="Scheda collegamenti" lightbox="./media/virtual-wan-tutorial-site-include/site-links.png":::

   * **Nome collegamento** - Nome che si desidera fornire per il collegamento fisico al sito VPN. Esempio: mylink1.
   * **Velocità del collegamento** : velocità del dispositivo VPN nella posizione del ramo. Esempio: 50, che indica 50 Mbps è la velocità del dispositivo VPN nella sede del ramo.
   * **Nome provider di collegamento** : nome del collegamento fisico nel sito VPN. Esempio: ATT, Verizon.
   * **Collega indirizzo IP/FQDN** -indirizzo IP pubblico del dispositivo locale usando questo collegamento. Facoltativamente, è possibile specificare l'indirizzo IP privato del dispositivo VPN locale che si trova dietro ExpressRoute. È anche possibile includere un nome di dominio completo. Ad esempio, *something.contoso.com*. Il nome FQDN deve essere risolvibile dal gateway VPN. Questo è possibile se il server DNS che ospita questo nome FQDN è raggiungibile tramite Internet. L'indirizzo IP ha la precedenza quando vengono specificati sia l'indirizzo IP che il nome FQDN.

     >[!NOTE]
     >
     >* È supportato un unico indirizzo IPv4 per ogni nome FQDN. Se il nome FQDN deve essere risolto in più indirizzo IP, il gateway VPN preleva il primo indirizzo IP4 dall'elenco. Gli indirizzi IPv6 non sono supportati al momento.
     >
     >* Il gateway VPN mantiene una cache DNS che viene aggiornata ogni 5 minuti. Il gateway tenta di risolvere i nomi FQDN solo per i tunnel disconnessi. Anche una reimpostazione del gateway o una modifica di configurazione può attivare la risoluzione del nome FQDN.
     >
   * **Collegare Border Gateway Protocol** : la configurazione di BGP in un collegamento WAN virtuale equivale alla configurazione di BGP in una VPN del gateway di rete virtuale di Azure. L'indirizzo del peer BGP locale non deve essere uguale all'indirizzo IP pubblico del dispositivo VPN né allo spazio di indirizzi della rete virtuale del sito VPN. Usare un indirizzo IP diverso nel dispositivo VPN per il peer BGP. Può trattarsi di un indirizzo assegnato all'interfaccia di loopback nel dispositivo. Specificare questo indirizzo nel sito VPN corrispondente che rappresenta la posizione.  Per i prerequisiti di BGP, vedere [Informazioni su BGP con i gateway VPN di Azure](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). È sempre possibile modificare una connessione di collegamento VPN per aggiornare i parametri BGP (indirizzo IP del peering sul collegamento e come #).
1. È possibile aggiungere o eliminare altri collegamenti. Sono supportati quattro collegamenti per sito VPN. Se, ad esempio, si dispone di quattro provider di servizi Internet (ISP) nella posizione del ramo, è possibile creare quattro collegamenti, uno per ogni ISP, e fornire le informazioni per ogni collegamento.
1. Al termine della compilazione dei campi, selezionare **Rivedi + crea** per verificare e creare il sito.
1. Passare all'hub virtuale desiderato e deselezionare **associazione hub** per connettere il sito VPN all'hub.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/connect.png" alt-text="Connetti a questo hub" lightbox="./media/virtual-wan-tutorial-site-include/connect.png":::
