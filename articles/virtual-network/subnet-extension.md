---
title: Estensione subnet in Azure | Microsoft Docs
description: Informazioni sull'estensione subnet in Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: e39859e3cc4d28ac4b1456fcdaec65ecfb9c7f31
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728213"
---
# <a name="subnet-extension"></a>Estensione della subnet
La migrazione del carico di lavoro al cloud pubblico richiede un'attenta pianificazione e coordinamento. Una delle considerazioni principali può essere la possibilità di mantenere gli indirizzi IP. Ciò può essere importante soprattutto se le applicazioni hanno una dipendenza dell'indirizzo IP o se si hanno requisiti di conformità per l'uso di indirizzi IP specifici. Rete virtuale di Azure risolve automaticamente questo problema consentendo di creare reti virtuali e subnet usando un intervallo di indirizzi IP di propria scelta.

Le migrazioni possono risultare un po' complesse quando il requisito precedente è associato a un requisito aggiuntivo per mantenere alcune applicazioni in locale. In una situazione di questo tipo, sarà necessario suddividere le applicazioni tra Azure e in locale, senza rinumerare gli indirizzi IP su entrambi i lati. Inoltre, è necessario consentire alle applicazioni di comunicare come se fossero nella stessa rete.

Una soluzione al problema precedente è l'estensione della subnet. L'estensione di una rete consente alle applicazioni di parlare sullo stesso dominio broadcast quando esistono in posizioni fisiche diverse, eliminando la necessità di riarchitezionare la topologia di rete. 

Anche se l'estensione della rete non è una procedura consigliata in generale, i casi d'uso seguenti possono renderlo necessario.

- **Migrazione in più fasi:** lo scenario più comune è che si vuole eseguire la migrazione in più fasi. Si vuole prima portare alcune applicazioni e nel tempo eseguire la migrazione di altre applicazioni ad Azure.
- **Latenza:** i requisiti di bassa latenza possono essere un altro motivo per mantenere alcune applicazioni in locale per assicurarsi che siano il più vicino possibile al data center.
- **Conformità:** un altro caso d'uso è che potrebbero essere necessari requisiti di conformità per mantenere alcune applicazioni in locale.
 
> [!NOTE] 
> Non è consigliabile estendere le subnet a meno che non sia necessario. Nei casi in cui si estendono le subnet, è consigliabile provare a renderla un passaggio intermedio. Con il tempo, è consigliabile provare a numerare nuovamente le applicazioni nella rete locale ed eseguirne la migrazione ad Azure.

Nella sezione successiva verrà illustrato come estendere le subnet in Azure.


## <a name="extend-your-subnet-to-azure"></a>Estendere la subnet ad Azure
 È possibile estendere le subnet locali ad Azure usando una soluzione basata sulla rete di sovrapposizione di livello 3. La maggior parte delle soluzioni usa una tecnologia di sovrapposizione, ad esempio VXLAN, per estendere la rete di livello 2 usando una rete di sovrapposizione di livello 3. Il diagramma seguente mostra una soluzione generalizzata. In questa soluzione, la stessa subnet esiste su entrambi i lati, ad esempio Azure e in locale. 

![Esempio di estensione subnet](./media/subnet-extension/subnet-extension.png)

Gli indirizzi IP della subnet vengono assegnati alle macchine virtuali in Azure e in locale. Sia Azure che l'ambiente locale hanno un'NVA inserita nelle proprie reti. Quando una macchina virtuale in Azure prova a parlare con una macchina virtuale nella rete locale, l'NVA di Azure acquisisce il pacchetto, lo incapsula e lo invia tramite VPN/Express Route alla rete locale. L'NVA locale riceve il pacchetto, lo incapsula e lo inoltra al destinatario previsto nella propria rete. Il traffico di ritorno usa un percorso e una logica simili.

Nell'esempio precedente, l'NVA di Azure e l'NVA locale comunicano e apprendono informazioni sugli indirizzi IP l'uno dietro l'altro. Le reti più complesse possono anche avere un servizio di mapping, che gestisce il mapping tra le appliance virtuali di rete e gli indirizzi IP dietro di esse. Quando un'NVA riceve un pacchetto, esegue una query sul servizio di mapping per individuare l'indirizzo dell'NVA che contiene l'indirizzo IP di destinazione.

Nella sezione successiva sono disponibili informazioni dettagliate sulle soluzioni di estensione subnet testate in Azure.

## <a name="next-steps"></a>Passaggi successivi 
[Estendere le subnet locali in Azure usando la rete estesa di Azure.](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-extended-network)