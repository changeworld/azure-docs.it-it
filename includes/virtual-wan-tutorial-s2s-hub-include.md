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
ms.openlocfilehash: f469664c716ecef6b82de2befa40b33f253e229f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627710"
---
1. Individuare la rete WAN virtuale creata. Nella sezione **connettività** della pagina WAN virtuale selezionare **Hub**.
2. Nella pagina **Hub** selezionare **+ nuovo hub** per aprire la pagina **Crea hub virtuale** .

    ![Screenshot del riquadro Crea hub virtuale con la scheda Informazioni di base selezionata.](./media/virtual-wan-tutorial-hub-include/basics.png "Nozioni di base")
3. Nella scheda **Generale** della pagina **Crea hub virtuale** completare i campi seguenti:

   * **Region** (precedentemente denominata percorso)
   * **Nome**
   * **Spazio degli indirizzi privato dell'hub** : lo spazio degli indirizzi minimo è/24 per la creazione di un hub. Se si usa un valore compreso nell'intervallo da/25 a/32, verrà generato un errore durante la creazione. Non è necessario pianificare in modo esplicito lo spazio degli indirizzi della subnet per i servizi nell'hub virtuale. Poiché la rete WAN virtuale di Azure è un servizio gestito, crea le subnet appropriate nell'hub virtuale per i diversi gateway/Servizi, ad esempio gateway VPN, gateway ExpressRoute, gateway da punto a sito VPN utente, firewall, routing e così via.
4. Selezionare **Avanti: Da sito a sito**.

    ![Screenshot del riquadro Crea hub virtuale con l'opzione Da sito a sito selezionata.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Da sito a sito")

5. Nella scheda **Da sito a sito** completare i campi seguenti:

   * Scegliere **Sì** per creare una VPN da sito a sito.
   * Il campo numero AS non può essere modificato.
   * Nell'elenco a discesa selezionare il valore **Unità di scala gateway**. L'unità di scala consente di scegliere la velocità effettiva aggregata del gateway VPN creato nell'hub virtuale a cui si collegano i siti. Se si sceglie l'unità di scala 1 = 500 Mbps, verranno create due istanze per la ridondanza, ciascuna con una velocità effettiva massima di 500 Mbps. Se, ad esempio, si hanno cinque rami, ognuno dei quali contribuisce per 10 Mbps, sarà necessaria una velocità aggregata di 50 Mbps all'estremità finale. La capacità aggregata del gateway VPN di Azure deve essere pianificata dopo aver valutato la capacità necessaria per supportare il numero di rami che confluiscono nell'hub.
6. Selezionare **Rivedi e crea** per convalidare la selezione.
7. Fare clic su **Crea** per creare l'hub. Dopo 30 minuti, fare clic su **Aggiorna** per visualizzare l'hub nella pagina **Hub**. Selezionare **Vai alla risorsa** per passare alla risorsa.
